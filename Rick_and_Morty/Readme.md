# Приветствую!
## Здесь будут различные добычи с API по Рику и Морти.

  Для начала, попробуем подключиться и добыть датафрейм. Импортируем библиотеки.

    import requests
    import json
    import pandas as pd
    
  Ознакомившись с [документацией](https://rickandmortyapi.com/documentation/#get-all-characters) понимаем, что можно получить данные по всем персонажам добавив к запросу список с их *id*. Писать вручную 800 цифр многовато, поэтому:

    list_id = []
    for i in range(1,827):
        list_id.append(i)
    
  Создаём список циклом. Далее, пишем запрос и создаём из него датафрейм:
  
    req = requests.get('https://rickandmortyapi.com/api/character/'+str(list_id))
    df = pd.json_normalize(req.json())
