+++
date = '2025-09-02'
title = 'Web E-shop Project - BagusDrip - Part 2'
+++

# MySQL (the bane of my existence)

As everyone already know, MySQL is a type of Relational Database Management System (RDBMS). For our purposes, it is used to store, manage and retrieve everything related to the Webapp including, customer information, product IDs, product quantities, order records and so on. Said data is stored in tables that has rows and columns. And according to the wiki, the tables are **related** based on common columns, that's why it's called _Relational database_. Wow, who would have thought.  


Also, my professor for the course taught us how to configure everything using Command Prompt, and not the more intuitive SQL Workbench GUI or whatever. So, if you are looking forward to that, it's best to stop reading lol

## Directory
```Text
C:\
|-- WebProject
    |-- mysql
        |-- bin
```
When you first install mySQL from the [site](https://dev.mysql.com/downloads/installer/), you should probably set up a new folder called Webapp Project or whatever and use that as the directory.  

## Console Server
Before we start the server, we need to go into the directory above where the bin file is, because that is where the executable file is.  
Only then we do:  
`mysqld --console`  
You will see some lines pop up, but just ignore them.  
To shut down, all you need to do is press `ctrl + c`, but let's keep it on for now.

### Server Meaning
"mysqld" stands for MySQL Daemon. A "daemon" is a computing term that describes a program that runs in the background of your computer system, without user directions.  

When we run "mysqld", we started a MySQL server which listens for client connections, in this case, would be the Java servlet on a selected port.


## Starting a Client

`mysql -u root -p`  
Note that from this point onwards, I won't really explain the codes because they are kind of self-explanatory. Also, for this part in particular, I'm just gonna show you my database instead of creating another one.  
```SQL
mysql> show DATABASES;
+--------------------+
| Database           |
+--------------------+
| bagusdrip          |
| clicker            |
| ebookshop          |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
7 rows in set (0.00 sec)

mysql> use bagusdrip;
Database changed
mysql> show tables;
+---------------------+
| Tables_in_bagusdrip |
+---------------------+
| order_records       |
| products            |
+---------------------+
2 rows in set (0.04 sec)

mysql> select * from products;
+----+--------+----------------------------------+----------------+--------+------+----------------------------------------------------+
| id | brand  | model                            | category       | price  | qty  | image_url                                          |
+----+--------+----------------------------------+----------------+--------+------+----------------------------------------------------+
|  1 | Nike   | Air Max 90                       | Shoes          |    120 |   20 | images/nike-air-max-90.png                         |
|  2 | Nike   | React Infinity Run               | Shoes          |  150.5 |   14 | images/nike-react-infinity-run.png                 |
|  3 | Nike   | Blazer Mid 77                    | Shoes          | 110.25 |   30 | images/nike-blazer-mid-77.png                      |
|  4 | Nike   | Sportswear Club Fleece           | Sweatshirt     |     60 |   25 | images/nike-sportswear-club-fleece.png             |
|  5 | Nike   | Tech Fleece Hoodie               | Sweatshirt     |  90.75 |   18 | images/nike-tech-fleece-hoodie.png                 |
|  6 | Nike   | Therma-FIT Pullover              | Sweatshirt     |  75.25 |   22 | images/nike-therma-fit-pullover.png                |
|  7 | Nike   | Flex Stride Shorts               | Joggers/Shorts |     45 |   30 | images/nike-flex-stride-shorts.png                 |
|  8 | Nike   | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 |   35 | images/nike-dri-fit-academy-shorts.png             |
|  9 | Nike   | Sportswear Club Fleece Joggers   | Joggers/Shorts |  55.75 |   40 | images/nike-sportswear-club-fleece-joggers.png     |
| 10 | Adidas | Ultraboost DNA                   | Shoes          |    180 |   18 | images/adidas-ultraboost-dna.png                   |
| 11 | Adidas | Stan Smith                       | Shoes          |  80.75 |   25 | images/adidas-stan-smith.png                       |
| 12 | Adidas | NMD_R1                           | Shoes          |    130 |   22 | images/adidas-nmd-r1.png                           |
| 13 | Adidas | Essentials Fleece Hoodie         | Sweatshirt     |     70 |   20 | images/adidas-essentials-fleece-hoodie.png         |
| 14 | Adidas | Adicolor Classics Trefoil Hoodie | Sweatshirt     |   85.5 |   15 | images/adidas-adicolor-classics-trefoil-hoodie.png |
| 15 | Adidas | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 |   28 | images/adidas-future-icons-3-bar-hoodie.png        |
| 16 | Adidas | Own The Run Shorts               | Joggers/Shorts |     50 |   25 | images/adidas-own-the-run-shorts.png               |
| 17 | Adidas | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 |   32 | images/adidas-designed-4-training-shorts.png       |
| 18 | Adidas | Tiro 23 League Training Pants    | Joggers/Shorts |     65 |   38 | images/adidas-tiro-23-league-training-pants.png    |
| 19 | Puma   | RS-X                             | Shoes          |  100.5 |   28 | images/puma-rs-x.png                               |
| 20 | Puma   | Cali Sport                       | Shoes          |     90 |   35 | images/puma-cali-sport.png                         |
| 21 | Puma   | Suede Classic                    | Shoes          |  75.25 |   40 | images/puma-suede-classic.png                      |
| 22 | Puma   | Essentials+ Two-Tone Hoodie      | Sweatshirt     |     55 |   30 | images/puma-essentials-two-tone-hoodie.png         |
| 23 | Puma   | Amplified Hoodie                 | Sweatshirt     |  65.75 |   20 | images/puma-amplified-hoodie.png                   |
| 24 | Puma   | Essentials+ Big Logo Hoodie      | Sweatshirt     |  80.25 |   25 | images/puma-essentials-big-logo-hoodie.png         |
| 25 | Puma   | Run Favorite Woven 7 Shorts      | Joggers/Shorts |     40 |   35 | images/puma-run-favorite-woven-7-shorts.png        |
| 26 | Puma   | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 |   40 | images/puma-teamliga-training-shorts.png           |
| 27 | Puma   | Essentials Fleece Pants          | Joggers/Shorts |  50.75 |   45 | images/puma-essentials-fleece-pants.png            |
+----+--------+----------------------------------+----------------+--------+------+----------------------------------------------------+
27 rows in set (0.04 sec)
mysql> select * from order_records;
+----------+------------+-----------+---------------+------------+----------------------------------+----------------+--------+---------------------+----------+
| order_id | product_id | cust_name | cust_email    | cust_phone | model                            | category       | price  | order_date          | quantity |
+----------+------------+-----------+---------------+------------+----------------------------------+----------------+--------+---------------------+----------+
|        1 |         19 | asd       | asd           | asd        | RS-X                             | Shoes          |  100.5 | 2025-03-04 10:09:24 |        1 |
|        2 |         19 | asd       | asd           | asd        | RS-X                             | Shoes          |  100.5 | 2025-03-04 10:09:24 |        1 |
|        3 |         19 | asd       | asd           | asd        | RS-X                             | Shoes          |  100.5 | 2025-03-04 10:09:24 |        1 |
|        4 |         10 | asd       | asd           | asd        | Ultraboost DNA                   | Shoes          |    180 | 2025-03-04 10:09:24 |        1 |
|        5 |         14 | asd       | asd           | asd        | Adicolor Classics Trefoil Hoodie | Sweatshirt     |   85.5 | 2025-03-04 10:09:24 |        1 |
|        6 |         12 | a         | a             | a          | NMD_R1                           | Shoes          |    130 | 2025-03-04 10:10:26 |        1 |
|        7 |         11 | a         | a             | a          | Stan Smith                       | Shoes          |  80.75 | 2025-03-04 10:10:26 |        1 |
|        8 |         17 | a         | a             | a          | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-04 10:10:26 |        1 |
|        9 |         10 | a         | a             | a          | Ultraboost DNA                   | Shoes          |    180 | 2025-03-04 10:27:29 |        1 |
|       10 |         12 | a         | a             | a          | NMD_R1                           | Shoes          |    130 | 2025-03-04 10:27:29 |        1 |
|       11 |         10 | asd       | asdasd        | asdasd     | Ultraboost DNA                   | Shoes          |    180 | 2025-03-04 12:52:45 |        1 |
|       12 |         15 | asd       | asdasd        | asdasd     | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 | 2025-03-04 12:52:45 |        1 |
|       13 |         13 | asd       | asdasd        | asdasd     | Essentials Fleece Hoodie         | Sweatshirt     |     70 | 2025-03-04 12:52:45 |        1 |
|       14 |          2 | asd       | asdasd        | asdasd     | React Infinity Run               | Shoes          |  150.5 | 2025-03-04 12:52:45 |        1 |
|       15 |          3 | asd       | asdasd        | asdasd     | Blazer Mid 77                    | Shoes          | 110.25 | 2025-03-04 12:52:45 |        1 |
|       16 |          4 | asd       | asdasd        | asdasd     | Sportswear Club Fleece           | Sweatshirt     |     60 | 2025-03-04 12:52:45 |        1 |
|       17 |         22 | asd       | asdasd        | asdasd     | Essentials+ Two-Tone Hoodie      | Sweatshirt     |     55 | 2025-03-04 12:52:45 |        1 |
|       18 |         26 | asd       | asdasd        | asdasd     | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-04 12:52:45 |        1 |
|       19 |         19 | asd       | asdasd        | asdasd     | RS-X                             | Shoes          |  100.5 | 2025-03-04 12:52:45 |        1 |
|       20 |         20 | sdfg      | dfg           | sdfg       | Cali Sport                       | Shoes          |     90 | 2025-03-04 13:03:47 |        1 |
|       21 |         19 | sdfg      | dfg           | sdfg       | RS-X                             | Shoes          |  100.5 | 2025-03-04 13:03:47 |        1 |
|       22 |         19 | ass       | ass           | ass        | RS-X                             | Shoes          |  100.5 | 2025-03-07 22:45:43 |        1 |
|       23 |          2 | aSS       | ASS           | ASS        | React Infinity Run               | Shoes          |  150.5 | 2025-03-07 23:26:39 |        1 |
|       24 |          7 | aSS       | ASS           | ASS        | Flex Stride Shorts               | Joggers/Shorts |     45 | 2025-03-07 23:26:39 |        1 |
|       25 |          8 | aSS       | ASS           | ASS        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-07 23:26:39 |        1 |
|       26 |          8 | aSS       | ASS           | ASS        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-07 23:26:39 |        1 |
|       27 |          8 | aSS       | ASS           | ASS        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-07 23:26:39 |        1 |
|       28 |          8 | aSS       | ASS           | ASS        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-07 23:26:39 |        1 |
|       29 |          8 | aSS       | ASS           | ASS        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-07 23:26:39 |        1 |
|       30 |         19 | AA        | AA            | AA         | RS-X                             | Shoes          |  100.5 | 2025-03-07 23:27:53 |        1 |
|       31 |         19 | ASDFDasfd | asdfasd       | fasdfasdf  | RS-X                             | Shoes          |  100.5 | 2025-03-08 21:46:19 |        1 |
|       32 |         24 | ASDFDasfd | asdfasd       | fasdfasdf  | Essentials+ Big Logo Hoodie      | Sweatshirt     |  80.25 | 2025-03-08 21:46:19 |        1 |
|       33 |         26 | ASDFDasfd | asdfasd       | fasdfasdf  | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-08 21:46:19 |        1 |
|       34 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-09 19:18:43 |        1 |
|       35 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-09 19:18:43 |        1 |
|       36 |         27 | a         | a             | a          | Essentials Fleece Pants          | Joggers/Shorts |  50.75 | 2025-03-09 19:34:46 |        1 |
|       37 |         22 | a         | a             | a          | Essentials+ Two-Tone Hoodie      | Sweatshirt     |     55 | 2025-03-09 19:34:46 |        1 |
|       38 |         23 | a         | a             | a          | Amplified Hoodie                 | Sweatshirt     |  65.75 | 2025-03-09 19:34:46 |        1 |
|       39 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-09 19:34:46 |        1 |
|       40 |         15 | a         | a             | a          | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 | 2025-03-09 19:40:20 |        1 |
|       41 |         12 | a         | a             | a          | NMD_R1                           | Shoes          |    130 | 2025-03-09 19:40:20 |        1 |
|       42 |         16 | a         | a             | a          | Own The Run Shorts               | Joggers/Shorts |     50 | 2025-03-09 19:40:20 |        1 |
|       43 |         10 | a         | a             | a          | Ultraboost DNA                   | Shoes          |    180 | 2025-03-09 19:40:20 |        1 |
|       44 |         10 | a         | a             | a          | Ultraboost DNA                   | Shoes          |    180 | 2025-03-09 19:40:20 |        1 |
|       45 |         17 | a         | a             | a          | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-09 19:40:20 |        1 |
|       46 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-09 19:41:17 |        1 |
|       47 |         10 | a         | a             | a          | Ultraboost DNA                   | Shoes          |    180 | 2025-03-09 19:41:17 |        1 |
|       48 |         12 | a         | a             | a          | NMD_R1                           | Shoes          |    130 | 2025-03-09 19:41:17 |        1 |
|       49 |         19 | a         | a             | a          | RS-X                             | Shoes          |  100.5 | 2025-03-09 19:47:09 |        1 |
|       50 |          2 | a         | a             | a          | React Infinity Run               | Shoes          |  150.5 | 2025-03-09 19:47:09 |        1 |
|       51 |         15 | a         | a             | a          | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 | 2025-03-09 19:48:56 |        1 |
|       52 |         17 | a         | a             | a          | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-09 19:48:56 |        1 |
|       53 |         16 | a         | a             | a          | Own The Run Shorts               | Joggers/Shorts |     50 | 2025-03-09 19:48:56 |        1 |
|       54 |         20 | a         | a             | a          | Cali Sport                       | Shoes          |     90 | 2025-03-09 19:51:08 |        1 |
|       55 |         27 | a         | a             | a          | Essentials Fleece Pants          | Joggers/Shorts |  50.75 | 2025-03-09 19:51:08 |        1 |
|       56 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-09 19:51:08 |        1 |
|       57 |         22 | sdf       | sdf           | sdf        | Essentials+ Two-Tone Hoodie      | Sweatshirt     |     55 | 2025-03-09 20:07:49 |        1 |
|       58 |         27 | sdf       | sdf           | sdf        | Essentials Fleece Pants          | Joggers/Shorts |  50.75 | 2025-03-09 20:07:49 |        1 |
|       59 |         19 | sdf       | sdf           | sdf        | RS-X                             | Shoes          |  100.5 | 2025-03-09 20:07:49 |        1 |
|       60 |         27 | a         | a             | a          | Essentials Fleece Pants          | Joggers/Shorts |  50.75 | 2025-03-09 23:46:05 |        1 |
|       61 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-10 15:36:55 |        1 |
|       62 |          8 | a         | a             | a          | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-10 15:36:55 |        1 |
|       63 |         17 | a         | a             | a          | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-10 15:36:55 |        1 |
|       64 |          7 | a         | a             | a          | Flex Stride Shorts               | Joggers/Shorts |     45 | 2025-03-10 15:36:55 |        1 |
|       65 |         16 | a         | a             | a          | Own The Run Shorts               | Joggers/Shorts |     50 | 2025-03-10 15:36:55 |        1 |
|       66 |         27 | a         | a             | a          | Essentials Fleece Pants          | Joggers/Shorts |  50.75 | 2025-03-10 15:36:55 |        1 |
|       67 |          9 | a         | a             | a          | Sportswear Club Fleece Joggers   | Joggers/Shorts |  55.75 | 2025-03-10 15:36:55 |        1 |
|       68 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-10 15:40:16 |        1 |
|       69 |         17 | a         | a             | a          | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-10 15:40:16 |        1 |
|       70 |         26 | a         | a             | a          | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-10 15:40:16 |        1 |
|       71 |         17 | a         | a             | a          | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-10 15:40:16 |        1 |
|       72 |         16 | a         | a             | a          | Own The Run Shorts               | Joggers/Shorts |     50 | 2025-03-10 15:40:16 |        1 |
|       73 |         27 | a         | a             | a          | Essentials Fleece Pants          | Joggers/Shorts |  50.75 | 2025-03-10 15:40:16 |        1 |
|       74 |         22 | a         | a             | a          | Essentials+ Two-Tone Hoodie      | Sweatshirt     |     55 | 2025-03-10 15:40:16 |        1 |
|       75 |         18 | a         | a             | a          | Tiro 23 League Training Pants    | Joggers/Shorts |     65 | 2025-03-10 15:40:16 |        1 |
|       76 |         23 | a         | a             | a          | Amplified Hoodie                 | Sweatshirt     |  65.75 | 2025-03-10 15:40:16 |        1 |
|       77 |         13 | a         | a             | a          | Essentials Fleece Hoodie         | Sweatshirt     |     70 | 2025-03-10 15:40:16 |        1 |
|       78 |         21 | a         | a             | a          | Suede Classic                    | Shoes          |  75.25 | 2025-03-10 15:40:16 |        1 |
|       79 |         24 | a         | a             | a          | Essentials+ Big Logo Hoodie      | Sweatshirt     |  80.25 | 2025-03-10 15:40:16 |        1 |
|       80 |         11 | a         | a             | a          | Stan Smith                       | Shoes          |  80.75 | 2025-03-10 15:40:16 |        1 |
|       81 |         14 | a         | a             | a          | Adicolor Classics Trefoil Hoodie | Sweatshirt     |   85.5 | 2025-03-10 15:40:16 |        1 |
|       82 |         20 | a         | a             | a          | Cali Sport                       | Shoes          |     90 | 2025-03-10 15:40:16 |        1 |
|       83 |         15 | a         | a             | a          | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 | 2025-03-10 15:40:16 |        1 |
|       84 |         19 | a         | a             | a          | RS-X                             | Shoes          |  100.5 | 2025-03-10 15:40:16 |        1 |
|       85 |         12 | a         | a             | a          | NMD_R1                           | Shoes          |    130 | 2025-03-10 15:40:16 |        1 |
|       86 |         10 | a         | a             | a          | Ultraboost DNA                   | Shoes          |    180 | 2025-03-10 15:40:16 |        1 |
|       87 |          8 | asd       | asd           | asd        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-10 15:40:30 |        1 |
|       88 |          7 | asd       | asd           | asd        | Flex Stride Shorts               | Joggers/Shorts |     45 | 2025-03-10 15:40:30 |        1 |
|       89 |          9 | asd       | asd           | asd        | Sportswear Club Fleece Joggers   | Joggers/Shorts |  55.75 | 2025-03-10 15:40:30 |        1 |
|       90 |          2 | a         | a             | a          | React Infinity Run               | Shoes          |  150.5 | 2025-03-10 15:49:06 |        1 |
|       91 |          5 | asd       | asd@email.com | asd        | Tech Fleece Hoodie               | Sweatshirt     |  90.75 | 2025-03-10 19:45:49 |        1 |
|       92 |          6 | asd       | asd@email.com | asd        | Therma-FIT Pullover              | Sweatshirt     |  75.25 | 2025-03-10 19:45:49 |        1 |
|       93 |         26 | as        | as@af         | asd        | TeamLiga Training Shorts         | Joggers/Shorts |   30.5 | 2025-03-10 19:55:52 |        1 |
|       94 |          8 | as        | as@af         | asd        | Dri-FIT Academy Shorts           | Joggers/Shorts |   35.5 | 2025-03-10 19:55:52 |        1 |
|       95 |         15 | asd       | asd@asd       | asd        | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 | 2025-03-11 20:35:22 |        1 |
|       96 |         11 | asd       | asd@asd       | asd        | Stan Smith                       | Shoes          |  80.75 | 2025-03-11 20:35:22 |        1 |
|       97 |         16 | asd       | asd@asd       | asd        | Own The Run Shorts               | Joggers/Shorts |     50 | 2025-03-11 20:35:22 |        1 |
|       98 |         10 | asd       | asd@qwe       | asd        | Ultraboost DNA                   | Shoes          |    180 | 2025-03-11 20:56:06 |        4 |
|       99 |         17 | asd       | asd@qwe       | asd        | Designed 4 Training Shorts       | Joggers/Shorts |  40.75 | 2025-03-11 20:56:06 |        2 |
|      100 |         12 | asd       | asd@qwe       | asd        | NMD_R1                           | Shoes          |    130 | 2025-03-11 20:56:06 |        6 |
|      101 |         18 | asd       | asd@qwe       | asd        | Tiro 23 League Training Pants    | Joggers/Shorts |     65 | 2025-03-11 20:56:06 |        3 |
|      102 |         16 | asd       | asd@qwe       | asd        | Own The Run Shorts               | Joggers/Shorts |     50 | 2025-03-11 20:56:06 |        3 |
|      103 |         25 | sdf       | sdf@asd       | asd        | Run Favorite Woven 7 Shorts      | Joggers/Shorts |     40 | 2025-03-11 21:28:25 |        2 |
|      104 |         10 | sdf       | sdf@asd       | asd        | Ultraboost DNA                   | Shoes          |    180 | 2025-03-11 21:28:25 |        1 |
|      105 |          2 | sdf       | sdf@asd       | asd        | React Infinity Run               | Shoes          |  150.5 | 2025-03-11 21:28:25 |        2 |
|      106 |          1 | sdf       | sdf@asd       | asd        | Air Max 90                       | Shoes          |    120 | 2025-03-11 21:28:25 |        2 |
|      107 |         12 | asd       | asd@asd       | asd        | NMD_R1                           | Shoes          |    130 | 2025-03-12 20:54:50 |        3 |
|      108 |          5 | asd       | asd@asd       | asd        | Tech Fleece Hoodie               | Sweatshirt     |  90.75 | 2025-03-13 09:01:57 |        1 |
|      109 |          6 | asd       | asd@asd       | asd        | Therma-FIT Pullover              | Sweatshirt     |  75.25 | 2025-03-13 09:01:57 |        1 |
|      110 |         12 | asd       | asd@asd       | asd        | NMD_R1                           | Shoes          |    130 | 2025-03-17 21:55:15 |        2 |
|      111 |         11 | asd       | asd@asd       | asd        | Stan Smith                       | Shoes          |  80.75 | 2025-03-17 21:55:15 |        2 |
|      112 |         18 | asd       | asd@asd       | asd        | Tiro 23 League Training Pants    | Joggers/Shorts |     65 | 2025-03-17 22:34:33 |        2 |
|      113 |         10 | asd       | asd@asd       | asd        | Ultraboost DNA                   | Shoes          |    180 | 2025-03-17 22:52:00 |        1 |
|      114 |          2 | asd       | asd@asd       | 123        | React Infinity Run               | Shoes          |  150.5 | 2025-03-19 18:00:01 |        1 |
|      115 |          3 | sfdfsd    | sdfsdf@asd    | asdas      | Blazer Mid 77                    | Shoes          | 110.25 | 2025-03-19 20:08:06 |       30 |
|      116 |         10 | asd       | asd@asd       | asd        | Ultraboost DNA                   | Shoes          |    180 | 2025-03-19 23:09:59 |        2 |
|      117 |         12 | asd       | asd@asd       | asd        | NMD_R1                           | Shoes          |    130 | 2025-03-19 23:09:59 |        2 |
|      118 |         15 | asd       | asd@asd       | asd        | Future Icons 3-Bar Hoodie        | Sweatshirt     |  95.25 | 2025-03-19 23:09:59 |        1 |
|      119 |         11 | asd       | asd@asd       | asd        | Stan Smith                       | Shoes          |  80.75 | 2025-03-19 23:09:59 |        1 |
|      120 |          2 | asd       | asd@tgr       | 57567      | React Infinity Run               | Shoes          |  150.5 | 2025-03-20 09:36:04 |        1 |
+----------+------------+-----------+---------------+------------+----------------------------------+----------------+--------+---------------------+----------+
120 rows in set (0.04 sec)
```
Feel free to ignore the order_records part, I didn't clean it since last year, but I kept it in cuz it was kinda funny


## Relating to ProductServlet.java

```Java
//Recall
String updateSql = "UPDATE products SET qty = qty - " + quantity + " WHERE id = " + productId + " AND qty >= " + quantity;
int updateCount = stmt.executeUpdate(updateSql);
```
This line subtracts the ordered quantity from "products" if a certain product id still has "qty" that is more than zero, aka if there is still stock left

```Java
String insertSql = "INSERT INTO order_records (product_id, cust_name, cust_email, cust_phone, model, category, price, quantity) VALUES (?, ?, ?, ?, ?, ?, ?, ?)";
PreparedStatement pstmt = conn.prepareStatement(insertSql);
pstmt.setInt(1, productId);
pstmt.setString(2, custName);
// ...
pstmt.executeUpdate();
```
See how the parameters here match the ones in the order_records table in MySQL above.