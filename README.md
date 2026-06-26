# activiad-c4-postgres

# Ejercicios de consultas SQL — base `world`



Basado en el script `world-postgres.sql`.



# 



## 1. Países de América con formato de presentación



Consulta los países de `North America` y `South America` cuya población esté entre 1 millón y 100 millones. Muestra el nombre del país en mayúsculas, el continente en minúsculas y una etiqueta construida con el código y el nombre.



---



## 2. Ciudades agrupadas por país



Muestra los países que tienen más de 10 ciudades registradas en la tabla `city`. Para cada país, muestra el código, la cantidad de ciudades y la población total de sus ciudades. Ordená de mayor a menor población total.



select * from city c ;



select countrycode, count(*), sum(population)
from city c
group by countrycode

having count(*) > 10

order by sum(population) desc;







---





## 3. Regiones con alta población promedio



Agrupa los países por región y calcula la población promedio de cada región. Muestrá solo las regiones con promedio superior a 20 millones de habitantes.



select * from country c;



select region, avg(population)

from country c 

group by region

having avg(population) > 20000000;



---



## 4. Nombres limpios y longitud de países



Consulta países cuyo nombre tenga espacios sobrantes o nombres largos. Mostrá el nombre original, el nombre sin espacios laterales y la longitud del nombre limpio.



select * from country c;



select name, TRIM(name), length(trim(name))

from country

where length(name) > 10;





---



## 5. Idiomas oficiales destacados



Consulta los idiomas oficiales con porcentaje mayor o igual a 50%. Mostrá una frase con país, idioma y porcentaje, separando los valores con guiones.



select * from countrylanguage c;



select countrycode || '-' || language || '-' || percentage

from countrylanguage 

where isofficial = 'T'

and percentage >= 50;



---



## 6. Países sin año de independencia



Lista los países cuyo año de independencia sea nulo. Si el jefe de estado también es nulo, mostrale el texto `Sin jefe de estado registrado`.



select * from country c;



select name, coalesce(nullif(headofstate, ''), 'sin jefe de estado')

from country c

where indepyear is null;



---



## 7. Países con año de independencia conocido



Consulta países que sí tengan año de independencia y clasificalos como `Antiguo`, `Moderno` o `Reciente` según el año.



select name , indepyear,

case 

when indepyear < 1800 then 'antiguo'

when indepyear between 1800 and 1949 then 'moderno'

else 'reciente'

end 

from country c where indepyear is not null;



---



## 8. Comparación entre `gnp` y `gnpold`



Consulta países donde el valor actual de `gnp` sea diferente del valor anterior `gnpold`, tratando correctamente los nulos. Mostrá además la diferencia absoluta entre ambos valores cuando sea posible.



select * from country c;



select gnp, gnpold

from country c 

where gnp != gnpold and gnp is not null and gnpold is not null;

---



## 9. Países donde el GNP anterior evita división por cero



Calcula una relación entre `gnp` y `gnpold`, evitando división por cero. Mostrá solo países donde ambos valores permitan hacer el análisis.



select name, gnp, gnpold, round (gnp/gnpold,2)

from country c 

where gnpold is not null

and gnpold != 0;



---



## 10. Superficie redondeada por continente



Agrupa países por continente y calcula la superficie total. Mostrá la superficie con redondeo normal, hacia arriba y hacia abajo.



select continent, round(sum(surfacearea)), ceil(sum(surfacearea)), floor(sum(surfacearea))

from country c

group by continent;







---



## 11. Búsqueda flexible de ciudades



Busca ciudades cuyo nombre empiece por `San`, contenga `rio` sin importar mayúsculas, o no contenga `New`. Ordená los resultados por población descendente.



select name, population

from city 

where name like 'San%'

or lower(name) like '%rio%'

or name not like '%New%'

order by population desc;

---



## 12. Patrones avanzados en nombres de países



Consulta países cuyos nombres cumplan patrones de texto más complejos: nombres que empiecen por vocal, nombres que contengan una doble letra, y nombres que no contengan números.



SELECT name FROM country

WHERE name SIMILAR TO '[AEIOUaeiou]%'

AND name ~ '(.)\1'

AND name NOT SIMILAR TO '%[0-9]%';

---



## 13. Países con o sin ciudades registradas



Consulta países que tengan al menos una ciudad registrada y, en otra consulta, países que no tengan ciudades registradas.



SELECT name FROM country

WHERE code IN (SELECT countrycode FROM city);



-- Países SIN ciudades

SELECT name FROM country

WHERE code NOT IN (SELECT countrycode FROM city);



---



## 14. Comparaciones contra subconjuntos



Consulta países cuya población sea mayor que todas las poblaciones de países de `Oceania`, países cuya expectativa de vida sea mayor que alguna expectativa de vida registrada en `Africa`, y excluí países de continentes que definas en una lista.



SELECT name, population, lifeexpectancy

FROM country

WHERE population > ALL(SELECT population FROM country WHERE continent = 'Oceania')

AND lifeexpectancy > ANY(SELECT lifeexpectancy FROM country WHERE continent = 'Africa' AND lifeexpectancy IS NOT NULL)

AND continent NOT IN ('Antarctica', 'Oceania');



---



## 15. Reporte final de países e idiomas



Genera un reporte que una países con sus idiomas. Mostrá el nombre del país con formato de título, una abreviatura del idioma, el porcentaje formateado, la fecha actual y el siglo calculado a partir del año de independencia cuando exista.





SELECT
    INITCAP(c.name),
    cl.language,
    cl.percentage::text || '%',
    CURRENT_DATE
FROM country c
JOIN countrylanguage cl
    ON c.code = cl.countrycode
WHERE cl.isofficial = 'T';

---
