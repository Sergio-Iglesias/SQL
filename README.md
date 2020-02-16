En estos apuntes hay muchos ejemplos de código, ya que me ayuda ver código ya hecho para entender como funciona.


### Ejemplo de SQL normal

```
SELECT name, population
FROM world
WHERE name >= 'Germany';
ORDER BY population desc
```

Para mayor o menor, hay que usar primero "<=", no permite hacer "=<". Igualmente, tiene que ser ">=" y no "=>". El igual al final siempre.

Siempre hay que añadir punto y coma al final.

```
SELECT name, population, area
```

Para seleccionar varias columnas se debe separarlas por comas.

```
LIMIT 3
```

Enseña solo las tres primeras filas de la tabla.

```
LIMIT 5,20 
```

Enseña las 15 filas después de saltar las 5 primeras. 
Sería como decirle: "Saca las 20 primeras filas, pero no enseñes las 5 primeras".

```
WHERE name IN ('Brazil', 'Russia', 'India', 'China');
```
Introducimos los datos que queremos que salgan separados por comas.
Equivale a usar varios `Name = ''` separados por OR.

```
ORDER BY population desc
```

Ordena la salida por orden **desc**endiente o **asc**endiente.
Poner más de un ORDER BY sirve para ordenar los que están la misma posición. (Ordenar por clase, ordenar por nombre. Quedarían los alumnos ordenados alfabeticamente por clase).

```
WHERE area BETWEEN 200 AND 300
```

Entre el primer número y el segundo. Incluye los dos valores en los extremos (200 y 300).

```
WHERE name BETWEEN 'A' AND 'B'
```
> ('D' >= pais >='F')

Solo busca la primera letra porque busca hasta "B". Si hay un pais llamado "B" lo pondría. (Nombres que empiezan por "A", no por "B").

```
FROM world AS mundo
```

Renombrar la tabla world por mundo en el código, permitiendo usar un alias de la tabla por comodidad.

> (SELECT mundo.name)

Sería como se usa el alias.

```
WHERE name LIKE '%7'
```

Usando `%` con `LIKE` permite buscar partes solamente. En este ejemplo buscaría nombres que acaben con un 7.
`%` es que acepta cualquier número de caracteres.

```
AND name LIKE '8%'
```

Continuando la anterior, esto buscaría que termine en 7 y que empiece por 8. (Aunque hay mejores maneras de hacer esto, como `LIKE '8%7'`).

```
WHERE LOWER(first_name) LIKE 'an%'
```

Poner LOWER hace que el texto en `first_name` sea transformado en minúsculas (A menos que ya estea en minúsculas).


` % `
Cualquier carácter y número de caracteres, incluso ninguno.

` _ `
Un carácter cualquiera, pero obligatorio uno.

```
WHERE CONCAT(name, ' City') LIKE capital
```

`CONCAT name +" City"` junta lo que saca de `name` con " City".

```
SELECT name, REPLACE(capital, name, '') AS ext
```

REPLACE: En  A, cambiar B por C. En el ejemplo, coje `name` y lo cambia por nada en `capital`. Mexico y Mexico City quedaría en " City".

```
SELECT DISTINCT continent
```

DISTINCT: Selecciona solo los continentes diferentes, para no seleccionarlos mßs de una vez. Si un SELECT sacaría la misma cosa 5 veces, DISTINCT hace que salga una vez solo.

```
WHERE continent = "South America"

WHERE name BETWEEN 'D' and 'F';
```

Es:

```
WHERE name >= 'Denmark'
AND name <= 'F';
```

Si se quiere no cojer los extremos hay que usar la segunda forma, cambiando los mayor/menor e igual que por solo mayor/menor que.

```
WHERE name IN ('Sweden', 'Norway', 'Denmark');
```

Es:

```
WHERE name = 'Sweden'
OR name = 'Norway'
OR name = 'Denmark';
```

```
WHERE area > 3000000
XOR population > 250000000;
XOR: Uno o otro, pero no los dos a la vez.
```

Es:

```
WHERE (area > 3000000 OR population > 250000000)
AND NOT (area > 3000000 AND population > 250000000);
```

```
SELECT name,
ROUND(population/1000000,2),
ROUND(gdp/1000000000,2)
```

ROUND: Cosa a redondear/número para dividir, número de decimales

LENGTH: Cuenta caracteres y lo pone como número.

<>: Que no sea igual, no se escribe como '!='

LEFT: (cosa de donde quieres coger caracteres, número de caracteres)


```
WHERE continent = 'Europe'
AND gdp / population > (SELECT gdp / population
FROM world
WHERE name LIKE 'United Kingdom');
```

Se puede poner SELECT dentro de WHERE.


Una variable sería:

```
var GermanyPopulation = SELECT population
FROM world
WHERE name = 'Germany'
```

Ahora se coge todo menos la variable y se pone donde quieres.


Avanzada:

```
SELECT name, CONCAT(
                   ROUND(
                       100 * population / (SELECT population
                       FROM world
                       WHERE name = 'Germany'),
                   0),
             '%')
FROM world
WHERE continent = 'Europe';
```

All: hace una lista con las respuestas, en el ejemplo se usa la lista para comprobar el gdp contra ella.

```
SELECT name
FROM world
WHERE gdp > ALL(SELECT gdp
                FROM world
                WHERE gdp>0
                AND continent = 'Europe');
```

Ejercicio de Ejemplo:

```
SELECT DISTINCT InWorld.continent, (SELECT OutWorld.name
          FROM world AS OutWorld
          WHERE InWorld.continent = OutWorld.continent
       -- AND name IS NOT NULL
          ORDER BY OutWorld.name ASC
          LIMIT 1)
FROM world as InWorld;
```

Versión del profesor:

```
SELECT continent, name
FROM world AS A 
WHERE name <= ALL (
    SELECT name
    FROM world AS B
    WHERE A.continent = B.continent
 -- AND name IS NOT NULL
);
```

Ejemplo de usar un ORDER BY doble:

```
ORDER BY continent ASC, name ASC
```

```
3 >= (
    SELECT name
    FROM world as B
    WHERE A.population / B.population
    AND A.population != B.population);
```

```
SELECT name, continent
FROM world as A
WHERE population > ALL(
    SELECT B.population * 3
    FROM world AS B
    WHERE A.continent = B.continent
    AND NOT A.name = B.name);
```


```
SELECT game.team1, game.team2, goal.player
from game JOIN goal
ON game.id = goal.matchid
WHERE player LIKE 'Mario%'
```

Los SELECT no necesitan siempre tener antes de que tabla son, pero ayuda mucho.

El JOIN se pone en FROM.

"ON" se usa para indicar que filas en la 1 van con las filas de la 2, tienen que ser iguales ambos.

Se puede usar "AS" en FROM y JOIN para facilitar la escritura (Ga.id en vez de game.id)

```
SELECT game.stadium, COUNT(*) AS 'goals'
FROM game JOIN goal on game.id=goal.matchid
GROUP BY game.stadium;
```

```
SELECT matchid, mdate, COUNT(goal.player)
FROM goal JOIN game ON matchid = id 
WHERE (team1 = 'POL' OR team2 = 'POL')
GROUP BY goal.matchid, game.mdate;
```

GROUP BY necesita todos los atributos.

```
SELECT actor.name
FROM actor JOIN casting ON casting.actorid=actor.id 
WHERE casting.movieid = ALL(SELECT id
                            FROM movie
                            WHERE title='Casablanca');
```

```
SELECT actor.name
FROM actor JOIN casting ON casting.actorid=actor.id
	   JOIN X 
WHERE casting.movieid='11768'
```

```
SELECT movie.yr, COUNT(movie.title) AS movies
FROM movie JOIN casting ON movie.id = casting.movieid
           JOIN actor   ON actor.id = casting.actorid
WHERE actor.name = 'Rock Hudson'
GROUP BY movie.yr
HAVING COUNT(movie.title) > 2;
```

```
SELECT DISTINCT A1.name
FROM actor AS A1 JOIN casting as C1
                 ON A1.id = C1.actorid
                 JOIN casting as C2
		 ON C1.movieid = C2.movieid
		 JOIN actor AS A2
                 ON A2.id = C2.actorid
WHERE A2.name = 'Art Garfunkle'
      AND A2.id <> A1.id;
```
	  
	  
Externo - Conceptual - Interno

BACHMAN - Entidad relación. (Conceptual).

Entidad relación extendido tiene relaciones débiles.

1ª Forma normal: No valores multivalorados.
2ª Forma normal: Dependecia completa de la clave
3ª Forma normal: No dependencias transitivas

Independecia lógica es cuando los usuarios no tengan que sufrir las caidas por cambios.

```
SELECT teacher.name
FROM teacher
WHERE teacher.dept IS NULL;
```

Obetener los departamentos NULL.

```
SELECT teacher.name
FROM teacher
WHERE teacher.name =    'S%' -- 'S%' CADENA             - TIENE QUE SER S%
  OR  teacher.name LIKE 'S%' -- 'S%' EXPRESION REGULAR  - TIENE QUE EMPEZAR POR S
```

INNER JOIN es igual a JOIN

LEFT JOIN es: "Sácame los de la izquierda, aunque sean nulos."

RIGHT JOIN es: "Sácame los de la izquierda, aunque sean nulos."


```
SELECT teacher.name, dept.name
 FROM teacher LEFT JOIN dept ON (teacher.dept=dept.id)
UNION
SELECT teacher.name, dept.name
 FROM teacher LEFT JOIN dept ON (teacher.dept=dept.id);
```

```
 SELECT teacher.name, dept.name
FROM teacher RIGHT JOIN dept ON (teacher.dept=dept.id)
```

Se puede hacer con un left join si se cambia `teacher.dept=dept.id` y se pone al revés.

```
SELECT teacher.name, COALESCE(teacher.mobile, '07986 444 2266')
FROM teacher;
```

COALESCE: Cambia NULL en un campo a otro valor.

```
SELECT 
COUNT(teacher.name), 
COUNT(teacher.mobile)
FROM teacher;
```

COUNT: Contar filas (Es un reductor).

```
SELECT stops.id, stops.name
FROM stops JOIN route ON stops.id = route.stop
WHERE route.company = 'LRT' 
  AND route.num     = '4';
```

```
SELECT actor1.name
FROM actor AS actor1 JOIN actor AS actor2
WHERE actor2.name = 'Art Garfunkel';
```

```
SELECT actor.name
FROM actor
WHERE actor.name IN (

	SELECT actor.name
	FROM actor
	WHERE Actor.name = 'Art Garfunkel');
```

SQL es una lenguaje declarativa

## Sub-Lenguajes de SQL 

 DDL Data Definition Language - Importante

>	CREATE, ALTER, DROP

 DML Data Manipulation Language   - Importante

>	INSERT, UPDATE, DELETE

 DCL Data Control Language

>	GRANT, REVOKE, AUDIT, COMMENT

 TCL Transaction Control Language

>	COMMIT, ROLLBACK SAVEPOINT

 DQL Data Query Language  - Importante

>	SELECT

 SCL Session Control Language

>	ALTER SESSION, SET ROLL

	
Añadir `(link)?answer=1` al final de la url de SQLZoo permite ver las respuestas.
