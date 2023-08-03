## Udemy Course: "Introduction to Spatial Databases with PostGIS and QGIS 3"

### Instructor: Michael Miller

https://www.udemy.com/course/introduction-to-spatial-databases-with-postgis-and-qgis/

## Summary

Hands on kursus om PostGIS & QGIS

## Den røde tråd

### Section 1: Introduction

Han introducerer spatielle databaser og rationaler for at bruge dem.

### Section 2: Installing software and loading data

Han demonstrerer, hvordan man installerer en PostgreSQL server og Postgis som add-on. Han demonstrerer også, hvordan man installerer QGIS

1. Installer en PostgreSQL server. I forbindelse med installationen har man mulighed for at køre programmet Stack Builder, som bruges til at installere ekstra software til sin Postgres server. Man kan også køre Stack Bulder for sig selv for at tilføje PostGIS komponenter til en eksisterende installation af PostgreSQL.
2. Opret på sædvanlig vis en ny database på Postgres serveren
3. Åbn en query editor i pgadmin og eksekver: `CREATE EXTENSION postgis`. Bemærk i pgadmins Browser-view hvordan der tilføjes en extension for databasen samt et antal functions (under Schema).
4. Installer QGIS
5. Åbn et nyt projekt i QGIS, importer et antal shape files som nye layers ved at dragge dem fra QGISs Browser view hen til Layers panelet.
6. Importer også et antal excel spreedsheets med non-spatielle data til QGIS

### Section 3: Data Query Language - SQL queries for non-spatial data

#### 3.1 The SQL SELECT statement

```
SELECT * FROM baea_nests
SELECT * FROM baea_surveys
SELECT id, nest, user, date, result FROM baea_surveys

SELECT id, nest, user, date, result FROM raptor_surveys ORDER BY nest, date DESC

-- Bemærk at alle de kommaseparerede elementer efter SELECT ovenfor reelt er EXPRESSIONS, dvs noget ~der giver en værdi
-- Det kan vi udnytte til at lave en mere kompleks query alla det her:

SELECT id * 2, nest, user, date, LEFT(result, 3) FROM raptor_surveys ORDER BY nest, date DESC

-- Udtrykket id * 2 påvirker ikke, hvilke rækker, der vælges, men det viser dem blot ganget med 2 i output tabellen.
-- Det giver ikke så meget mening i dette eksempel men kan gøre det i andre tilfælde.
-- Funktionen LEFT viser resultatet trunkeret til de første 3 karakterer

-- Hvis vi gør som ovenfor får vi ikke nogle sigende kolonnenavne, men det kan vi rette op på ved at bruges aliases som nedenfor
SELECT id * 2 AS idx2, nest, user, date AS survey_date, LEFT(result, 3) AS result_code
FROM raptor_surveys ORDER BY nest, date DESC

-- Her dirigerer vi resultatet ind i en ny tabel frem for bare at vise det på skærmen..
-- (det bruges vist ikke så tit i praksis)
SELECT id * 2 AS idx2, nest, user, date AS survey_date, LEFT(result, 3) AS result_code
INTO raptor_surveys_sorted
FROM raptor_surveys ORDER BY nest, date DESC

-- Bemærk at man også, frem for at selecte fra en tabel, kan selecte fra outputtet fra et andet select statement,
-- hvilket man så placerer inden for paranteser. Man kan embedde vilkårligt mange sql statements på denne måde.
-- For at det skal virke, skal subqueryen have et alias.
SELECT idx2, survey_date, result_code 
FROM
(   SELECT id * 2 AS idx2, nest, user, date AS survey_date, LEFT(result, 3) AS result_code
    FROM raptor_surveys 
    ORDER BY nest, date DESC
) AS subq
```

#### 3.2 The WHERE clause

```
-- Nu leger vi med where clauses. Bemærk, at vi har sat user-feltet i anførselstegn for ikke at forveksle det med
-- det reserverede ord user
SELECT nest, "user", date, result 
FROM raptor_surveys 
WHERE "user" = 'Mike Miller' 
AND nest > 200 
AND date > '2015-07-01' 
--AND result LIKE 'ACT%' -- % er et wildcard, der repræsenterer et ANTAL karakterer
--AND result LIKE '%ACT%' -- % kan placeres flere steder i filteret
--AND result LIKE 'A_TI_E NEST' -- _ (underscore) repræsenterer ÉN karakter i filteret
--AND result NOT LIKE 'ACT%' -- Man kan også negere et filter med NOT operatoren
AND result IN ('ACTIVE NEST', 'FLEDGED NEST') -- Med IN operatoren skal feltet passe med en værdi fra listen 
ORDER BY nest, date DESC

-- Operatoren DISTINCT kan bruges til at hente de unikke værdier for et felt
SELECT DISTINCT result FROM raptor_surveys
```

#### 3.3 Aggregate functions and the GROUP BY clause

```
SELECT COUNT(*) FROM raptor_surveys
SELECT COUNT(*), AVG(nest), MAX(date), MIN("user") FROM raptor_surveys

-- Der er også statistiske aggregate functions såsom: 
--   STDEV(expression)
--   VARIANCE(expression)
--   CORR(expression1, expression2)
--   COVAR(expression1, expression2)

-- Aggregate functions can be combined with the GROUP BY clause 
SELECT COUNT(*) FROM raptor_surveys GROUP BY nest
SELECT nest, COUNT(*) FROM raptor_surveys GROUP BY nest
SELECT nest, COUNT(*) FROM raptor_surveys GROUP BY nest ORDER BY nest
SELECT nest, COUNT(*) FROM raptor_surveys WHERE "result"='ACTIVE NEST' GROUP BY nest ORDER BY nest

-- Man kan også lave subgruppering som her
SELECT nest, "result", COUNT(*) 
FROM raptor_surveys
GROUP BY nest, "result"
ORDER BY nest, "result"
```

#### 3.4 Multi-table Queries Part 1

```
-- Here, we join data from different tables based on a common field
SELECT * FROM raptor_surveys, raptor_nests WHERE nest=nest_id

-- Cardinality of a join
--   One-to-One
--   One-to-Many
--   Many-to-Many (with a join table)

-- Dette virker men er ikke den bedste praksis
-- ..man kan opnå bedre flexibilitet og kontrol ved at benytte sig af JOIN clauses
SELECT n.id, s.nest, n.nest_id, s.user, s.date, s.result, n.recentspec
FROM raptor_surveys s, raptor_nests n
WHERE s.nest = n.nest_id AND s.result != 'ACTIVE NEST'
ORDER BY n.nest_id
```

#### 3.5 Multi-table Queries Part 2

```
-- INNER JOIN
-- NB: Just writing JOIN corresponds to INNER JOIN, which is the default
-- "For an inner join, records without any matches are ignored" - dvs en parent uden children medtages ikke
SELECT n.id, s.nest, n.nest_id, s.user, s.date, s.result, n.recentspec
FROM raptor_surveys s 
JOIN raptor_nests n ON s.nest = n.nest_id
ORDER BY n.nest_id

-- OUTER JOIN comes in 3 flavors:
-- - LEFT OUTER JOIN: 
--     Som inner join, men hvor man medtager rækker fra VENSTRE side (sædvanligvs parent siden), selv om de 
--     ikke har noget match på højresiden. For rækker uden matches skrives blot null i højre side.
SELECT n.id, s.nest, n.nest_id, s.user, s.date, s.result, n.recentspec
FROM raptor_surveys s 
LEFT OUTER JOIN raptor_nests n ON s.nest = n.nest_id
ORDER BY n.nest_id
-- - RIGHT OUTER JOIN
--     Som inner join, men hvor man medtager rækker fra HØJRE side (sædvanligvs child siden), selv om de 
--     ikke har noget match på venstresiden. For rækker uden matches skrives blot null i venstre side.
SELECT n.id, s.nest, n.nest_id, s.user, s.date, s.result, n.recentspec
FROM raptor_surveys s 
RIGHT OUTER JOIN raptor_nests n ON s.nest = n.nest_id
ORDER BY n.nest_id
-- - FULL OUTER JOIN
--     En kombination af left og right outer join, hvor man medtager rækker fra begge sider, selv om de ikke
--     har nogen matches
SELECT n.id, s.nest, n.nest_id, s.user, s.date, s.result, n.recentspec
FROM raptor_surveys s 
FULL OUTER JOIN raptor_nests n ON s.nest = n.nest_id
ORDER BY n.nest_id
-- Man kan f.eks. bruge outer joins til quality control for at checke om man har orphants eller business rule
-- violations, f.eks. hvor en parent skal have mindst et child
-- Man har også noget, der hedder CROSS JOIN, som ikke bruges så tit i praksis. Her matcher man hver række på
-- venstre side med hver række på højre side. Det ka altså resultere i rigtig mange rækker i outputtet
```

#### 3.6 Data Types in PostgreSQL Part 1

Han gennemgår de indbyggede datatyper, der er i Postgres: boolean, smallint, integer, bigint, serial, real, double precision, numeric, money, date, time, timestamp, interval, char(n), varchar(n), text

#### 3.7 Data Types in PostgreSQL Part 2

Denne lektion handler om **casting** og det at man med PostgreSQL kan definere sine egne datatyper. Et cast kan være implicit, eller explicit. Man kan i øvrigt lave user defined casts. Bemærk i øvrigt i pgadmin, at man, når man extender en database med PostGIS pluginnet, får et et antal types såsom box2d, geography, geometry, raster osv. Det kan ses ved at expande "Types"-noden under Schemas-noden for databasen i browser-panelet i pgadmin. Man får også et antal casts, hvilket fremgår af "Casts"-noden lige under Database-noden.

```
SELECT 123456789012345 -- castes til bigint
SELECT 1234567890.12345 -- castes til numeric
SELECT 1234567890.12345::real -- castes til real
SELECT 1234567890.12345::double precision -- castes til double precision
SELECT 1234567890.12345::numeric -- castes til numeric - den er i øvrigt ret præcis i forhold til f.eks. real
SELECT '2015-10-23'::date -- castes til date
SELECT '2015-10-23'::timestamp -- castes til date
SELECT '2015-10-23 13:02:12'::timestamp -- castes til date
SELECT '1234567.8901'::numeric::integer -- castes først til en numeric og derefter til en int
```

#### 3.8 The FROM clause implies a loop

Dette er en kort lektion, hvor han pointerer, at FROM klausulen effektivt kører et loop. Det indebærer f.eks. at man kan gøre noget som vidt nedenfor:

```
SELECT 5 FROM raptor_nests
SELECT random() FROM raptor_nests
```

.. hvor det, der står efter SELECT ikke har noget med tabellen efter FROM at gøre, men blot returneres et antal gange svarende til antallet af rækker, der returneres fra FROM-delen.

#### 3.9 SQL Expressions and Functions Part 1

"An **expression** is a combination constants, variables, operators, and functions that produces a value".

Examples of expressions:

* 1 + 2
* age * 2
* '2015-06-01'::date + 5
* left(result, 3)

"A **function** is a subroutine that returns a value"

Examples of functions:

```
SELECT random() -- random number between 0 and 1
SELECT upper('active nest') -- capitalizes text
SELECT mod(11, 3) -- modulus
SELECT right('ACTIVE NEST', 4) -- Returns 'NEST'
SELECT substring('ACTIVE NEST', 4) -- Returns 'IVE NEST'
SELECT substring('ACTIVE NEST', 4, 3) -- Returns 'IVE'
```

"An **operator** is a construct that generally behaves like a function, but which differ syntactically or semantically from usual functions"

Examples of operators:

```
SELECT 1 + 2 -- Here, plus is an operator
SELECT '1975-07-24'::date + 3 -- Here, plus is an operator that interprets the number as a number of days
SELECT ('2015-03-01'::date, '2015-04-01'::date) OVERLAPS ('2015-03-15'::date, '2015-06-01'::date) -- Here, OVERLAPS is an operator
```

Man kan relativt let lave sine egne funktioner, f.eks. således, hvor man bruger generel SQL:

```
CREATE FUNCTION add(integer, integer) RETURNS integer
	AS 'SELECT $1 + $2;'
	LANGUAGE SQL
	IMMUTABLE
	RETURN NULL ON NULL INPUT
```

Nedenfor gør vi det samme, men under anvendelse af sproget PL/pgSQL ("Procedural Language for PostgreSQÆ"), som er inkluderet i PostgreSQL, og som giver noget mere flexibilitet end hvis man bare bruger generel SQL:

```
CREATE OR REPLACE FUNCTION add(inp1 integer, inp2 integer) RETURNS integer as $$
	BEGIN
		RETURN inp1 + inp2;
	END;
$$ LANGUAGE plpgsql;
```

Efter at have lavet sådan en funktion kan man f.eks. eksekvere: `SELECT add(3, 4);`

Han tilføjer, at man kan bruge mange forskellige programmeringssprog til at lave sine egne funktioner, f.eks. Python, Perl osv

#### 3.10 SQL Expressions and Functions Part 2

I denne lektion kigger vi på nogle af de *mange* indbyggede funktioner i PostgreSQL. Det er også intentionen at lære at bruge dokumentationen, som er her: https://www.postgresql.org/docs/current/functions.html.

Logical operators: AND, OR, NOT

Comparison operators: <, >, <=, >=, =, <> (corresponds to !=), BETWEEN osv

Mathematical operators: +, -, *, /, %, ^, osv

Mathematical functions: abs, ceil, floor, round, exp, ln, pi, sin, cos osv

#### 3.11 The CASE Statement

Princip:

```
CASE 
	WHEN predicate1 THEN expression1 -- Hvis predicate 1 evaluerer til true, returneres expression1
	WHEN predicate2 THEN expression2 -- Eller - hvis predicate 2 evaluerer til true, returneres expression2	
    WHEN predicate3 THEN expression2 -- Eller - hvis predicate 3 evaluerer til true, returneres expression3
    osv
    ELSE expression5 -- denne returneres hvis alle predicates evaluerer til false
```

Eksempel på brug:

```
SELECT 
	nest_id,
	long_x_dd,
	CASE
		WHEN long_x_dd < -104.9 THEN 'West'
		WHEN long_x_dd > -104.5 THEN 'East'
		ELSE 'Center'
	END AS location, 
	status 
FROM baea_nests		
```

#### 3.12 SQL Views











