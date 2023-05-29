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

