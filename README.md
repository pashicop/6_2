# 6_2
## 1
docker-compose.yml
```
# Use postgres/example user/password credentials
version: '3.1'

services:

  db:
    image: postgres:12
    restart: always
    ports:
      - 54320:5432
    volumes:
      - ./data/db:/var/lib/postgresql/data/
      - ./data/backup:/var/lib/postgresql/backup
    environment:
      POSTGRES_PASSWORD: example
      PGDATA: /var/lib/postgresql/data/pgdata
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```
## 2
Список БД:  
```
test_db=# \l
                                     List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |       Access privileges        
-----------+----------+----------+------------+------------+--------------------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres                   +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres                   +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =Tc/postgres                  +
           |          |          |            |            | postgres=CTc/postgres         +
           |          |          |            |            | "test-admin-user"=CTc/postgres
(4 rows)
```
Описание таблиц:  
```
test_db=# \d orders
                            Table "public.orders"
 Column |  Type   | Collation | Nullable |              Default               
--------+---------+-----------+----------+------------------------------------
 id     | integer |           | not null | nextval('orders_id_seq'::regclass)
 name   | text    |           |          | 
 price  | integer |           |          | 
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Referenced by:
    TABLE "clients" CONSTRAINT "order_id" FOREIGN KEY ("order") REFERENCES orders(id)

test_db=# \d clients
                             Table "public.clients"
 Column  |  Type   | Collation | Nullable |               Default               
---------+---------+-----------+----------+-------------------------------------
 id      | integer |           | not null | nextval('clients_id_seq'::regclass)
 soname  | text    |           |          | 
 country | text    |           |          | 
 order   | integer |           |          | 
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
Foreign-key constraints:
    "order_id" FOREIGN KEY ("order") REFERENCES orders(id)

```
SQL запрос для выдачи списка пользователей с правами над таблицами test_db:  
`SELECT * from information_schema.table_privileges where grantee = 'test-admin-user' or grantee = 'test-simple-user' order by grantee, table_name;`
Список пользователей с правами над таблицами test_db:  
```
test_db=# SELECT * from information_schema.table_privileges where grantee = 'test-admin-user' or grantee = 'test-simple-user' order by grantee, table_name;
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy 
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | test-admin-user  | test_db       | public       | clients    | TRUNCATE       | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test-admin-user  | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | DELETE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | TRIGGER        | NO           | NO
 postgres | test-admin-user  | test_db       | public       | clients    | REFERENCES     | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | DELETE         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | TRUNCATE       | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | REFERENCES     | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | TRIGGER        | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test-admin-user  | test_db       | public       | orders     | SELECT         | NO           | YES
 postgres | test-admin-user  | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
 postgres | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
(22 rows)
```
