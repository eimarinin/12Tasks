# 12Tasks

## 1. Сколько медалей выиграл Jesse Owens в 1936?
```
SELECT COUNT(*) AS total_medals
FROM dataset
WHERE Athlete = 'Jesse Owens' AND Edition = 1936;
```
### 4

## 2. Какая страна выиграла большинство золотых медалей мужчинами в бадминтоне?
```
SELECT NOC, COUNT(*) AS gold_medals
FROM dataset
WHERE Sport = 'Badminton' AND Gender = 'Men' AND Medal = 'Gold'
GROUP BY NOC
ORDER BY gold_medals DESC
LIMIT 1;

```
### 8

## 3. Какие три страны выиграли большинство медалей в последние годы (с 1984 по 2008)?
```
SELECT NOC, COUNT(*) AS total_medals
FROM dataset
WHERE Edition >= 1984 AND Edition <= 2008
GROUP BY NOC
ORDER BY total_medals DESC
LIMIT 3;

```
### USA, AUS, GER
USA |         1837
AUS |          762
GER |          691

## 4. Покажите мужчин золотых медалистов по 100m. Выведите результаты по убыванию года выйгрыша. Покажите город в котором проходила олимпиада, год, имя атлета и страну за которую он выступал.
```
SELECT City, Edition, Athlete, NOC
FROM dataset
WHERE Event = '100m' AND Gender = 'Men' AND Medal = 'Gold'
ORDER BY Edition DESC;

```
### Получилось 26 строк, вот первые 3:
Beijing               |    2008 | BOLT, Usain           | JAM
Athens                |    2004 | GATLIN, Justin        | USA
Sydney                |    2000 | GREENE, Maurice       | USA

## 5. Как много медалей было выиграно мужчинами и женщинами в истории олимпиады. Как много золотых, серебряных и бронзовых медалей было выиграно каждым полом?
```
SELECT Gender, Medal, COUNT(*) AS total_medals
FROM dataset
GROUP BY Gender, Medal
ORDER BY Gender, Medal;

```
### 6 строк:

Men    | Bronze |         7175
Men    | Gold   |         7365
Men    | Silver |         7181
Women  | Bronze |         2514
Women  | Gold   |         2485
Women  | Silver |         2496

## 6. Выведите общее кол-во медалей для каждой олимпиады.
```
SELECT Edition, COUNT(*) AS total_medals
FROM dataset
GROUP BY Edition
ORDER BY Edition;

```
### 26 строк, вот первые 3:
1896 |          151
1900 |          512
1904 |          470

## 7. Создайте список показывающий число всех медалей выигранных каждой страной в течение всей истории олимпийских игр. Для каждой страны, необходимо показать год первой и последней заработанной медали.
```
SELECT NOC, COUNT(*) AS total_medals, MIN(Edition) AS first_year, MAX(Edition) AS last_year
FROM dataset
GROUP BY NOC
ORDER BY total_medals DESC;

```
### Много строк, вот первые 3:
USA |         4335 |       1896 |      2008
URS |         2049 |       1952 |      1988
GBR |         1594 |       1896 |      2008

## 8. Атлеты выигравшие медали в Beijing на дистанции 100m или 200m.
```
SELECT Athlete, Event
FROM dataset
WHERE City = 'Beijing' AND (Event = '100m' OR Event = '200m');

```
### 12 строк:
DIX, Walter              | 100m
BOLT, Usain              | 100m
THOMPSON, Richard        | 100m
FRASER, Shelly-ann       | 100m
SIMPSON, Sherone         | 100m
STEWART, Kerron          | 100m
DIX, Walter              | 200m
BOLT, Usain              | 200m
CRAWFORD, Shawn          | 200m
STEWART, Kerron          | 200m
CAMPBELL-BROWN, Veronica | 200m
FELIX, Allyson           | 200m

## 9. Найдите кол-во золотых медалей выигранных США мужчинами и женщинами в атлетике на каждой олимпиаде.
```
SELECT Edition, Gender, COUNT(*) AS gold_medals
FROM dataset
WHERE NOC = 'USA' AND Sport = 'Athletics' AND Medal = 'Gold'
GROUP BY Edition, Gender
ORDER BY Edition;

```
### Первые 3:
1896 | Men    |           9
1900 | Men    |          16
1904 | Men    |          27

## 10. Найдите 5 атлетов которые выиграли большинство золотых медалей.
```
SELECT Athlete, COUNT(*) AS gold_medals
FROM dataset
WHERE Medal = 'Gold'
GROUP BY Athlete
ORDER BY gold_medals DESC
LIMIT 5;

```
### 5 строк:
PHELPS, Michael  |          14
LATYNINA, Larisa |           9
NURMI, Paavo     |           9
SPITZ, Mark      |           9
LEWIS, Carl      |           9

## 11. Покажите суммарное количество медалей выигранных странами в последних олимпийских играх.
```
SELECT NOC, COUNT(*) AS total_medals
FROM dataset
WHERE Edition = (SELECT MAX(Edition) FROM dataset)
GROUP BY NOC
ORDER BY total_medals DESC;

```
### Первые 3:
USA |          315
CHN |          184
AUS |          149

## 12. Постройте таблицу в которой по годам всех олимпиад покажите топовых атлетов США (1 атлет на год) по общему количеству медалей. Включите дисциплину атлета.
```
SELECT Edition, Athlete, Discipline, COUNT(*) AS total_medals
FROM dataset
WHERE NOC = 'USA'
GROUP BY Edition, Athlete, Discipline
HAVING COUNT(*) = (
  SELECT MAX(medals_count)
  FROM (
    SELECT Edition, Athlete, COUNT(*) AS medals_count
    FROM dataset
    WHERE NOC = 'USA'
    GROUP BY Edition, Athlete
  ) AS subquery
  WHERE subquery.Edition = dataset.Edition
)
ORDER BY Edition;

```
### Первые 3:
1896 | GARRETT, Robert                  | Athletics     |            4
1900 | BAXTER, Irving                   | Athletics     |            5
1900 | TEWKSBURY, Walter B. John        | Athletics     |            5
