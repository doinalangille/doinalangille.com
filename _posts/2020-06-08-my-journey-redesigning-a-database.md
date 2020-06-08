---
layout: post
title: My Journey Redesigning a Database
subtitle: Redesign a relational database
image: /img/post_img.jpg
tags: [database, relational, PostgreSQL, SQL, Groa]
comments: true
---

Every website has a database in its "backpack". Some of them are based on relational databases, others on non-relational databases, depending on the nature and the purpose of the website. I had the chance to work on an amazing recommendation engine - [Gróa](https://groa.us/), and my first task was to analyze the database. But first, let's have a general idea what is this project about.

# About Gróa

Gróa is a media personalization hub of unbiased and accurate movie recommendations. It is based on user ratings and user reviews, and it provides information such as description, trailer, and the platforms where the movie can be found. The recommendation models are trained on ratings and reviews from different major platforms, such as [IMDB](https://www.imdb.com/) or [Letterboxd](https://letterboxd.com/). The recommended list of movies is different for every user, because it is based on personal preferences.

# Why SQL?

It is extremely important to decide from the begining what kind of data we are working with. Gróa uses machine learning models which require data integrity. So why SQL?

First of all, the platform needs to ensure ACID compliance (Atomicity, Consistency, Isolation, Durability). It is also working with complex queries which aim to retrieve and present data. Lastly, the platform doesn't work with too many data types, and a relational database is a perfect fit in this case.

# The issues with the previous database

Groa gives movie recommendations based on user ratings and reviews. The ratings and the reviews are added in the database either from Letterboxd or IMDb. A user can also add his ratings directly on the platform. The previous database schema is presented below.

![alt text](/img/previous_schema.PNG "Previous Schema")

There are several reasons why the database had to be reconfigured: 

**1. Redundancy**

  * `letterboxd_ratings` table contains the same information already presented in `letterboxd_movies` table. Idem `imdb_ratings` contains the information already presented in `imdb_movies` table;
  * Two tables contain information about movies: `letterboxd_movies` and `imdb_movies`;
  * Two tables contain movie reviews: `letterboxd_reviews` and `groa_reviews`;
  * Two tables contain user reviews: `user_letterboxd_reviews` and `user_groa_reviews`;
  * Three tables contain user ratings: `user_letterboxd_ratings`, `user_groa_ratings`, and `user_imdb_ratings`;
  * Three tables contain user watchlist: `user_letterboxd_watchlist`, `user_groa_watchlist`, and `user_imdb_watchlist`

**2. Values are not atomic**

  The recommendations are stored in the `recommendation_json` column of the `recommendations` table. The column's data type is JSON, and it stores information about title, year, genres, poster_url, as well as model metrics.

**3. Entity integrity violated**

  `user_groa_willnotwatchlist` does not have a primary key or a unique constraint, allowing the existence of duplicate rows.

**4. Missing relationships**

  All tables with the `user_%` format contain information related to movies (`name` and `year`), but there is no attribute to refer to the tables containing detailed information about those movies.

# The process of reconfiguration

The process of reconfiguring the database implies several steps:

1. Delete tables `letterboxd_ratings` and `imdb_ratings`;
2. Merge `letterboxd_movies` and `imdb_movies` in a new table: `movies`;
3. Merge `letterboxd_reviews` and `imdb_reviews` in a new table - `movie_reviews`;
4. The structure of the table `users` remains the same, except for the primary key, which was renamed from `id` to `user_id`;
5. The data from `user_letterboxd_watched` was imported into the new table `user_watched`, where a movie_id is assigned to each row, instead of `name` and `year`;
6. The data from `user_letterboxd_watchlist`, `user_groa_watchlist`, and `user_imdb_watchlist` is stored into a new table called `user_watchlist`. Each row has a movie_id assigned, based on the name and year of the movie;
7. The table `user_groa_willnotwatchlist` becomes `user_willnotwatchlist`, where each row has a movie_id assigned, based on the name and year of the movie;
8. The data from `user_letterboxd_ratings`, `user_groa_ratings`, and `user_imdb_ratings` is stored into a new table called `user_ratings`. Each row has a movie_id assigned, based on the name and year of the movie;
9. The data from `user_letterboxd_reviews` and `user_groa_reviews` is stored into a new table called `user_reviews`. Each row has a movie_id assigned, based on the name and year of the movie;
10. The `recommendation_json` column of the `recommendations` table is transformed into a separate table, storing only the `movie_id` and the metrics of the model.
11. Apply sequences to autogenerate the following columns:

| Table                 | Column            |
| --------------------- | ----------------- |
| movie_reviews         | review_id         |
| user_ratings          | rating_id         |
| user_reviews          | review_id         |
| user_watchlist        | id                |
| users                 | user_id           |
| user_willnotwatchlist | id                |
| recommendations       | recommendation_id |

# The new database

The following picture represents the new database schema:

![alt text](/img/current_schema.PNG "Current Schema")

As a result of reconfiguring the database, the new one has atomic values, non-redundand data, and it follows the entity integrity and referential integrity.

