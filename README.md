# Práctica 4. Modelo Relacional. Vistas y disparadores Tarea
### Autores: Alba Morales Martín y Ruymán García Martín

# 1. Restauración de la base de datos ```AlquilerPractica.tar```, con: ```pg_restore -d db_prct04 -U postgres -h localhost -p 5432 AlquilerPractica.tar```

## 2. Tablas, vistas y secuencias
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
Schema  |            Name            |   Type   |  Owner   
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

## 3. Tablas principales, atributos (campos) y algunas características: claves (primarias) y ajenas```\d table_name```
### Actor (actor)
````
   Column    |            Type             | Collation | Nullable |                 Default                 
-------------+-----------------------------+-----------+----------+-----------------------------------------
 actor_id    | integer                     |           | not null | nextval('actor_actor_id_seq'::regclass)
 first_name  | character varying(45)       |           | not null | 
 last_name   | character varying(45)       |           | not null | 
 last_update | timestamp without time zone |           | not null | now()
Indexes:
    "actor_pkey" PRIMARY KEY, btree (actor_id)
    "idx_actor_last_name" btree (last_name)
Referenced by:
    TABLE "film_actor" CONSTRAINT "film_actor_actor_id_fkey" FOREIGN KEY (actor_id) REFERENCES actor(actor_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Categoría (category)
````
   Column    |            Type             | Collation | Nullable |                    Default                    
-------------+-----------------------------+-----------+----------+-----------------------------------------------
 category_id | integer                     |           | not null | nextval('category_category_id_seq'::regclass)
 name        | character varying(25)       |           | not null | 
 last_update | timestamp without time zone |           | not null | now()
Indexes:
    "category_pkey" PRIMARY KEY, btree (category_id)
Referenced by:
    TABLE "film_category" CONSTRAINT "film_category_category_id_fkey" FOREIGN KEY (category_id) REFERENCES category(category_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Película (film)
````                         
      Column      |            Type             | Collation | Nullable |                Default                
------------------+-----------------------------+-----------+----------+---------------------------------------
 film_id          | integer                     |           | not null | nextval('film_film_id_seq'::regclass)
 title            | character varying(255)      |           | not null | 
 description      | text                        |           |          | 
 release_year     | year                        |           |          | 
 language_id      | smallint                    |           | not null | 
 rental_duration  | smallint                    |           | not null | 3
 rental_rate      | numeric(4,2)                |           | not null | 4.99
 length           | smallint                    |           |          | 
 replacement_cost | numeric(5,2)                |           | not null | 19.99
 rating           | mpaa_rating                 |           |          | 'G'::mpaa_rating
 last_update      | timestamp without time zone |           | not null | now()
 special_features | text[]                      |           |          | 
 fulltext         | tsvector                    |           | not null | 
Indexes:
    "film_pkey" PRIMARY KEY, btree (film_id)
    "film_fulltext_idx" gist (fulltext)
    "idx_fk_language_id" btree (language_id)
    "idx_title" btree (title)
Foreign-key constraints:
    "film_language_id_fkey" FOREIGN KEY (language_id) REFERENCES language(language_id) ON UPDATE CASCADE ON DELETE RESTRICT
Referenced by:
    TABLE "film_actor" CONSTRAINT "film_actor_film_id_fkey" FOREIGN KEY (film_id) REFERENCES film(film_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "film_category" CONSTRAINT "film_category_film_id_fkey" FOREIGN KEY (film_id) REFERENCES film(film_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "inventory" CONSTRAINT "inventory_film_id_fkey" FOREIGN KEY (film_id) REFERENCES film(film_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Lengua (language)
````
   Column    |            Type             | Collation | Nullable |                    Default                    
-------------+-----------------------------+-----------+----------+-----------------------------------------------
 language_id | integer                     |           | not null | nextval('language_language_id_seq'::regclass)
 name        | character(20)               |           | not null | 
 last_update | timestamp without time zone |           | not null | now()
Indexes:
    "language_pkey" PRIMARY KEY, btree (language_id)
Referenced by:
    TABLE "film" CONSTRAINT "film_language_id_fkey" FOREIGN KEY (language_id) REFERENCES language(language_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Inventario (inventory)
````
    Column    |            Type             | Collation | Nullable |                     Default                     
--------------+-----------------------------+-----------+----------+-------------------------------------------------
 inventory_id | integer                     |           | not null | nextval('inventory_inventory_id_seq'::regclass)
 film_id      | smallint                    |           | not null | 
 store_id     | smallint                    |           | not null | 
 last_update  | timestamp without time zone |           | not null | now()
Indexes:
    "inventory_pkey" PRIMARY KEY, btree (inventory_id)
    "idx_store_id_film_id" btree (store_id, film_id)
Foreign-key constraints:
    "inventory_film_id_fkey" FOREIGN KEY (film_id) REFERENCES film(film_id) ON UPDATE CASCADE ON DELETE RESTRICT
Referenced by:
    TABLE "rental" CONSTRAINT "rental_inventory_id_fkey" FOREIGN KEY (inventory_id) REFERENCES inventory(inventory_id) ON UPDATE CASCADE ON DELETE RESTRICT
````

### Pago (payment)
````
   Column     |            Type             | Collation | Nullable |                   Default                   
--------------+-----------------------------+-----------+----------+---------------------------------------------
 payment_id   | integer                     |           | not null | nextval('payment_payment_id_seq'::regclass)
 customer_id  | smallint                    |           | not null | 
 staff_id     | smallint                    |           | not null | 
 rental_id    | integer                     |           | not null | 
 amount       | numeric(5,2)                |           | not null | 
 payment_date | timestamp without time zone |           | not null | 
Indexes:
    "payment_pkey" PRIMARY KEY, btree (payment_id)
    "idx_fk_customer_id" btree (customer_id)
    "idx_fk_rental_id" btree (rental_id)
    "idx_fk_staff_id" btree (staff_id)
Foreign-key constraints:
    "payment_customer_id_fkey" FOREIGN KEY (customer_id) REFERENCES customer(customer_id) ON UPDATE CASCADE ON DELETE RESTRICT
    "payment_rental_id_fkey" FOREIGN KEY (rental_id) REFERENCES rental(rental_id) ON UPDATE CASCADE ON DELETE SET NULL
    "payment_staff_id_fkey" FOREIGN KEY (staff_id) REFERENCES staff(staff_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Alquiler (rental)
````
   Column     |            Type             | Collation | Nullable |                  Default                  
--------------+-----------------------------+-----------+----------+-------------------------------------------
 rental_id    | integer                     |           | not null | nextval('rental_rental_id_seq'::regclass)
 rental_date  | timestamp without time zone |           | not null | 
 inventory_id | integer                     |           | not null | 
 customer_id  | smallint                    |           | not null | 
 return_date  | timestamp without time zone |           |          | 
 staff_id     | smallint                    |           | not null | 
 last_update  | timestamp without time zone |           | not null | now()
Indexes:
    "rental_pkey" PRIMARY KEY, btree (rental_id)
    "idx_fk_inventory_id" btree (inventory_id)
    "idx_unq_rental_rental_date_inventory_id_customer_id" UNIQUE, btree (rental_date, inventory_id, customer_id)
Foreign-key constraints:
    "rental_customer_id_fkey" FOREIGN KEY (customer_id) REFERENCES customer(customer_id) ON UPDATE CASCADE ON DELETE RESTRICT
    "rental_inventory_id_fkey" FOREIGN KEY (inventory_id) REFERENCES inventory(inventory_id) ON UPDATE CASCADE ON DELETE RESTRICT
    "rental_staff_id_key" FOREIGN KEY (staff_id) REFERENCES staff(staff_id)
Referenced by:
    TABLE "payment" CONSTRAINT "payment_rental_id_fkey" FOREIGN KEY (rental_id) REFERENCES rental(rental_id) ON UPDATE CASCADE ON DELETE SET NULL
````
### Cliente (Customer)
````
    Column   |            Type            | Collation  | Nullable |                    Default                    
-------------+-----------------------------+-----------+----------+-----------------------------------------------
 customer_id | integer                     |           | not null | nextval('customer_customer_id_seq'::regclass)
 store_id    | smallint                    |           | not null | 
 first_name  | character varying(45)       |           | not null | 
 last_name   | character varying(45)       |           | not null | 
 email       | character varying(50)       |           |          | 
 address_id  | smallint                    |           | not null | 
 activebool  | boolean                     |           | not null | true
 create_date | date                        |           | not null | 'now'::text::date
 last_update | timestamp without time zone |           |          | now()
 active      | integer                     |           |          | 
Indexes:
    "customer_pkey" PRIMARY KEY, btree (customer_id)
    "idx_fk_address_id" btree (address_id)
    "idx_fk_store_id" btree (store_id)
    "idx_last_name" btree (last_name)
Foreign-key constraints:
    "customer_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
Referenced by:
    TABLE "payment" CONSTRAINT "payment_customer_id_fkey" FOREIGN KEY (customer_id) REFERENCES customer(customer_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "rental" CONSTRAINT "rental_customer_id_fkey" FOREIGN KEY (customer_id) REFERENCES customer(customer_id) ON UPDATE CASCADE ON DELETE RESTRICT
````


### Tienda (store)
````
      Column      |            Type             | Collation | Nullable |                 Default                 
------------------+-----------------------------+-----------+----------+-----------------------------------------
 store_id         | integer                     |           | not null | nextval('store_store_id_seq'::regclass)
 manager_staff_id | smallint                    |           | not null | 
 address_id       | smallint                    |           | not null | 
 last_update      | timestamp without time zone |           | not null | now()
Indexes:
    "store_pkey" PRIMARY KEY, btree (store_id)
    "idx_unq_manager_staff_id" UNIQUE, btree (manager_staff_id)
Foreign-key constraints:
    "store_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
    "store_manager_staff_id_fkey" FOREIGN KEY (manager_staff_id) REFERENCES staff(staff_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Empleada (staff)
````
   Column    |            Type             | Collation | Nullable |                 Default                 
-------------+-----------------------------+-----------+----------+-----------------------------------------
 staff_id    | integer                     |           | not null | nextval('staff_staff_id_seq'::regclass)
 first_name  | character varying(45)       |           | not null | 
 last_name   | character varying(45)       |           | not null | 
 address_id  | smallint                    |           | not null | 
 email       | character varying(50)       |           |          | 
 store_id    | smallint                    |           | not null | 
 active      | boolean                     |           | not null | true
 username    | character varying(16)       |           | not null | 
 password    | character varying(40)       |           |          | 
 last_update | timestamp without time zone |           | not null | now()
 picture     | bytea                       |           |          | 
Indexes:
    "staff_pkey" PRIMARY KEY, btree (staff_id)
Foreign-key constraints:
    "staff_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
Referenced by:
    TABLE "payment" CONSTRAINT "payment_staff_id_fkey" FOREIGN KEY (staff_id) REFERENCES staff(staff_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "rental" CONSTRAINT "rental_staff_id_key" FOREIGN KEY (staff_id) REFERENCES staff(staff_id)
    TABLE "store" CONSTRAINT "store_manager_staff_id_fkey" FOREIGN KEY (manager_staff_id) REFERENCES staff(staff_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
### Dirección (Address)
````
   Column    |            Type             | Collation | Nullable |                   Default                   
-------------+-----------------------------+-----------+----------+---------------------------------------------
 address_id  | integer                     |           | not null | nextval('address_address_id_seq'::regclass)
 address     | character varying(50)       |           | not null | 
 address2    | character varying(50)       |           |          | 
 district    | character varying(20)       |           | not null | 
 city_id     | smallint                    |           | not null | 
 postal_code | character varying(10)       |           |          | 
 phone       | character varying(20)       |           | not null | 
 last_update | timestamp without time zone |           | not null | now()
Indexes:
    "address_pkey" PRIMARY KEY, btree (address_id)
    "idx_fk_city_id" btree (city_id)
Foreign-key constraints:
    "fk_address_city" FOREIGN KEY (city_id) REFERENCES city(city_id)
Referenced by:
    TABLE "customer" CONSTRAINT "customer_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "staff" CONSTRAINT "staff_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
    TABLE "store" CONSTRAINT "store_address_id_fkey" FOREIGN KEY (address_id) REFERENCES address(address_id) ON UPDATE CASCADE ON DELETE RESTRICT
````
## 4. Consultas

* A continuación crearemos las siguientes consultas que se solicitan:

* ### Obtenga las ventas totales por categoría de películas ordenadas descendentemente.
````
SELECT 
    category.name AS category,
    COUNT(payment.payment_id) AS total_sales
FROM 
    payment
JOIN 
    rental 
	ON payment.rental_id = rental.rental_id
JOIN 
    inventory 
	ON rental.inventory_id = inventory.inventory_id
JOIN 
    film_category 
	ON inventory.film_id = film_category.film_id
JOIN 
    category 
	ON film_category.category_id = category.category_id
GROUP BY 
    category.name
ORDER BY 
    total_sales DESC;
````

El resultado por pantalla de la consulta sería el siguiente:

````
  category   | total_sales 
-------------+-------------
 Sports      |        1081
 Animation   |        1065
 Action      |        1013
 Sci-Fi      |         998
 Family      |         988
 Foreign     |         953
 Drama       |         953
 Documentary |         937
 Games       |         884
 New         |         864
 Children    |         861
 Classics    |         860
 Comedy      |         851
 Horror      |         773
 Travel      |         765
 Music       |         750

(16 rows)
````

* ### Obtenga las ventas totales por tienda, donde se refleje la ciudad, el país (concatenar la ciudad y el país empleando como separador la “,”), y el encargado.

````
SELECT 
    store.store_id AS store_id,
    CONCAT(city.city, ', ', country.country) AS location,
    CONCAT(manager.first_name, ' ', manager.last_name) AS manager_name,
    COUNT(payment.payment_id) AS total_sales
FROM 
    store
JOIN 
    address 
	ON store.address_id = address.address_id
JOIN 
    city 
	ON address.city_id = city.city_id
JOIN 
    country 
	ON city.country_id = country.country_id
JOIN 
    staff AS manager 
	ON store.manager_staff_id = manager.staff_id
JOIN 
    payment 
	ON payment.staff_id = manager.staff_id
GROUP BY 
    store.store_id, location, manager_name
ORDER BY 
    total_sales DESC;
````

El resultado por pantalla de la consulta sería el siguiente:

````
 store_id |       location       | manager_name | total_sales 
----------+----------------------+--------------+-------------
        2 | Woodridge, Australia | Jon Stephens |        7304
        1 | Lethbridge, Canada   | Mike Hillyer |        7292

(2 rows)
````

* ### Obtenga una lista de películas, donde se reflejen el identificador, el título, descripción, categoría, el precio, la duración de la película, clasificación, nombre y apellidos de los actores (puede realizar una concatenación de ambos)

````
SELECT 
    film.film_id AS film_id,
    film.title AS title,
    film.description AS description,
    category.name AS category,
    film.rental_rate AS price,
    film.length AS duration,
    film.rating AS rating,
    STRING_AGG(CONCAT(actor.first_name, ' ', actor.last_name), ', ') AS actors
FROM 
    film
JOIN 
    film_category 
	ON film.film_id = film_category.film_id
JOIN 
    category 
	ON film_category.category_id = category.category_id
JOIN 
    film_actor 
	ON film.film_id = film_actor.film_id
JOIN 
    actor 
	ON film_actor.actor_id = actor.actor_id
GROUP BY 
    film.film_id, category.name
ORDER BY 
    film.title;
````

El resultado por pantalla de la consulta sería el siguiente:

```
| film_id | Title               | Description                                                                                                           | Category     | Price | Duration | Rating | Actors                                                                                                                                         |
|---------|---------------------|-----------------------------------------------------------------------------------------------------------------------|--------------|-------|----------|--------|------------------------------------------------------------------------------------------------------------------------------------------------|
| 1       | Academy Dinosaur    | A Epic Drama of a Feminist And a Mad Scientist who must Battle a Teacher in The Canadian Rockies                      | Documentary  | 0.99  | 86       | PG     | Rock Dukakis, Mary Keitel, Johnny Cage, Penelope Guiness, Sandra Peck, Christian Gable, Oprah Kilmer, Warren Nolte, Lucille Tracy, Mena Temple |
| 2       | Ace Goldfinger      | A Astounding Epistle of a Database Administrator And a Explorer who must Find a Car in Ancient China                  | Horror       | 4.99  | 48       | G      | Minnie Zellweger, Chris Depp, Bob Fawcett, Sean Guiness                                                                                        |
| 3       | Adaptation Holes    | A Astounding Reflection of a Lumberjack And a Car who must Sink a Lumberjack in A Baloon Factory                      | Documentary  | 2.99  | 50       | NC-17  | Cameron Streep, Bob Fawcett, Nick Wahlberg, Ray Johansson, Julianne Dench                                                                      |
| 4       | Affair Prejudice    | A Fanciful Documentary of a Frisbee And a Lumberjack who must Chase a Monkey in A Shark Tank                          | Horror       | 2.99  | 117      | G      | Jodie Degeneres, Kenneth Pesci, Fay Winslet, Oprah Kilmer, Scarlett Damon                                                                      |
| 5       | African Egg         | A Fast-Paced Documentary of a Pastry Chef And a Dentist who must Pursue a Forensic Psychologist in The Gulf of Mexico | Family       | 2.99  | 130      | G      | Dustin Tautou, Matthew Leigh, Gary Phoenix, Matthew Carrey, Thora Temple                                                                       |
| 6       | Agent Truman        | A Intrepid Panorama of a Robot And a Boy who must Escape a Sumo Wrestler in Ancient China                             | Foreign      | 2.99  | 169      | PG     | Warren Nolte, Sandra Kilmer, Jayne Neeson, Morgan Williams, Kirsten Paltrow, Kenneth Hoffman, Reese West                                       |
| 7       | Airplane Sierra     | A Touching Saga of a Hunter And a Butler who must Discover a Butler in A Jet Boat                                     | Comedy       | 4.99  | 62       | PG-13  | Mena Hopper, Jim Mostel, Michael Bolger, Oprah Kilmer, Richard Penn                                                                            |
| 8       | Airport Pollock     | A Epic Tale of a Moose And a Girl who must Confront a Monkey in Ancient India                                         | Horror       | 4.99  | 54       | R      | Lucille Dee, Susan Davis, Fay Kilmer, Gene Willis                                                                                              |
| 9       | Alabama Devil       | A Thoughtful Panorama of a Database Administrator And a Mad Scientist who must Outgun a Mad Scientist in A Jet Boat   | Horror       | 2.99  | 114      | PG-13  | William Hackman, Rip Crawford, Rip Winslet, Greta Keitel, Christian Gable, Mena Temple, Meryl Allen, Warren Nolte, Elvis Marx                  |
| 10      | Aladdin Calendar    | A Action-Packed Tale of a Man And a Lumberjack who must Reach a Feminist in Ancient China                             | Sports       | 4.99  | 63       | NC-17  | Greta Malden, Rock Dukakis, Ray Johansson, Renee Tracy, Val Bolger, Judy Dean, Jada Ryder, Alec Wayne                                          |

```

* ### Obtenga la información de los actores, donde se incluya sus nombres y apellidos, las categorías y sus películas. Los actores deben de estar agrupados y, las categorías y las películas deben estar concatenados por “:” 

````
SELECT 
    actor.actor_id,
    CONCAT(actor.first_name, ' ', actor.last_name) AS actor_name,
    STRING_AGG(category.name || ': ' || film.title, ', ') AS category_films
FROM 
    actor
JOIN 
    film_actor 
	ON actor.actor_id = film_actor.actor_id
JOIN 
    film 
	ON film_actor.film_id = film.film_id
JOIN 
    film_category 
	ON film.film_id = film_category.film_id
JOIN 
    category 
	ON film_category.category_id = category.category_id
GROUP BY 
    actor.actor_id, actor.first_name, actor.last_name
ORDER BY 
    actor_name;

````

El resultado por pantalla de la consulta sería el siguiente:

````

| actor_id | actor_name         | category_films                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|----------|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 71       | Adam Grant         | Sci-Fi: Annie Identity, Foreign: Ballroom Mockingbird, Travel: Disciple Mother, Comedy: Fireball Philadelphia, Family: Gladiator Westward, Games: Glory Tracy, Comedy: Groundhog Uncut, Foreign: Happiness United, Children: Idols Snatchers, Sports: Loser Hustler, Games: Mars Roman, Action: Midnight Westward, Comedy: Operation Operation, Sports: Seabiscuit Punk, Children: Splendor Patton, Classics: Tadpole Park, Children: Twisted Pirates, Games: Wanda Chamber                                                                                                           |
| 132      | Adam Hopper        | Sci-Fi: Blindness Gun, Family: Blood Argonauts, Music: Chamber Italian, Documentary: Clerks Angels, Action: Clueless Bucket, Foreign: Fiction Christmas, Family: Gables Metropolis, Family: Grease Youth, Comedy: Heaven Freedom, New: Loverboy Attacks, Music: Masked Bubble, Action: Mockingbird Hollywood, Children: Noon Papi, Sci-Fi: Open African, Documentary: Princess Giant, Comedy: Saddle Antitrust, New: Sleepy Japanese, Drama: Torque Bound, Classics: Towers Hurricane, Horror: Train Bunch, Sci-Fi: Vacation Boondock, Music: Words Hunter |
| 165      | Al Garland         | Documentary: Bill Others, New: Breakfast Goldfinger, Drama: Chitty Lock, Drama: Dalmations Sweden, Action: Drifter Commandments, Travel: Enough Raging, Action: Glass Dying, Action: Grail Frankenstein, Action: Handicap Boondock, Foreign: Holiday Games, Family: House Dynamite, Drama: Jacket Frisco, Foreign: Muppet Mile, Animation: Oscar Gold, Action: Park Citizen, Animation: Potter Connecticut, Horror: Rock Instinct, Sports: Sense Greek, Sci-Fi: Silverado Goldfinger, Games: Sleuth Orient, Sports: Slipper Fidelity, Family: Splash Gump, Children: Splendor Patton, Foreign: Vision Torque, New: Voice Peach, Classics: Wasteland Divine |
| 173      | Alan Dreyfuss      | Sci-Fi: Badman Dawn, Sci-Fi: Barbarella Streetcar, Music: Birch Antitrust, Family: Blanket Beverly, Games: Bulworth Commandments, Animation: Clash Freddy, Action: Clueless Bucket, Comedy: Crazy Home, Sci-Fi: Divide Monster, Horror: Fidelity Devil, Drama: Greedy Roots, Travel: Haunted Antitrust, Children: Jumping Wrath, Travel: Kick Savannah, Comedy: Lonely Elephant, Family: Maguire Apache, Animation: Massage Image, Documentary: Metal Armageddon, Music: Monster Spartacus, Children: Polish Brooklyn, Family: Rush Goodfellas, Documentary: Sagebrush Clueless, Children: Strangelove Desire, Comedy: Strictly Scarface, Music: Uncut Suicides, Children: Uptown Young, New: Vampire Whale |
| 146      | Albert Johansson   | Music: Alaska Phantom, Foreign: Alley Evolution, Drama: Apollo Teen, Games: Candles Grapes, Sci-Fi: Connecticut Tramp, Children: Crooked Frogmen, Sports: Crusade Honey, Foreign: Dangerous Uptown, Drama: Deceiver Betrayed, Music: Elf Murder, Sci-Fi: Express Lonely, Animation: Fight Jawbreaker, New: Flamingos Connecticut, Sports: Graceland Dynamite, Music: Grosse Wonderful, Animation: Harper Dying, Comedy: Heaven Freedom, Documentary: Homeward Cider, Sports: Honey Ties, Classics: League Hellfighters, Drama: Lebowski Soldiers, Documentary: Metal Armageddon, Games: Monsoon Cause, New: Redemption Comforts, Classics: Right Cranes, Documentary: Road Roxanne, Comedy: Sweden Shining, Horror: Treasure Command, Horror: Undefeated Dalmations, Documentary: Virginian Pluto, Children: Walls Artist, Documentary: Wedding Apollo, Drama: West Lion |
| 125      | Albert Nolte       | Documentary: Bed Highball, Drama: Bright Encounters, Foreign: Brooklyn Desert, Sci-Fi: Camelot Vacation, Family: Confused Candles, Comedy: Crazy Home, Drama: Dalmations Sweden, Children: Doctor Grail, Action: Dragon Squad, Comedy: Flintstones Happiness, Sci-Fi: Frisco Forrest, Sports: Gleaming Jawbreaker, Sci-Fi: Goldmine Tycoon, Action: Handicap Boondock, Foreign: Hellfighters Sierra, Family: Homicide Peach, Sports: Honey Ties, Children: Idols Snatchers, Documentary: Kill Brotherhood, Family: Manchurian Curtain, Comedy: Memento Zoolander, Foreign: Mixed Doors, Sci-Fi: Mourning Purple, Foreign: Newton Labyrinth, Action: Patriot Roman, Drama: Pity Bound, Family: Rage Games, Music: Taxi Kick, Foreign: Trap Guys, Games: Volcano Texas, Horror: Watership Frontier |
| 29       | Alec Wayne         | Sports: Aladdin Calendar, Drama: Blade Polish, Action: Bull Shawshank, Children: Cabin Flash, Classics: Center Dinosaur, Music: Chamber Italian, Drama: Coneheads Smoochy, New: Destiny Saturday, Family: Effect Gladiator, Drama: Encounters Curtain, Sci-Fi: Express Lonely, Foreign: Fiction Christmas, Comedy: Freedom Cleopatra, Travel: Fugitive Maguire, New: Hours Rage, Comedy: Hustler Party, Sci-Fi: Identity Lover, Music: Insider Arizona, Classics: Jeopardy Encino, Sports: Joon Northwest, Sports: Liberty Magnificent, Children: Magic Mallrats, New: Money Harold, Games: Outbreak Divine, Sci-Fi: Reign Gentlemen, Documentary: Smoking Barbarella, Classics: Summer Scarface, Children: Uptown Young, Drama: Virgin Daisy |
| 65       | Angela Hudson      | Sci-Fi: Armageddon Lost, Games: Autumn Crow, Action: Bride Intrigue, Games: Bulworth Commandments, Games: Candles Grapes, Travel: Cassidy Wyoming, Music: Clones Pinocchio, Comedy: Element Freddy, New: Fatal Haunted, Sci-Fi: Frisco Forrest, Travel: Games Bowfinger, Action: Gosford Donnie, Music: Hanover Galaxy, Classics: Island Exorcist, Horror: Japanese Run, Family: Jason Trap, Children: Jumping Wrath, Travel: Kick Savannah, Music: Legend Jedi, Sports: Lesson Cleopatra, Animation: Luke Mummy, Games: Maltese Hope, Documentary: Metal Armageddon, Sports: Mile Mulan, Animation: Nash Chocolat, Horror: Paris Weekend, Drama: Pity Bound, Classics: Prejudice Oleander, Sci-Fi: Random Go, Children: Robbers Joon, Children: Strangelove Desire, Comedy: Velvet Terminator, Classics: Voyage Legally, Horror: Watership Frontier |
| 144      | Angela Witherspoon | Animation: Alter Victory, Action: Berets Agent, Drama: Blade Polish, New: Boulevard Mob, Comedy: Bringing Hysterical, Action: Bull Shawshank, Travel: Casablanca Super, Travel: Cassidy Wyoming, Comedy: Cat Coneheads, Action: Celebrity Horn, Sports: Chance Resurrection, Documentary: Coast Rainbow, Classics: Core Suit, New: Day Unfaithful, Classics: Detective Vision, Sports: Dude Blindness, Drama: Edge Kissing, Sports: Evolution Alter, Sports: Exorcist Sting, Sci-Fi: Fiddler Lost, Documentary: Halloween Nuts, Drama: Hanging Deep, Drama: Jacket Frisco, Drama: Kwai Homeward, Music: Lucky Flying, Sports: Mother Oleander, Sports: Peak Forever, Horror: Pulp Beverly, Family: Rush Goodfellas, Games: Sassy Packer, Documentary: Secret Groundhog, Travel: Shawshank Bubble, Foreign: Stepmom Dream, Travel: Tomatoes Hellfighters, Animation: Wait Cider |
| 76       | Angelina Astaire   | Classics: Beast Hunchback, Children: Beneath Rush, Children: Betrayed Rear, New: Breakfast Goldfinger, Horror: Carrie Bunch, Sports: Cranes Reservoir, Animation: Desire Alien, Sci-Fi: Disturbing Scarface, New: Dragonfly Strangers, Family: Gandhi Kwai, Comedy: Hustler Party, Animation: Intentions Empire, Sports: Jade Bunch, Family: Killer Innocent, Comedy: Memento Zoolander, Comedy: Mulan Moon, Sports: Mummy Creatures, Travel: Order Betrayed, Travel: Outlaw Hanky, Documentary: Pacific Amistad, Drama: Racer Egg, New: Samurai Lion, Comedy: Saturn Name, Games: Seven Swarm, Action: Story Side, Classics: Summer Scarface, Family: Sunset Racer, Horror: Swarm Gold, Sci-Fi: Trojan Tomorrow, New: Vanished Garden, Drama: Wardrobe Phantom |


````

## 5. Vistas

## 6. Restricciones CHECK añadidas
* En la **tabla rental**, asegurar que la fecha de alquiler sea menor o igual a la fecha de devolución.
````
ALTER TABLE rental
ADD CONSTRAINT check_rental_date_before_return_date
CHECK (rental_date <= return_date);
````
* En la **tabla film**, asegurar que, el precio diario por alquilar la película y el costo de reposición por pérdida ó daño, sean mayor a 0 (positivos).
````
ALTER TABLE film
ADD CONSTRAINT check_rental_rate_positive
CHECK (rental_rate > 0);

ALTER TABLE film
ADD CONSTRAINT check_replacement_cost_positive
CHECK (replacement_cost > 0);
````
* En la **tabla film**, asegurar que el tiempo de alquiler tenga un tiempo máximo. 
````
ALTER TABLE film
ADD CONSTRAINT check_rental_duration
CHECK (rental_duration > 0 AND rental_duration <= 30);
````
* En la **tabla payment**, asegurar que el pago no se realiza en una fecha inválida.
````
ALTER TABLE payment
ADD CONSTRAINT check_payment_date_not_in_future
CHECK (payment_date <= CURRENT_TIMESTAMP);
````
* En la **tabla customer**, asegurar que el email tiene un buen formato.
````
ALTER TABLE customer
ADD CONSTRAINT check_email_format 
CHECK (email IS NULL OR email LIKE '%@%');
````
* En la **tabla customer**, asegurar que activebool sólo contiene TRUE o FALSE.
````
ALTER TABLE customer
ADD CONSTRAINT check_activebool_validboolean 
CHECK (activebool IN (TRUE, FALSE));
````
* En la **tabla film** , asegurar que los valores de rating se encuentren en un dominio predefinido.
````
ALTER TABLE film
ADD CONSTRAINT check_valid_rating
CHECK (rating IN ('G', 'PG', 'PG-13', 'R', 'NC-17'));
````

## 7. Triggers en customer:

## 8. Disparador que almacena, en una nueva tabla, la fecha de las nuevas inserciones de `film`.
* Se crea una nueva tabla que contendrá los atributos `film_id` e `insert_date`. Ambos serán clave promaria.
```
CREATE TABLE film_inserts_log (
    film_id INTEGER NOT NULL,
    insert_date TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (film_id, insert_date)
);
```
* Se crea la función a la que llamará el trigger. Esta inserta un nuevo resgistro en la tabla `film_inserts_log` cada vez que es invocada.
````
CREATE OR REPLACE FUNCTION film_insertion()
RETURNS TRIGGER AS $$
BEGIN
    INSERT INTO film_inserts_log (film_id, insert_date)
    VALUES (NEW.film_id, CURRENT_TIMESTAMP);

    RETURN NEW;
END;
$$ LANGUAGE plpgsql;
````
* Se crea el disparador que llama a la función `film_insertion()` cada vez que se inserta un nuevo registro en la tabla `film`. 
````
CREATE TRIGGER trigger_log_film_insertion
AFTER INSERT ON film
FOR EACH ROW
EXECUTE FUNCTION log_film_insertion();
````

## 9. Disparador que almacena, en una nueva tabla, la fecha de los registros eliminados de `film`.

## 10. Secuencias

#### * Volcado final: `sudo -u postgres pg_dump -d db_prct04 > db_prct04.sql`













