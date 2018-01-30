## Homework Assignment

### Loading the sample database for this homework

For this homework, we'll be using a sample database, with sample data provided by MySQL. Before moving on, please [follow these instructions](https://docs.google.com/document/d/1JAWfwmCDsq6lJsZcatMriz0ImHX_uVdxVryp8ZGr4SQ/) to install this sample DB. If you're having trouble with the installation, please post your questions in #questions so that other members of the class can help.

### Formatting your homework submission

Once you've successfully loaded the data in the `sakila` DB into MySQL, solve each of the below problems, writing the SQL for each solution and recording it in a file. Keeping these solutions in a Markdown file (e.g. like a `README.md` file) can be convenient for formatting your SQL code. So your final solution could look something like this:

1a)

    SELECT blah1, blah1
    FROM the_table

1b)

    SELECT more_fields
    FROM another_table

and so on. Please checkout this file with `git` onto your local machine, and examine this Markdown file in Visual Studio to see how we achieved this formatting. Feel free to pick whatever format works for you, as long as it's clear what SQL corresponds to what problem.

### Problems

* 1a. Display the first and last names of all actors from the table `actor`. 

select first_name, last_name from actor;

* 1b. Display the first and last name of each actor in a single column in upper case letters. Name the column `Actor Name`. 

alter table actor add column Actor_Name VARCHAR(50);
UPDATE actor set Actor_Name = CONCAT(first_name, ' ',last_name);

* 2a. You need to find the ID number, first name, and last name of an actor, of whom you know only the first name, "Joe." What is one query would you use to obtain this information?

select first_name, last_name, actor_id from actor
where first_name = 'JOE';
  	
* 2b. Find all actors whose last name contain the letters `GEN`:

select last_name from actor
where last_name like '%GEN%';
  	
* 2c. Find all actors whose last names contain the letters `LI`. This time, order the rows by last name and first name, in that order:

select first_name, last_name from actor
where last_name like '%LI%'
order by first_name, last_name;

* 2d. Using `IN`, display the `country_id` and `country` columns of the following countries: Afghanistan, Bangladesh, and China:

select country_id, country from country
where country in ('Afghanistan', 'Bangladesh', 'China');

* 3a. Add a `middle_name` column to the table `actor`. Position it between `first_name` and `last_name`. Hint: you will need to specify the data type.

alter table actor
add column middle_name VARCHAR(50) 
after first_name;
  	
* 3b. You realize that some of these actors have tremendously long last names. Change the data type of the `middle_name` column to `blobs`.

ALTER TABLE actor
MODIFY COLUMN middle_name blob;

* 3c. Now delete the `middle_name` column.

ALTER TABLE actor
DROP COLUMN middle_name;

* 4a. List the last names of actors, as well as how many actors have that last name.

select count(last_name),last_name from actor
group by last_name;
  	
* 4b. List last names of actors and the number of actors who have that last name, but only for names that are shared by at least two actors

select count(last_name) as cn,last_name from actor
group by last_name
having cn > 1;
  	
* 4c. Oh, no! The actor `HARPO WILLIAMS` was accidentally entered in the `actor` table as `GROUCHO WILLIAMS`, the name of Harpo's second cousin's husband's yoga teacher. Write a query to fix the record.

	update actor
	set first_name = 'HARPO'
	where first_name = 'GROUCHO' and last_name = 'WILLIAMS';
  	
* 4d. Perhaps we were too hasty in changing `GROUCHO` to `HARPO`. It turns out that `GROUCHO` was the correct name after all! In a single query, if the first name of the actor is currently `HARPO`, change it to `GROUCHO`. Otherwise, change the first name to `MUCHO GROUCHO`, as that is exactly what the actor will be with the grievous error. BE CAREFUL NOT TO CHANGE THE FIRST NAME OF EVERY ACTOR TO `MUCHO GROUCHO`, HOWEVER! (Hint: update the record using a unique identifier.)

update actor
set first_name = 
(	case when first_name = 'HARPO' then 'GROUCHO'
	else 'MUCHO GROUCHO'
    end
)
Where actor_id = 172;

* 5a. You cannot locate the schema of the `address` table. Which query would you use to re-create it? HINT: [CHECK THIS OUT](https://dev.mysql.com/doc/refman/5.7/en/show-create-table.html)

SHOW CREATE TABLE address;

* 6a. Use `JOIN` to display the first and last names, as well as the address, of each staff member. Use the tables `staff` and `address`:

select s.first_name, s.last_name, a.address
from staff s
join address a on
a.address_id = s.address_id;

* 6b. Use `JOIN` to display the total amount rung up by each staff member in August of 2005. Use tables `staff` and `payment`. 

select s.first_name, s.last_name, sum(p.amount) as total
from staff s
join payment p on
p.staff_id = s.staff_id
WHERE
	EXTRACT(month FROM payment_date) = 8 AND 
    EXTRACT(year FROM payment_date) = 2005
group by s.staff_id;
  	
* 6c. List each film and the number of actors who are listed for that film. Use tables `film_actor` and `film`. Use inner join.

select f.title, count(a.actor_id) as counts
from film f
join film_actor a on
a.film_id = f.film_id
group by f.title;
  	
* 6d. How many copies of the film `Hunchback Impossible` exist in the inventory system?

select f.title, count(i.film_id) as total
from film f
join inventory i on
i.film_id = f.film_id
where f.title = 'HUNCHBACK IMPOSSIBLE'
group by f.title;

* 6e. Using the tables `payment` and `customer` and the `JOIN` command, list the total paid by each customer. List the customers alphabetically by last name:

select sum(p.amount), c.first_name, c.last_name
from payment p
join customer c on
p.customer_id = c.customer_id
group by c.customer_id
order by c.last_name;

  ```
  	![Total amount paid](Images/total_payment.png)
  ```

* 7a. The music of Queen and Kris Kristofferson have seen an unlikely resurgence. As an unintended consequence, films starting with the letters `K` and `Q` have also soared in popularity. Use subqueries to display the titles of movies starting with the letters `K` and `Q` whose language is English. 

select f.title from film f
join language l on 
l.language_id = f.language_id
where (f.title like 'Q%' or f.title like 'K%')
and l.name = 'English';

* 7b. Use subqueries to display all actors who appear in the film `Alone Trip`.

select first_name, last_name from (select f.title, a.actor_id, c.first_name, c.last_name
from film_actor a
left outer join film f on
a.film_id = f.film_id
left outer join actor c on 
c.actor_id = a.actor_id) as alias
where title = 'Alone Trip';


   
* 7c. You want to run an email marketing campaign in Canada, for which you will need the names and email addresses of all Canadian customers. Use joins to retrieve this information.

select c.first_name, c.last_name, c.email from customer c
join address a on c.address_id = a.address_id
join city i on i.city_id = a.city_id
join country u on u.country_id = i.country_id
where country = 'Canada';

* 7d. Sales have been lagging among young families, and you wish to target all family movies for a promotion. Identify all movies categorized as famiy films.

select f.title, a.name from film f
join film_category c on
f.film_id = c.film_id
join category a on
a.category_id = c.category_id
where a.name = 'Family';

* 7e. Display the most frequently rented movies in descending order.

select title, count from (select f.title, count(title) as count from film f 
join inventory i on i.film_id = f.film_id
join rental r on r.inventory_id = i.inventory_id
group by f.title) as alias
order by count desc;
  	
* 7f. Write a query to display how much business, in dollars, each store brought in.

 select st.store_id, concat('$',format(sum(amount),2)) as total_business
  from store st
  join customer c
 using (store_id)
  join payment  p
    on (c.customer_id = p.customer_id)
group by (st.store_id);

* 7g. Write a query to display for each store its store ID, city, and country.

select store_id, city, country
from store s
join address a on s.address_id = a.address_id
join city i on i.city_id = a.city_id
join country u on u.country_id = i.country_id;
  	
* 7h. List the top five genres in gross revenue in descending order. (**Hint**: you may need to use the following tables: category, film_category, inventory, payment, and rental.)

select c.name, sum(p.amount) as gross from category c
join film_category f on f.category_id = c.category_id
join inventory i on f.film_id = i.film_id
join rental r on i.inventory_id = r.inventory_id
join payment p on p.rental_id = r.rental_id
group by name
order by gross desc limit 5;
  	
* 8a. In your new role as an executive, you would like to have an easy way of viewing the Top five genres by gross revenue. Use the solution from the problem above to create a view. If you haven't solved 7h, you can substitute another query to create a view.

create view `CEO` as select c.name, sum(p.amount) as gross from category c
join film_category f on f.category_id = c.category_id
join inventory i on f.film_id = i.film_id
join rental r on i.inventory_id = r.inventory_id
join payment p on p.rental_id = r.rental_id
group by name
order by gross desc limit 5;
  	
* 8b. How would you display the view that you created in 8a?

select * from `CEO`;

* 8c. You find that you no longer need the view `top_five_genres`. Write a query to delete it.

DROP VIEW `CEO`;

### Appendix: List of Tables in the Sakila DB

* A schema is also available as `sakila_schema.svg`. Open it with a browser to view.

```sql
	'actor'
	'actor_info'
	'address'
	'category'
	'city'
	'country'
	'customer'
	'customer_list'
	'film'
	'film_actor'
	'film_category'
	'film_list'
	'film_text'
	'inventory'
	'language'
	'nicer_but_slower_film_list'
	'payment'
	'rental'
	'sales_by_film_category'
	'sales_by_store'
	'staff'
	'staff_list'
	'store'
```

