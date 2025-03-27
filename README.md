# Project-data-analytics-Sql
 SQL Capstone Project
Please answer the following questions using Airline DB database.
Instruction to attempt questions:  Students need to write queries for the questions mentioned in the using Airline DB database Read the questions carefully before writing the query in Airline Playground (in the Playground
chapter of SQL)  Airline DB: https://www.skillovilla.com/playground/sql?exerciseId=0181e251-6ea8-4595-ae2b0c690119f8db
How to submit the capstone:  Copy the SQL query code and paste it in the answer section in this file.  Once the assignment is done, submit the file over LMS.
Invalid Submissions:  Pasting pictures of the code as answer is NOT acceptable.  Uploading output data (CSVs) of the SQL queries is NOT acceptable.
Write your answers(query) in the answer and submit it. To write the answer in the
assignment, please follow the below example in yellow
Example:
Questions: Extract all the columns of the flights table
Answer: SELECT * FROM flights
 Attempt the following Questions1. Represent the “book_date” column in “yyyy-mmm-dd” format using Bookings table
Expected output: book_ref, book_date (in “yyyy-mmm-dd” format) , total amount
Answer: select
 book_ref,
 to_char (book_date, 'YYYY-MMM-DD') as book_date, total_amount
 from bookings;
2. Get the following columns in the exact same sequence.
Expected columns in the output: ticket_no, boarding_no, seat_number, passenger_id,
passenger_name.
 SQL Capstone Project
Answer: select
 b.ticket_no,
 b.boarding_no,
 b.seat_no,
 t.passenger_id,
 t.passenger_name
 from boarding_passes b
 join tickets t
 on b.ticket_no = t.ticket_no
3. Write a query to find the seat number which is least allocated among all the seats?Answer: select
 seat_no
 from boarding_passes
 group by seat_no
 order by count (*) asc
 limit 1
4. In the database, identify the month wise highest paying passenger name and
passenger id.
Expected output: Month_name(“mmm-yy” format), passenger_id, passenger_name and total
amount
Answer: select
 to_char (book_date, 'mmm-yy') as month_name,
 t.passenger_id,
 t.passenger_name,
 sum (b.total_amount) as total_amount
 from bookings b
 join tickets t
 on b.book_ref = t.book_ref
 group by to_char (book_date, 'mmm-yy'), t.passenger_id,t.passenger_name
 order by month_name, total_amount desc;
 SQL Capstone Project
5. In the database, identify the month wise least paying passenger name and
passenger id?
Expected output: Month_name(“mmm-yy” format), passenger_id, passenger_name and total
amount
Answer: select month_name, passenger_id, passenger_name,
total_amount
 From
 (select
 to_char (book_date, 'mmm-yy') as month_name,
 t.passenger_id,
 t.passenger_name,
 sum (b.total_amount) as total_amount,
 rank () over (partition by to_char(book_date,'mmmyy') order by sum (b.total_amount)) as rank
 from bookings b
 join tickets t
on b.book_ref = t.book_ref
 group by to_char (book_date, 'mmm-yy'), t.passenger_id,t.passenger_name
 ) as ranked_passengers
 where rank = 1;
6. Identify the travel details of non stop journeys or return journeys (having more than
1 flight).
Expected Output: Passenger_id, passenger_name, ticket_number and flight count.
Answer: select
 t.passenger_id,
 t.passenger_name,
 b.ticket_no,
 Count (b.flight_id) as flight_count
 from tickets t
join boarding_passes b
on t.ticket_no = b.ticket_no
 group by 1, 2 , 3
 having count (b.flight_id) > 1
 SQL Capstone Project
7. How many tickets are there without boarding passes?
Expected Output: just one number is required.
Answer: select
 count (*) as ticket_count
 from tickets t
 left join boarding_passes b
 on t.ticket_no = b.ticket_no
 where boarding_no is null
output- 251
8. Identify details of the longest flight (using flights table)?
Expected Output: Flight number, departure airport, arrival airport, aircraft code and durations.
Answer: select
 flight_no,
 departure_airport,
 arrival_airport,
 aircraft_code,
 (scheduled_arrival - scheduled_departure) as
duration
 from flights
 order by 5 desc
 limit 1;
9. Identify details of all the morning flights (morning means between 6AM to 11 AM,
using flights table)?
Expected output: flight_id, flight_number, scheduled_departure, scheduled_arrival and timings. Answer: select
 flight_id,
 flight_no,
 scheduled_departure,
 scheduled_arrival,
 CAST(scheduled_departure AS time) as timing
 FROM flights
 WHERE CAST(scheduled_departure AS time) BETWEEN '06:00:00'AND'11:00:00'
 SQL Capstone Project
10. Identify the earliest morning flight available from every airport.
Expected output: flight_id, flight_number, scheduled_departure, scheduled_arrival, departure
airport and timings.
Answer: WITH EarlyMorningFlights AS
(SELECT flight_id,
 flight_no,
 scheduled_departure,
 scheduled_arrival,
 departure_airport,
 CAST(scheduled_departure AS time) as timing,
 ROW_NUMBER() OVER(PARTITION BY departure_airport ORDERBYscheduled_departure) AS row_num
FROM flights
WHERE CAST(scheduled_departure AS time)BETWEEN '06:00:00' AND'11:00:00')
SELECT
flight_id,
flight_no,
scheduled_departure,
scheduled_arrival,
departure_airport,
timing
FROM EarlyMorningFlights
WHERE row_num = 1
11. Questions: Find list of airport codes in Europe/Moscow timezone
Expected Output: Airport_code.
Answer: select
 airport_code
 from airports
 where timezone = 'Europe/Moscow'
 SQL Capstone Project
12. Write a query to get the count of seats in various fare condition for every aircraft code?Expected Outputs: Aircraft_code, fare_conditions ,seat count
Answer: select
 aircraft_code,
 fare_conditions,
 count (Seat_no) as seat_count
 from seats
 group by 1, 2
13. How many aircrafts codes have at least one Business class seats?
Expected Output : Count of aircraft codes
Answer: select
 count (distinct aircraft_code) as count_aircraft_codes from seats
where fare_conditions = 'Business'
14. Find out the name of the airport having maximum number of departure flight
Expected Output : Airport_name
Answer: select
 airport_name,
 count (f.flight_id) as count_flights
 from flights f
 join airports a
 on f.departure_airport = a.airport_code
 group by airport_name
 order by count (f.flight_id) desc
 limit 1;
15. Find out the name of the airport having least number of scheduled departure flights
Expected Output : Airport_name
Answer: select
 airport_name,
 count (f.flight_id) as count_flights
 from flights f
 SQL Capstone Project
 join airports a
 on f.departure_airport = a.airport_code
 group by airport_name
 order by count (f.flight_id) asc
 limit 1;
16. How many flights from ‘DME’ airport don’t have actual departure?
 Expected Output : Flight Count
Answer: select
 count (flight_id) as flight_count
 from flights
 where departure_airport = 'DME' and actual_departure isnull;17. Identify flight ids having range between 3000 to 6000
Expected Output : Flight_Number , aircraft_code, ranges
Answer: SELECT
f.flight_no,
f.aircraft_code,
a.range
FROM flights as f
JOIN aircrafts AS a
ON f.aircraft_code=a.aircraft_code
WHERE a.range BETWEEN 3000 AND 6000
GROUP BY f.flight_no, f.aircraft_code, a.range
ORDER BY a.range
18. Write a query to get the count of flights flying between URS and KUF?
Expected Output : Flight_count
Answer: select
 count (flight_id)
 from flights
 where (departure_airport = 'URS' and arrival_airport = 'KUF')or (departure_airport = 'KUF' AND arrival_airport = 'URS')
19. Write a query to get the count of flights flying from either from NOZ or KRR?
 Expected Output : Flight count
 SQL Capstone Project
Answer: select
 count (flight_id)
 from flights
 where departure_airport in ('NOZ', 'KKR')
20. Write a query to get the count of flights flying from KZN,DME,NBC,NJC,GDX,SGC,VKO,ROVExpected Output : Departure airport ,count of flights flying from these airports.
Answer: select
 departure_airport,
 count (flight_id) as flight_counts
 from flights
 where departure_airport in
('KZN','DME','NBC','NJC','GDX','SGC','VKO','ROV')
 group by departure_airport
21. Write a query to extract flight details having range between 3000 and 6000 and flying from
DME
Expected Output :Flight_no,aircraft_code,range,departure_airport
Answer: SELECT
f.flight_no,
f.aircraft_code,
a.range,
f.departure_airport
FROM flights AS f
JOIN aircrafts AS a
ON f.aircraft_code = a.aircraft_code
WHERE a.range BETWEEN 3000 AND 6000 AND departure_airport = 'DME'GROUP BY f.flight_no,f.aircraft_code, a.range, f.departure_airportORDER BY a.range
22. Find the list of flight ids which are using aircrafts from “Airbus” company and got cancelled
or delayed
Expected Output : Flight_id,aircraft_model
 SQL Capstone Project
Answer: select
 f.flight_id,
 a.model as aircraft_model
 from flights f
 join aircrafts a
 on f.aircraft_code = a.aircraft_code
 where model like '%Airbus%'
 and (status = 'Cancelled' or status = 'Delayed');
23. Find the list of flight ids which are using aircrafts from “Boeing” company and got cancelled
or delayed
Expected Output : Flight_id,aircraft_model
Answer: select
 f.flight_id,
 a.model as aircraft_model
 from flights f
 join aircrafts a
 on f.aircraft_code = a.aircraft_code
 where model like '%Boeing%'
 and (status = 'Cancelled' or status = 'Delayed');
24. Which airport(name) has most cancelled flights (arriving)?
Expected Output : Airport_name
select
 a.airport_name,
 count (*) as cancelled_flights
from flights f
join airports a
on f.arrival_airport = a.airport_code
where status= 'Cancelled'
group by airport_name
order by cancelled_flights desc
limit 1;
25. Identify flight ids which are using “Airbus aircrafts”
Expected Output : Flight_id,aircraft_model
 SQL Capstone Project
Answer: select
 f.flight_id,
 a.model as aircraft_model
from flights f
join aircrafts a
on f.aircraft_code= a.aircraft_code
where model like '%Airbus%'
26. Identify date-wise last flight id flying from every airport?
Expected Output: Flight_id,flight_number,schedule_departure,departure_airport
Answer: WITH LastFlights AS
(SELECT
f.flight_id,
f.flight_no,
f.scheduled_departure,
f.departure_airport,
MAX(scheduled_departure) OVER(PARTITION BY departure_airport,DATE(scheduled_departure)) AS max_scheduled_departure
FROM flights AS f)
SELECT
flight_id,
flight_no,
scheduled_departure,
departure_airport
FROM LastFlights
WHERE scheduled_departure=max_scheduled_departure
ORDER BY 2
27. Identify list of customers who will get the refund due to cancellation of the flights and how
much amount they will get?
Expected Output : Passenger_name,total_refund.
Answer:
28. Identify date wise first cancelled flight id flying for every airport?
Expected Output : Flight_id,flight_number,schedule_departure,departure_airport
Answer: SELECT
 SQL Capstone Project
flight_id,
flight_no,
scheduled_departure,
departure_airport
FROM
(
SELECT
flight_id,
flight_no,
scheduled_departure,
departure_airport,
ROW_NUMBER() OVER(PARTITION BY departure_airport ORDER BY
scheduled_departure ASC) AS m
FROM flights
WHERE status='cancelled') AS t
WHERE m = 1
ORDER BY departure_airport,scheduled_departure
29. Identify list of Airbus flight ids which got cancelled.
Expected Output : Flight_id
Answer: SELECT
f.flight_id
FROM flights f
JOIN aircrafts a
ON f.aircraft_code=a.aircraft_code
WHERE a.model LIKE '%Airbus%' AND f.status='Cancelled'
30. Identify list of flight ids having highest range.
Expected Output : Flight_no, range
Answer: SELECT
f.flight_no,
max(a.range) as range
FROM flights f
JOIN aircrafts a
ON f.aircraft_code=a.aircraft_code
GROUP BY flight_no
