# Práctica 4. Modelo Relacional. Vistas y disparadores Tarea
### Autores: Alba Morales Martín y Ruyman 

## 1. Tablas, vistas y secuencias
* Listado de **tablas** ```\dt```:
````
             List of relations
 Schema |     Name      | Type  |  Owner   
--------+---------------+-------+----------
 public | actor         | table | postgres
 public | address       | table | postgres
 public | category      | table | postgres
 public | city          | table | postgres
 public | country       | table | postgres
 public | customer      | table | postgres
 public | film          | table | postgres
 public | film_actor    | table | postgres
 public | film_category | table | postgres
 public | inventory     | table | postgres
 public | language      | table | postgres
 public | payment       | table | postgres
 public | rental        | table | postgres
 public | staff         | table | postgres
 public | store         | table | postgres
````
* Listado de **vistas** ```\dv```:
````
Did not find any relations.
````
* Listado de **secuencias** ```\ds```:
````
Schema |            Name            |   Type   |  Owner   
--------+----------------------------+----------+----------
 public | actor_actor_id_seq         | sequence | postgres
 public | address_address_id_seq     | sequence | postgres
 public | category_category_id_seq   | sequence | postgres
 public | city_city_id_seq           | sequence | postgres
 public | country_country_id_seq     | sequence | postgres
 public | customer_customer_id_seq   | sequence | postgres
 public | film_film_id_seq           | sequence | postgres
 public | inventory_inventory_id_seq | sequence | postgres
 public | language_language_id_seq   | sequence | postgres
 public | payment_payment_id_seq     | sequence | postgres
 public | rental_rental_id_seq       | sequence | postgres
 public | staff_staff_id_seq         | sequence | postgres
 public | store_store_id_seq         | sequence | postgres
````

## 2. Tablas principales, atributos y algunos registros. ```SELECT * FROM table_name;```
### Actor (actor)
````
 actor_id | first_name  |  last_name   |      last_update       
----------+-------------+--------------+------------------------
        1 | Penelope    | Guiness      | 2013-05-26 14:47:57.62
        2 | Nick        | Wahlberg     | 2013-05-26 14:47:57.62
        3 | Ed          | Chase        | 2013-05-26 14:47:57.62
        4 | Jennifer    | Davis        | 2013-05-26 14:47:57.62
        5 | Johnny      | Lollobrigida | 2013-05-26 14:47:57.62
        6 | Bette       | Nicholson    | 2013-05-26 14:47:57.62
        7 | Grace       | Mostel       | 2013-05-26 14:47:57.62
        8 | Matthew     | Johansson    | 2013-05-26 14:47:57.62
        9 | Joe         | Swank        | 2013-05-26 14:47:57.62
       10 | Christian   | Gable        | 2013-05-26 14:47:57.62
````
### Categoría (category)
````
 category_id |    name     |     last_update     
-------------+-------------+---------------------
           1 | Action      | 2006-02-15 09:46:27
           2 | Animation   | 2006-02-15 09:46:27
           3 | Children    | 2006-02-15 09:46:27
           4 | Classics    | 2006-02-15 09:46:27
           5 | Comedy      | 2006-02-15 09:46:27
           6 | Documentary | 2006-02-15 09:46:27
           7 | Drama       | 2006-02-15 09:46:27
           8 | Family      | 2006-02-15 09:46:27
           9 | Foreign     | 2006-02-15 09:46:27
          10 | Games       | 2006-02-15 09:46:27
````
### Cliente (Customer)
````
 customer_id | store_id | first_name  |  last_name   |                  email                   | address_id | activebool | create_date |       last_update       | active 
-------------+----------+-------------+--------------+------------------------------------------+------------+------------+-------------+-------------------------+--------
         524 |        1 | Jared       | Ely          | jared.ely@sakilacustomer.org             |        530 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           1 |        1 | Mary        | Smith        | mary.smith@sakilacustomer.org            |          5 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           2 |        1 | Patricia    | Johnson      | patricia.johnson@sakilacustomer.org      |          6 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           3 |        1 | Linda       | Williams     | linda.williams@sakilacustomer.org        |          7 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           4 |        2 | Barbara     | Jones        | barbara.jones@sakilacustomer.org         |          8 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           5 |        1 | Elizabeth   | Brown        | elizabeth.brown@sakilacustomer.org       |          9 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           6 |        2 | Jennifer    | Davis        | jennifer.davis@sakilacustomer.org        |         10 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           7 |        1 | Maria       | Miller       | maria.miller@sakilacustomer.org          |         11 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           8 |        2 | Susan       | Wilson       | susan.wilson@sakilacustomer.org          |         12 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
           9 |        2 | Margaret    | Moore        | margaret.moore@sakilacustomer.org        |         13 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
          10 |        1 | Dorothy     | Taylor       | dorothy.taylor@sakilacustomer.org        |         14 | t          | 2006-02-14  | 2013-05-26 14:49:45.738 |      1
````
### Película (film)
````                         

````







