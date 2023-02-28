# java-filmorate

Спринт 11 Промежуточное задание по SQL

# **Описание схемы базы данных filmorate.**

За основу проектирования  схемы БД  были взяты  модели классов   программы java-filmorate. Выделены  следующие 
сущностные элементы.

Сущность пользователь  (user) отвечает за хранения информации о пользователе. Как следует из названия поля  login,
оно хранит информацию о логине пользователя, name — о его имени, email  - о почтовом адресе пользователя. 
Таблица user связана через внешние ключи user_id и friends_id с таблицей друзья (friends). 	Таблица  friends необходима,
так как она позволяет организовать связь многие-ко-многим.

Сущность likes позволяет хранить данные о любимых фильмах пользователя в поле film_id. Связана с таблицей user через
поле user_id. Содержит внешний ключ film_id для связи с таблицей film.
   
Сущность фильм (film) содержит следующие поля:  name (название фильма ),  description (описание фильма),
realisedate (дата выхода  фильма), duration (продолжительность фильма). Три внешних ключа таблицы связывают ее
с  таблицей возрастных ограничений (category_id->category),  таблицей жанров (genre_id→genre) и таблицей  любимых
фильмов(like_id→likes).

Сущность category отвечает за  возрастные ограничения фильмов.  Поле name определяет наименование возрастного ценза,
первичный ключ category_id связан с таблицей film.

Сущность жанр (genre) хранит данные о жанре фильма в поле name. Связана с таблицей film через первичный ключ genre_id.



# **Схема базы данных filmorate приведена на рисунке.**

![filmorate.png](https://github.com/olegfxf/ERDiagramm/blob/main/filmorate.png "ER")


# **Тестовые запросы к таблицам базы данных filmorate.**
Создаем все необходимые таблицы

 Создаем таблицу user:
 ```SQL
 CREATE TABLE "user" (
 user_id integer NOT NULL GENERATED ALWAYS AS IDENTITY,
 login varchar NOT NULL,
 name varchar NOT NULL,
 email varchar NOT NULL,
 CONSTRAINT user_pk PRIMARY KEY (user_id)
 );
```

Создаем таблицу friends:
 ```SQL
 CREATE TABLE friends (
	user_id integer NOT NULL,
	friend_id integer NOT NULL,
	status bool NOT NULL,
    CONSTRAINT friends_fk1 FOREIGN KEY (user_id) REFERENCES "user"(user_id),
    CONSTRAINT friends_fk2 FOREIGN KEY (friend_id) REFERENCES "user"(user_id)
```
Создаем таблицу genre
```SQL 
CREATE TABLE genre (
	genre_id integer NOT NULL GENERATED ALWAYS AS IDENTITY,
	name varchar NOT NULL,
	CONSTRAINT genre_pk PRIMARY KEY (genre_id)
);
```
Создаем таблицу category
```SQL 
CREATE TABLE category (
	category_id integer NOT NULL GENERATED ALWAYS AS IDENTITY,
	name varchar NOT NULL,
	CONSTRAINT category_pk PRIMARY KEY (category_id)
);
```
Создаем таблицу film
```SQL 
CREATE TABLE film (
	film_id integer NOT NULL GENERATED ALWAYS AS IDENTITY,
	name varchar NOT NULL,
	description varchar NOT NULL,
	releasedate date NOT NULL,
	duration integer NOT NULL,
	category_id integer NOT NULL,
	genre_id integer NOT NULL,
	CONSTRAINT film_pk PRIMARY KEY (film_id),
	CONSTRAINT film_ct_fk FOREIGN KEY (category_id) REFERENCES category(category_id),
	CONSTRAINT film_gn_fk FOREIGN KEY (genre_id) REFERENCES genre(genre_id)
);
```
Создаем  таблицы likes:
```SQL 
CREATE TABLE likes (
	film_id integer NOT NULL,
	user_id integer NOT NULL,
   	CONSTRAINT likes_us_fk FOREIGN KEY (user_id) REFERENCES "user"(user_id),
	CONSTRAINT likes_fl_fk FOREIGN KEY (film_id) REFERENCES "film"(film_id)	
);
```




Тестовые запросы к таблицам базы данных filmorate.


Проверяем доступность таблицы user:
UPDATE public."user" SET email  = 'djo22@mail.ru' WHERE user_id = 3;

| user_id | login  | name  | email           |
|---------|--------|-------|-----------------|
| 1       | login1 | name1 | name1@yandex.ru |
| 2       | login2 | name2 | name1@yandex.ru |
| 4       | login4 | name4 | name1@yandex.ru |
| 5       | login5 | name5 | name1@yandex.ru |
| 6       | login6 | name6 | name1@yandex.ru |
| 7       | login7 | name7 | name1@yandex.ru |
| 8       | login8 | name8 | name1@yandex.ru |
| 3       | login3 | name3 | djo22@mail.ru   |


Проверяем связь таблиц friends c user:
```SQL 
SELECT * FROM public."user" u
LEFT JOIN public.friends f on u.user_id  = f.user_id ;`
```
| user_id | login  | name  | email           | user_id | film_id |
|---------|--------|-------|-----------------|---------|---------|
| 1       | login1 | name1 | name1@yandex.ru | 1       | 6       |
| 1       | login1 | name1 | name1@yandex.ru | 1       | 3       |
| 2       | login2 | name2 | name1@yandex.ru | 2       | 5       |
| 2       | login2 | name2 | name1@yandex.ru | 2       | 7       |
| 5       | login5 | name5 | name1@yandex.ru | 5       | 1       |
| 8       | login8 | name8 | name1@yandex.ru |         |         |
| 6       | login6 | name6 | name1@yandex.ru |         |         |
| 4       | login4 | name4 | name1@yandex.ru |         |         |
| 9       | login9 | name9 | name1@yandex.ru |         |         |
| 7       | login7 | name7 | name1@yandex.ru |         |         |


```SQL 
SELECT user_id, film_id, status FROM friends
``` 
| user_id | film_id | status |
|---------|---------|--------|
| 1       | 6       | true   |
| 1       | 3       | false  |
| 2       | 5       | true   |
| 2       | 7       | false  |
| 5       | 1       | true   |


Проверяем генерацию первичного ключа:
```SQL 
INSERT INTO public.user (login, name, email) VALUES ('1login', '1name','name@ya.ru');
SELECT * FROM public."user"
```
| user_id | login  | name  | email           |
|---------|--------|-------|-----------------|
| 1       | login1 | name1 | name1@yandex.ru |
| 2       | login2 | name2 | name1@yandex.ru |
| 4       | login4 | name4 | name1@yandex.ru |
| 5       | login5 | name5 | name1@yandex.ru |
| 6       | login6 | name6 | name1@yandex.ru |
| 7       | login7 | name7 | name1@yandex.ru |
| 8       | login8 | name8 | name1@yandex.ru |
| 3       | login3 | name3 | djo22@mail.ru   |
| 12      | 1login | 1name | name@ya.ru      |


Проверяем связь таблиц  film и user через таблицу likes:
```SQL 
SELECT f.film_id , u.user_id  FROM public."user" u
INNER JOIN public.likes l on u.user_id  = l.user_id
INNER join film f on l.film_id = f.film_id 
```
| film  | name  |
|-------|-------|
| film1 | name7 |
| film1 | name2 |
| film3 | name4 |
| film5 | name4 |
| film4 | name3 |
| film5 | name2 |
```SQL 
SELECT film_id, user_id FROM likes
```
| film_id | user_id |
|---------|---------|
| 1       | 7       |
| 1       | 2       |
| 3       | 4       |
| 5       | 4       |
| 4       | 3       |
| 5       | 2       |

Проверяем связь таблиц genre, category с таблицей film:
```SQL 
select  f."name" as titel,f.description as description , 
f.releasedate ,g."name" as genre ,c."name" as category  from film f  
Inner join public.genre g on f.genre_id = g.genre_id
Inner join public.category c on c.category_id = f.category_id  
```
| name  | description  | date       | genre          | category |
|-------|--------------|------------|----------------|----------|
| film2 | description2 | 1929-01-12 | Драма          | G        |
| film3 | description3 | 1939-01-12 | Комедия        | NC-17    |
| film4 | description4 | 1949-01-12 | Драма          | NC-17    |
| film5 | description5 | 1959-01-12 | Драма          | G        |
| film6 | description6 | 1969-01-12 | Триллер        | G        |
| film7 | description7 | 1979-01-12 | Документальный | G        |
| film8 | description8 | 1989-01-12 | Боевик         | G        |



