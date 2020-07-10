*************JOINS************

*** #1 ***
select * from invoice i
join invoice_line il on il.invoice_id = i.invoice_id
where il.unit_price > 0.99;
*** #2 ***
select i.invoice_date , c.first_name , c.last_name , i.total from invoice i
join customer c on c.customer_id = i.customer_id;
*** #3 ***
select c.first_name, c.last_name, esr.first_name, esr.last_name 
from customer c
join employee esr on c.support_rep_id = esr.employee_id;
*** #4 ***
select album.title, artist.name from album
join artist on album.artist_id = artist.artist_id;
*** #5 ***
select pt.track_id from playlist_track pt
join playlist p on pt.playlist_id = p.playlist_id
where p.name = 'Music';
*** #6 ***
select t.name from track t
join playlist_track pt on t.track_id = pt.track_id
where pt.playlist_id = 5;
*** #7 ***
select t.name, p.name from track t
join playlist_track pt on pt.track_id = t.track_id
join playlist p on pt.playlist_id = p.playlist_id;
*** #8 ***
select t.name, a.title from track t
join album a on t.album_id = a.album_id
join genre g on t.genre_id = g.genre_id
where g.name = 'Alternative & Punk';

***BLACK DIAMOND***
select t.name, g.name, a.title, art.name from track t
join album a on a.album_id = t.album_id
join genre g on g.genre_id = t.genre_id
join artist art on art.artist_id = a.artist_id
join playlist_track pt on pt.track_id = t.track_id
join playlist p on p.playlist_id = pt.playlist_id
where p.name = 'Music';

***********NESTED QUERIES**********

*** #1 ***
select * from invoice
where invoice_id in ( select invoice_id from invoice_line where unit_price > 0.99 ); 
*** #2 ***
select * from playlist_track
where playlist_id in (select playlist_id from playlist where name = 'Music' );
*** #3 ***
select name from track
where track_id in ( select track_id from playlist_track
where playlist_id = 5 );
*** #4 ***
select * from track
where genre_id in ( select genre_id from genre
where name = 'Comedy' );
*** #5 ***
select * from track
where album_id in ( select album_id from album
where title = 'Fireball' );
*** #6 ***
select * from track
where album_id in ( select album_id from album
where artist_id in (select artist_id from artist
where name = 'Queen' ));

**********UPDATING ROWS***********

*** #1 ***
update customer
set fax = null;
*** #2 ***
update customer
set company = 'Self'
where company is null;
*** #3 ***
update customer
set last_name = 'Thompson'
where first_name = 'Julia'
and last_name = 'Barnett';
*** #4 ***
update customer
set support_rep_id = 4
where email = 'luisrojas@yahoo.cl';
*** #5 ***
update track
set composer = 'The darkness around us'
where composer is null and 
genre_id in ( select genre_id from genre
where name = 'Metal' );

***********GROUP BY***************

*** #1 ***
select g.name, count(*)
from track t
join genre g on t.genre_id = g.genre_id
group by g.name
*** #2 ***
select g.name, count(*) 
from track t
join genre g on t.genre_id = g.genre_id
where g.name = 'Pop' or g.name = 'Rock'
group by g.name
*** #3 ***
select art.name, count(*)
from album a
join artist art on art.artist_id = a.artist_id
group by art.name;

***********USE DISTINCT************

*** #1 ***
delete from practice_delete
where type = 'bronze';
*** #2 ***
delete from practice_delete
where type = 'silver';
*** #3 ***
delete from practice_delete
where value = 150;

*******ECOMMERCE SIMULATION********

*** Create 3 tables following the criteria in the summary. ***
create table users (
  user_id serial primary key,
  name varchar(100),
  email varchar(200)
);

create table products (
  product_id serial primary key,
  order_id integer references orders(order_id),
  name varchar(100),
  price float
);

create table orders (
  order_id serial primary key
);
*** Add some data to fill up each table. ***
insert into users (
	name, email  
) values (
  'Kaden', 'klidenkern@gmail.com'
);
insert into users (
  name, email
) values (
	'Alex', 'rocklimber@gmail.com'  
);
insert into users (
  name, email
) values (
  'Slade', 'adelindeltoro@gmail.com'
);
insert into orders(
	order_id
) values (
	1
);
insert into orders(
	order_id
) values (
	2
);
insert into orders(
	order_id
) values (
	3
);
insert into products(
	order_id, price, name
) values (
	3, 8.99, 'Pickles'
);
insert into products(
	order_id, price, name
) values (
	2, 124, 'I-Phone'
);
insert into products(
	order_id, price, name
) values (
	1, 31, 'Bike'
);
insert into products(
	order_id, price, name
) values (
	1, 15.26, 'Cheetohs'
);
*** Run queries against your data. ***
**Get all products for the first order.**
select * from products
where order_id = 1;
**Get all orders.**
select * from orders;
**Get the total cost of an order**
select sum(p.price), o.order_id from orders o
join products p on o.order_id = p.order_id
group by o.order_id;
*** Add a foreign key reference from orders to users. ***
alter table orders
add column user_id integer references users(user_id);
*** Update the orders table to link a user to each order. ***
update orders
set user_id = 1
where order_id = 1 or order_id = 2;
update orders
set user_id = 2
where order_id = 3;
*** Run queries against your data. ***
**Get all orders for a user.**
select users.user_id, orders.* from orders
join users on users.user_id = orders.user_id
**Get how many orders each user has.**
select users.user_id, count(*) from orders
join users on users.user_id = orders.user_id
group by users.user_id;

*** BLACK DIAMOND ***
select u.user_id, sum(p.price) from users u
join orders o on o.user_id = u.user_id
join products p on o.order_id = p.order_id
group by u.user_id;




