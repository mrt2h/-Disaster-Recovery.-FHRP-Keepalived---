# «SQL. Часть 2» - "Хасаншин Марат"
### Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.
```sql
select	concat(sf.first_name , ' ', sf.last_name) as 'LFMname Employee',
		cy.city,
		COUNT(cr.customer_id) as 'Count Buyers'		
from sakila.store s
join sakila.staff sf on sf.store_id = s.store_id 
join sakila.customer cr on cr.store_id = s.store_id
join sakila.address a on a.address_id = s.address_id 
join sakila.city cy on cy.city_id = a.city_id 
group by sf.staff_id, cy.city_id 
having COUNT(cr.customer_id) > 300;
```

### Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.
```sql
select COUNT(f.title)
from sakila.film f  
where f.`length` > (select AVG(`length`) 
                    from sakila.film);
```

### Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.
```sql
select	t.amount_of_payments,
	t.month_of_payments,
	(select count(r.rental_id)
	from sakila.rental r
	where DATE_FORMAT(r.rental_date, '%M %Y') = t.month_of_payments) 'count_of_rent'
from (
  select SUM(p.amount) 'amount_of_payments', DATE_FORMAT(p.payment_date, '%M %Y') 'month_of_payments' 
  from sakila.payment p 
  group by DATE_FORMAT(p.payment_date, '%M %Y')) t
order by t.amount_of_payments desc  
limit 1;
```

### Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».
```sql
select	t.countpayment count_of_payments,
		t.staff_id staff_id,
		case 
			when t.countpayment > 8000 then 'Да'
			else 'Нет'
		end as bonus
from (	select	COUNT(payment_id) 'countpayment',
				staff_id  
  		from	sakila.payment p 
  		group by	staff_id ) t;
```

### Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.
```sql
select	f.title
from	sakila.rental r
right join	sakila.inventory i on i.inventory_id = r.inventory_id  
right join	sakila.film f  on f.film_id = i.film_id 
where	r.rental_id is null;
```
