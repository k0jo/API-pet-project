# Приветствую!
## Здесь будут различные добычи с API по Рику и Морти.
  *И первое, что можно узнать: кто был самым частым персонажем в сериале кроме самого семейства?*
  
  Для начала, попробуем подключиться и добыть датафрейм. Импортируем библиотеки.

```python
import requests
import json
import pandas as pd
```
    
  Ознакомившись с [документацией](https://rickandmortyapi.com/documentation/#get-all-characters) понимаем, что можно получить данные по всем персонажам добавив к запросу список с их *id*. Писать вручную 800 цифр многовато, поэтому:

```python
list_id = []
for i in range(1,827):
    list_id.append(i)
 ```
    
  Создаём список циклом. Далее, пишем запрос и создаём из него датафрейм:
```python  
req = requests.get('https://rickandmortyapi.com/api/character/'+str(list_id))
df = pd.json_normalize(req.json())
```   
Чтобы ответить на вопрос, нам не хватает кол-ва эпизодов в которых появлялся тот или иной персонаж. В датафрейме есть столбец со списками эпизодов с участием героя. Его мы и используем. Создадим столбец к кол-вом эпизодов, который будет рассчитываться таким образом: столбец со списками превращается в датафрейм, внутри этого дф считаем пустые ячейки и отнимаем их от 51 (общее кол-во эпизодов) для каждой строки.
```python 
df['count_episode'] = 51-(pd.DataFrame(df['episode'].tolist()).isnull().sum(axis=1))
```       
Теперь осортируем по эпизодам, оставим лишь пару столбцов и возьмём первые 10 значений:
```python 
df[['name','count_episode']].sort_values(by='count_episode',ascending=False).head(10)
 ```   
Получаем:

|id|name|	count_episode|
|- |:---| ------------:|
|0|	Rick Sanchez|	51|
|1|	Morty Smith|	51|
|2|	Summer Smith|	42|
|3|	Beth Smith|	42|
|4|	Jerry Smith|	39|
|179|	Jessica|	12|
|239|	Mr. Goldenfold|	10|
|37|	Beth Smith|	8|
|328|	Snuffles (Snowball)|	8|
|180|	Jessica's Friend|	8|
    
Джессика! Впрочем, это неудивительно. Удивительно, что есть ещё одна Бет. Да и Джерри не было 12 серий. Странно.
Подгрузим локации происхождения.
```python 
df[['name','count_episode','origin.name']].sort_values(by='count_episode',ascending=False).head(10)
```   
|id|name	|count_episode	|origin.name|
|--|:--- | ------------:|:----------|
0	|Rick Sanchez	|51	|Earth (C-137)
1	|Morty Smith	|51	|unknown
2	|Summer Smith	|42	|Earth (Replacement Dimension)
3	|Beth Smith	|42	|Earth (Replacement Dimension)
4	|Jerry Smith	|39	|Earth (Replacement Dimension)
179	|Jessica	|12	|Earth (Replacement Dimension)
239	|Mr. Goldenfold	|10	|Earth (Replacement Dimension)
37	|Beth Smith	|8	|Earth (C-137)
328	|Snuffles (Snowball)	|8	|Earth (C-137)
180	|Jessica's Friend	|8	|Earth (C-137)

Итак, есть Бет с оригинальной планеты (которая теперь населена Кроненбергами) и есть Бет, которую мы видим большую часть сериала.
Проверим Джерри, может мы видели его чаще.
```python
df.loc[df.name == 'Jerry Smith',['name','count_episode','origin.name']]
``` 
id|name	|count_episode	|origin.name
|- |:---| ------------:|:---|
4	|Jerry Smith	|39	|Earth (Replacement Dimension)
174	|Jerry Smith	|7	|Earth (C-137)
176	|Jerry Smith	|1	|Earth (Evil Rick's Target Dimension)

Да, Джерри мы встречали почаще. И всё же, из семьи он персонаж, который появлялся реже остальных (потому что "оригинальная" Саммер тоже присутствовала в 7ми эпизодах). Пусть и на пару эпизодов.
Итак, у нас есть два вывода:
 1. **Джессика наиболее частый персонаж (после семьи главных героев)**
 2. **Джерри наименее частый персонаж (из семьи главных героев)**
