# SQL

Basic Stuff - selecting rows and inserting rows.

INSERT INTO movies (id, name) VALUES (1, 'Some Movie');

## Updating Rows

UPDATE movies
SET name = 'Something Else'
WHERE id = 1;

## Deleting Rows

DELETE FROM movies
WHERE id = 1;

## Creating Tables and Databases

DROP DATABASE mything;
CREATE DATABASE mything;

USE mything;

CREATE TABLE stuff (
  id int,
  title varchar(50),
  genre varchar(15),
  duration int
);

## Adding and removing columns

ALTER TABLE stuff
ADD COLUMN newcol int;

ALTER TABLE stuff
DROP COLUMN newcol;

 




