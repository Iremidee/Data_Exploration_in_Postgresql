# Data Exploration in Postgresql
DVD Rental dataset was used for this data exploration

--How many payment transactions were greater than $5.00?

SELECT COUNT(amount) FROM payment WHERE amount > 5;


--How many actors have a first name that start with letter P?
SELECT COUNT(first_name) FROM actor
WHERE first_name like 'P%'


--How many unique districts are our customers from?

SELECT COUNT(DISTINCT district) FROM address; 


--Retrieve the list of names for those distinct districts from the previous question?

SELECT DISTINCT district FROM address;


--How many films have a rating of R and a replacement cost between $5 & $15

SELECT COUNT(title) FROM film
WHERE rating = 'R' AND replacement_cost between 5 and 15;


--How many films have the word Truman somewhere in the title?

SELECT COUNT (title) FROM film WHERE title like '%Truman%';


--We want to know what customers are eligible for our platinum credit card. The requirements are that the customers 
--has at least a total of 40 transaction payments. What customers are eligible for the credit card?

SELECT customer_id, SUM(amount) FROM payment
GROUP BY customer_id HAVING SUM(amount) > 40;


--When grouped by rating, what movie ratings have an average rental duration of more than 5 days?

SELECT rating, AVG(rental_duration) FROM film
GROUP BY rating HAVING AVG(rental_duration) > 5;


--Identify the top 10 customers and their email so we can reward them

SELECT CONCAT(first_name, ' ', last_name) AS full_name, email, SUM(amount) FROM customer 
INNER JOIN payment ON payment.customer_id = customer.customer_id
GROUP BY 1,2
ORDER BY 3 desc limit 10;


--Identify the bottom 10 customers and their email 

SELECT CONCAT(first_name, ' ', last_name) AS full_name, email, SUM(amount) FROM customer 
INNER JOIN payment ON payment.customer_id = customer.customer_id
GROUP BY 1,2
ORDER BY 3 asc limit 10;


--What are the most profitable movie genres (ratings)

SELECT category.name AS movie_genres, COUNT(customer.customer_id) AS total_demand, SUM(payment.amount) AS total_sales 
FROM category
INNER JOIN film_category ON category.category_id = film_category.category_id
INNER JOIN film ON film_category.film_id = film.film_id
INNER JOIN inventory ON film.film_id = inventory.film_id
INNER JOIN rental ON inventory.inventory_id = rental.inventory_id
INNER JOIN customer ON rental.customer_id = customer.customer_id
INNER JOIN payment ON rental.rental_id = payment.rental_id
GROUP BY 1
ORDER BY 2 DESC;
