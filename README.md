# Práctica 4. Modelo Relacional. Vistas y disparadores Tarea
### Autores: Alba Morales Martín y Ruyman 

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
   Column    |            Type             | Collation | Nullable |                   Default                   
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
   Column    |            Type             | Collation | Nullable |                  Default                  
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
    Column    |            Type             | Collation | Nullable |                    Default                    
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













