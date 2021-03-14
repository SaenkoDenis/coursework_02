# Курсовая работа № 2
## Data Scientist. Аналитика

Курсовая работа состоит из двух частей – обязательной и дополнительной. **Для зачета необходимо выполнение только первой части.** Выполнение второй части может потребовать дополнительные знания Python.

- [Часть первая](#Часть-первая)
- [Часть вторая](#Часть-вторая)

## Часть первая

Перед вами стоит задача – подготовить аналитический отчет для HR-отдела. На основании проведенной аналитики предполагается составить рекомендации для отдела кадров по стратегии набора персонала, а также по взаимодействию с уже имеющимися сотрудниками.
<br><br> В базе данных лежит набор таблиц, которые содержат данные о сотрудниках вымышленной компании.
Сделайте обзор штата сотрудников компании. Составьте набор предметов исследования, а затем проверьте их на данных. Вся аналитика должна быть выполена с помощью SQL. Впоследствии данные можно визуализировать, однако финальные датафреймы для графиков также должны быть подготовлены с помощью SQL.
```python
# Импорт библиотек Psycopg2, Numpy, Pandas
import psycopg2
import numpy as np
import pandas as pd

# Подключение к базе данных PostgreSQL
conn = psycopg2.connect(**{'host': 'dsstudents.skillbox.ru', 
                           'port': 5432, 
                           'dbname': 'human_resources', 
                           'user': 'readonly', 
                           'password': '6hajV34RTQfmxhS'})

# Получение информации о структуре таблицы hr_dataset
hr_df = pd.read_sql_query('select * from hr_dataset', conn).info()
```
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 310 entries, 0 to 309
    Data columns (total 29 columns):
     #   Column               Non-Null Count  Dtype  
    ---  ------               --------------  -----  
     0   id                   310 non-null    int64  
     1   Employee Name        310 non-null    object 
     2   Employee Number      310 non-null    int64  
     3   marriedid            310 non-null    int64  
     4   maritalstatusid      310 non-null    int64  
     5   genderid             310 non-null    int64  
     6   empstatus_id         310 non-null    int64  
     7   deptid               310 non-null    int64  
     8   perf_scoreid         310 non-null    int64  
     9   age                  310 non-null    int64  
     10  Pay Rate             310 non-null    float64
     11  state                310 non-null    object 
     12  zip                  310 non-null    int64  
     13  dob                  310 non-null    object 
     14  sex                  310 non-null    object 
     15  maritaldesc          310 non-null    object 
     16  citizendesc          310 non-null    object 
     17  Hispanic/Latino      310 non-null    object 
     18  racedesc             310 non-null    object 
     19  Date of Hire         310 non-null    object 
     20  Days Employed        310 non-null    int64  
     21  Date of Termination  103 non-null    object 
     22  Reason For Term      310 non-null    object 
     23  Employment Status    310 non-null    object 
     24  department           310 non-null    object 
     25  position             310 non-null    object 
     26  Manager Name         310 non-null    object 
     27  Employee Source      310 non-null    object 
     28  Performance Score    310 non-null    object 
    dtypes: float64(1), int64(11), object(17)
    memory usage: 70.4+ KB
    
```python
# Получение информации о структуре таблицы production_staff
pd.read_sql_query('select * from production_staff', conn).info()
```
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 256 entries, 0 to 255
    Data columns (total 16 columns):
     #   Column               Non-Null Count  Dtype  
    ---  ------               --------------  -----  
     0   id                   256 non-null    int64  
     1   Employee Name        209 non-null    object 
     2   Race Desc            209 non-null    object 
     3   Date of Hire         209 non-null    object 
     4   TermDate             83 non-null     object 
     5   Reason for Term      209 non-null    object 
     6   Employment Status    209 non-null    object 
     7   Department           209 non-null    object 
     8   Position             209 non-null    object 
     9   Pay                  209 non-null    object 
     10  Manager Name         209 non-null    object 
     11  Performance Score    209 non-null    object 
     12  Abutments/Hour Wk 1  208 non-null    float64
     13  Abutments/Hour Wk 2  208 non-null    float64
     14  Daily Error Rate     208 non-null    float64
     15  90-day Complaints    208 non-null    float64
    dtypes: float64(4), int64(1), object(11)
    memory usage: 32.1+ KB

```python
# Получение информации о структуре таблицы recruiting_cost
pd.read_sql_query('select * from recruiting_costs', conn).info()
```
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 22 entries, 0 to 21
    Data columns (total 15 columns):
     #   Column             Non-Null Count  Dtype 
    ---  ------             --------------  ----- 
     0   id                 22 non-null     int64 
     1   Employment Source  22 non-null     object
     2   January            22 non-null     int64 
     3   February           22 non-null     int64 
     4   March              22 non-null     int64 
     5   April              22 non-null     int64 
     6   May                22 non-null     int64 
     7   June               22 non-null     int64 
     8   July               22 non-null     int64 
     9   August             22 non-null     int64 
     10  September          22 non-null     int64 
     11  October            22 non-null     int64 
     12  November           22 non-null     int64 
     13  December           22 non-null     int64 
     14  Total              22 non-null     int64 
    dtypes: int64(14), object(1)
    memory usage: 2.7+ KB

```python
# Получение информации о структуре таблицы salary_grid
pd.read_sql_query("select * from salary_grid", conn).info()
```
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 12 entries, 0 to 11
    Data columns (total 8 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   id          12 non-null     int64  
     1   Position    12 non-null     object 
     2   Salary Min  12 non-null     int64  
     3   Salary Mid  12 non-null     int64  
     4   Salary Max  12 non-null     int64  
     5   Hourly Min  12 non-null     float64
     6   Hourly Mid  12 non-null     float64
     7   Hourly Max  12 non-null     float64
    dtypes: float64(3), int64(4), object(1)
    memory usage: 896.0+ bytes
    
### Исследование 1. Возраст сотрудника влияет на качество его работы. Возрастные сотрудники в работе допускают больше ошибок.
```python
# Получение из базы данных информации по сотрудникам компании
sql_query = '''
    select hr.age, pr."Daily Error Rate" as daily_error_rate
    from hr_dataset hr 
    left join production_staff pr on 
        (hr."Employee Name" = pr."Employee Name") and (hr."Date of Hire" = pr."Date of Hire") and 
        (hr.position = pr."Position")'''

staff_df = pd.read_sql_query(sql_query, conn).fillna(0)

# Определение возрастных групп сотрудников компании
age_range = pd.cut(staff_df['age'], [0, 30, 50, 80])

# Вывод среднего числа ежедневных ошибок, приходящееся на каждого сотрудника для каждой возрастной группы
staff_df.pivot_table(values='daily_error_rate', index=age_range, aggfunc=np.mean)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>age</th>
      <th>daily_error_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(0, 30]</th>
      <td>0.372549</td>
    </tr>
    <tr>
      <th>(30, 50]</th>
      <td>0.638393</td>
    </tr>
    <tr>
      <th>(50, 80]</th>
      <td>0.714286</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Переход в интерактивный режим построения графиков и диаграмм
%matplotlib inline

# Импорт библиотеки Matplotlib
import matplotlib.pyplot as plt

# Определение основных объектов диаграммы
fig, ax = plt.subplots(figsize=(6, 4))

# Отражение среднего количества ежедневных ошибок каждой возрастной группы
staff_df.pivot_table(values='daily_error_rate', index=age_range, aggfunc=np.mean).plot.bar(
    ax=ax, width=0.2, title='Среднее количество ежедневных\n ошибок каждой возрастной группы', rot=0, legend=None)

# Вывод подписей осей координат
ax.set_xlabel('Возрастная группа')
ax.set_ylabel('Среднее кол. ежедневных ошибок')

# Отображение диаграмм на странице
plt.show()
```
![png](Images/output_9_0.png)
    
### Исследование 2. Сотрудники среднего возраста - самые высокооплачиваемые сотрудники
```python
# Получение из базы данных информации по сотрудникам компании
sql_query = 'select age, "Pay Rate" as pay_rate from hr_dataset'
staff_df = pd.read_sql_query(sql_query, conn).fillna(0)

# Вывод медианы оплаты труда сотрудников для каждой возрастной группы
staff_df.pivot_table(values='pay_rate', index=age_range, aggfunc=np.median)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>age</th>
      <th>pay_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(0, 30]</th>
      <td>28.00</td>
    </tr>
    <tr>
      <th>(30, 50]</th>
      <td>23.25</td>
    </tr>
    <tr>
      <th>(50, 80]</th>
      <td>26.00</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Определение основных объектов диаграммы
fig, ax = plt.subplots(figsize=(6, 4))

# Отражение медианы оплаты труда сотрудников для каждой возрастной группы
staff_df.pivot_table(values='pay_rate', index=age_range, aggfunc=np.median).plot.bar(
    ax=ax, width=0.2, title='Медиана оплаты труда сотрудников\n для каждой возрастной группы', rot=0, legend=None)

# Вывод подписей осей координат
ax.set_xlabel('Возрастная группа')
ax.set_ylabel('Медиана оплаты труда')

# Отображение диаграмм на странице
plt.show()
```
![png](Images/output_12_0.png)
    
### Исследование 3. Сотрудники молодого возраста больше подвержены частой смене работы
```python
# Получение из базы данных информации по сотрудникам компании
sql_query = 'select age, "Days Employed" as days_employed from hr_dataset'
staff_df = pd.read_sql_query(sql_query, conn).fillna(0)

# Вывод медианы длительности работы сотрудника в компании для каждой возрастной группы
staff_df.pivot_table(values='days_employed', index=age_range, aggfunc=np.median)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>age</th>
      <th>days_employed</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(0, 30]</th>
      <td>1112</td>
    </tr>
    <tr>
      <th>(30, 50]</th>
      <td>1291</td>
    </tr>
    <tr>
      <th>(50, 80]</th>
      <td>1238</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Определение основных объектов диаграммы
fig, ax = plt.subplots(figsize=(6, 4))

# Отражение медианы длительности работы сотрудника в компании для каждой возрастной группы
staff_df.pivot_table(values='days_employed', index=age_range, aggfunc=np.median).plot.bar(
    ax=ax, width=0.2, title='Медиана длительности работы сотрудника\n в компании для каждой возрастной группы', 
    rot=0, legend=None)

# Вывод подписей осей координат
ax.set_xlabel('Возрастная группа')
ax.set_ylabel('Медиана длительности работы сотрудника')

# Отображение диаграмм на странице
plt.show()
```
![png](Images/output_15_0.png)
    
### Исследование 4. Иммигранты и неграждане страны менее добросовестно выполняют свою работу
```python
# Получение из базы данных информации по сотрудникам компании
sql_query = '''
    select hr.citizendesc, pr."Daily Error Rate" as daily_error_rate
    from hr_dataset hr 
    left join production_staff pr on 
        (hr."Employee Name" = pr."Employee Name") and (hr."Date of Hire" = pr."Date of Hire") and 
        (hr.position = pr."Position")'''

staff_df = pd.read_sql_query(sql_query, conn).fillna(0)

# Вывод среднего числа ежедневных ошибок у групп сотрудников из категории "citizendesc"
staff_df.pivot_table(values='daily_error_rate', index='citizendesc', aggfunc=np.mean)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>citizendesc</th>
      <th>daily_error_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Eligible NonCitizen</th>
      <td>0.750000</td>
    </tr>
    <tr>
      <th>Non-Citizen</th>
      <td>1.250000</td>
    </tr>
    <tr>
      <th>US Citizen</th>
      <td>0.588435</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Определение основных объектов диаграммы
fig, ax = plt.subplots(figsize=(6, 4))

# Отражение среднего числа ежедневных ошибок у групп сотрудников из категории "citizendesc"
staff_df.pivot_table(values='daily_error_rate', index='citizendesc', aggfunc=np.mean).plot.bar(
    ax=ax, width=0.2, title='Среднее количество ежедневных ошибок у\n групп сотрудников в категории "citizendesc"', 
    rot=0, legend=None)

# Вывод подписей осей координат
ax.set_xlabel('Возрастная группа')
ax.set_ylabel('Медиана длительности работы сотрудника')

# Отображение диаграмм на странице
plt.show()
```
![png](Images/output_18_0.png)
    
### Вывод

Все рассмотренные гипотезы подтвердились, кроме одной - "*Сотрудники среднего возраста - самые высокооплачиваемые сотрудники*". Согласно проведенного анализа по данной гипотезе, наибольший заработок получают сотрудники из возрастной группы не старше 30.

В качестве рекомендации по стратегии набора персонала, можно рекомендовать следующий портрет идеального сотрудника:
1. согласно гипотезам 1-3, сотрудник должен быть из группы среднего возраста (от 30 до 50), так как в работе допускает количество ежедневных ошибок в пределах среднего, получает наименьший заработок и меньше всего подвержен частым сменам работы;
2. согласно гипотезе 4, сотрудник должен быть гражданином Соединенных Штатов Америки, так как более добросовестно выполняет работу и меньше допускает ежедневных ошибок.

## Часть вторая

Перед вами стоит задача – подготовить аналитический ответ для SMM-отдела компании Skillbox. <br> Объектом анализа является  [паблик Skillbox Вконтакте](https://vk.com/skillbox_education). <br> <br> 
Подключитесь к  API VK и выгрузите посты со стены паблика Skillbox за интересующий период (определите самостоятельно и обоснуйте). Проанализируйте влияние различных факторов (например, времени публикации) на вовлеченность пользователей (количество лайков, комментариев, голосов в опросах). Сделайте аналитику по рубрикам (примеры рубрик: дизайн-битва, игра по управлению), которые есть в паблике. Выбрать нужные посты можно с помощью регулярных выражений. Составьте перечень рекомандаций для SMM-отдела по итогам анализа.
```python
# Импорт библиотек Math, Requests, Numpy и Pandas
import math
import requests
import numpy as np
import pandas as pd

# Импорт библиотеки DateTime
from datetime import datetime

# Определение основных параметров запроса
url = 'https://api.vk.com/method/wall.get'
token = '72dcb67872dcb67872dcb6785b72ae8662772dc72dcb6782c1740804e0b1207aa8a90ff'
domain = 'skillbox_education'
version = 5.107
offset = 0

# Получение количества постов на стене паблика Skillbox
response = requests.get(url, params={'access_token': token, 'v': version, 'domain': domain, 'count': 1})
count_posts = response.json()['response']['count']

# Получение всех постов стены паблика Skillbox
all_posts = list()

while offset <= math.ceil(count_posts / 100) * 100:
    response = requests.get(url, params={'access_token': token, 'v': version, 'domain': domain, 'count': 100, 'offset': offset})
    all_posts.extend(response.json()['response']['items'])
    offset += 100

# Формирование датасета с выгруженными постами 
posts_df = pd.json_normalize(all_posts)

# Преобразование значений в колонке 'date' в формат даты
posts_df['date'] = posts_df['date'].apply(datetime.fromtimestamp)

# Преобразование текста в колонке 'text' к нижнему регистру
posts_df['text'] = posts_df['text'].str.lower()
```

### Анализ влияния различных факторов (например, времени публикации) на вовлеченность пользователей (количество лайков, комментариев, голосов в опросах)
```python
# Определение основных объектов диаграммы
fig, axes = plt.subplots(4, 1, figsize=(10, 12))

# Формирование датасета для построения графиков
chart_df = posts_df.pivot_table(
    values=['views.count', 'likes.count', 'reposts.count', 'comments.count'], 
    index=pd.to_datetime(10000 * 2020 + 100 * posts_df.date.dt.month + posts_df.date.dt.day, format='%Y%m%d'), 
    aggfunc=np.median)

# Словарь заголовков
titles = {'views': 'Медианное количество просмотров постов паблика Skillbox',
          'likes':'Медианное количество лайков постов паблика Skillbox', 
          'reposts': 'Медианное количество репостов постов паблика Skillbox', 
          'comments': 'Медианное количество комментариев постов паблика Skillbox'}

# Вывод графиков на страницу
chart_df['views.count'].plot(ax=axes[0], color='tab:blue', title = titles['views'])
chart_df['likes.count'].plot(ax=axes[1], color='tab:orange', title = titles['likes'])
chart_df['reposts.count'].plot(ax=axes[2], color='tab:green', title = titles['reposts'])
chart_df['comments.count'].plot(ax=axes[3], color='tab:red', title = titles['comments'])

# Подписание осей координат
for ax in axes.flat:
    ax.set(xlabel=None, ylabel=None)

# Автоматическое позиционирование элементов графика
fig.tight_layout()

# Отображение графика на странице
plt.show()
```
![png](Images/output_25_0.png)
    
### Вывод

На графиках, отражающих вовлеченность, пользователей в различные периоды времени календарного года видно, что в периоды с апреля по июль и с октября по декабрь пользователи проявляют наибольший интерес к опубликованному материалу на стене паблика Skillbox. В весенне-летний период наблюдается не только высокий, но и стабильный интерес к постам паблика Skillbox.

### Анализ по рубрикам (примеры рубрик: дизайн-битва, игра по управлению), которые есть в паблике

#### Вывод самых публикуемых рубрик паблика Skillbox
```python
# Импорт модуля регулярных выражений
import re

# Формирование полного списка рубрик
rubric_list = posts_df['text'].str.findall('#\w+').apply(lambda x: ','.join(x)).to_list()
rubric_list = ','.join(rubric_list).split(',')

# Формирование датасета рубрик
rubric_df = pd.DataFrame({'rubric': rubric_list, 'count': 1})

# Удаление из датасета пустых позиций
rubric_df = rubric_df.query('rubric!=""')

# Вывод 10 популярных рубрик
rubric_df.groupby('rubric').count().sort_values('count', ascending=False).head(10)
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>rubric</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>#цитаты</th>
      <td>768</td>
    </tr>
    <tr>
      <th>#афоризмы</th>
      <td>764</td>
    </tr>
    <tr>
      <th>#read</th>
      <td>370</td>
    </tr>
    <tr>
      <th>#skillbox</th>
      <td>359</td>
    </tr>
    <tr>
      <th>#business</th>
      <td>319</td>
    </tr>
    <tr>
      <th>#skillbox_дизайн</th>
      <td>203</td>
    </tr>
    <tr>
      <th>#marketing</th>
      <td>202</td>
    </tr>
    <tr>
      <th>#design</th>
      <td>198</td>
    </tr>
    <tr>
      <th>#code</th>
      <td>190</td>
    </tr>
    <tr>
      <th>#статья_skillbox</th>
      <td>160</td>
    </tr>
  </tbody>
</table>
</div>

#### Анализ постов из рубрики "skillbox_дизайн"
```python
# Импорт модуля Dates пакета Matplotlib
import matplotlib.dates as mdates

# Определение основных объектов диаграммы
fig, axes = plt.subplots(3, 1, figsize=(10, 9))

# Формирование датасета для построения графика
index_list = posts_df['text'].str.contains('#skillbox_дизайн')

chart_df = posts_df[index_list].pivot_table(
    values=['likes.count', 'reposts.count', 'comments.count'],
    index=posts_df['date'].astype('datetime64[D]'), 
    aggfunc=np.median)

# Словарь заголовков
titles = {'likes':'Медианное количество лайков в зависимости от даты публикации', 
          'reposts': 'Медианное количество репостов в зависимости от даты публикации', 
          'comments': 'Медианное количество комментариев в зависимости от даты публикации'}

# Вывод графиков на страницу
chart_df['likes.count'].plot(ax=axes[0], color='tab:orange', title=titles['likes'])
chart_df['reposts.count'].plot(ax=axes[1], color='tab:green', title=titles['reposts'])
chart_df['comments.count'].plot(ax=axes[2], color='tab:red', title=titles['comments'])

# Определение формата меток осей
for ax in axes.flat:
    ax.xaxis.set_major_locator(mdates.MonthLocator())
    ax.xaxis.set_major_formatter(mdates.DateFormatter('%b %y'))

# Подписание осей координат
for ax in axes.flat:
    ax.set(xlabel=None, ylabel=None)

# Автоматическое позиционирование элементов графика
fig.tight_layout()

# Отображение графика на странице
plt.show()
```
![png](Images/output_32_0.png)
    
```python
# Вывод постов вызвавшие наибольший интерес в Декабре месяце
month_df = posts_df[['date', 'text', 'likes.count', 'reposts.count', 'comments.count']]
month_df = month_df.query('date > "2019.11.25" & date < "2019.12.10"')
month_df = month_df[month_df['text'].str.contains('#skillbox_дизайн')]

# Вывод постов
month_df
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>text</th>
      <th>likes.count</th>
      <th>reposts.count</th>
      <th>comments.count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>301</th>
      <td>2019-12-07 11:00:01</td>
      <td>студия warner bros. часто адаптирует свой лого...</td>
      <td>46</td>
      <td>2</td>
      <td>89</td>
    </tr>
    <tr>
      <th>305</th>
      <td>2019-12-05 17:00:10</td>
      <td>создать или скачать — вот в чем вопрос. да-да,...</td>
      <td>69</td>
      <td>11</td>
      <td>4</td>
    </tr>
    <tr>
      <th>306</th>
      <td>2019-12-05 12:22:09</td>
      <td>ко дню рождения уолта диснея, «отца» микки мау...</td>
      <td>262</td>
      <td>32</td>
      <td>3</td>
    </tr>
    <tr>
      <th>307</th>
      <td>2019-12-04 19:00:01</td>
      <td>дизайнеры, отвлекитесь от игр со шрифтами. лов...</td>
      <td>643</td>
      <td>129</td>
      <td>5</td>
    </tr>
    <tr>
      <th>327</th>
      <td>2019-11-26 11:43:14</td>
      <td>если давно хотели научиться рисовать в стиле p...</td>
      <td>129</td>
      <td>8</td>
      <td>16</td>
    </tr>
  </tbody>
</table>
</div>
```python
# Вывод текста поста с наибольшим количеством лайков
month_df['text'][month_df['likes.count'].idxmax()]
```
    'дизайнеры, отвлекитесь от игр со шрифтами. ловите подборку из семи фильмов и сериалов, которые расширят ваш профессиональный кругозор.\n\n#в_закладки_skillbox #skillbox_дизайн'

```python
# Вывод постов вызвавшие наибольший интерес в Феврале месяце
month_df = posts_df[['date', 'text', 'likes.count', 'reposts.count', 'comments.count']]
month_df = month_df.query('date > "2020.02.05" & date < "2020.02.15"')
month_df = month_df[month_df['text'].str.contains('#skillbox_дизайн')]

# Вывод постов
month_df
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>text</th>
      <th>likes.count</th>
      <th>reposts.count</th>
      <th>comments.count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>174</th>
      <td>2020-02-14 11:21:30</td>
      <td>быть частью целого, но оставаться собой😏 apple...</td>
      <td>213</td>
      <td>13</td>
      <td>7</td>
    </tr>
    <tr>
      <th>176</th>
      <td>2020-02-13 11:59:51</td>
      <td>дизайнеры, собрали для вас сайты, где можно бе...</td>
      <td>540</td>
      <td>94</td>
      <td>4</td>
    </tr>
    <tr>
      <th>177</th>
      <td>2020-02-12 18:46:18</td>
      <td>точка, точка, запятая, – вышла рожица кривая. ...</td>
      <td>72</td>
      <td>10</td>
      <td>5</td>
    </tr>
    <tr>
      <th>181</th>
      <td>2020-02-11 12:22:05</td>
      <td>нужна картинка для соцсетей, а photoshop нет п...</td>
      <td>610</td>
      <td>108</td>
      <td>0</td>
    </tr>
    <tr>
      <th>183</th>
      <td>2020-02-10 16:53:20</td>
      <td>сегодня не бульварное чтиво в моде, а мода в б...</td>
      <td>68</td>
      <td>2</td>
      <td>1</td>
    </tr>
    <tr>
      <th>187</th>
      <td>2020-02-08 14:28:34</td>
      <td>у талантливого дизайнера даже самые страшные м...</td>
      <td>122</td>
      <td>4</td>
      <td>6</td>
    </tr>
    <tr>
      <th>188</th>
      <td>2020-02-07 12:31:03</td>
      <td>евровидение меняет логотип каждый год, но на э...</td>
      <td>24</td>
      <td>0</td>
      <td>30</td>
    </tr>
    <tr>
      <th>192</th>
      <td>2020-02-06 11:31:11</td>
      <td>на вкус и цвет товарища нет? насчет вкуса согл...</td>
      <td>479</td>
      <td>92</td>
      <td>11</td>
    </tr>
    <tr>
      <th>193</th>
      <td>2020-02-05 19:11:06</td>
      <td>не можете определиться с цветовой палитрой для...</td>
      <td>110</td>
      <td>23</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Вывод текста поста с наибольшим количеством лайков
month_df['text'][month_df['likes.count'].idxmax()]
```
    'нужна картинка для соцсетей, а photoshop нет под рукой? воспользуйтесь бесплатными сервисами по созданию изображений. не ищите, мы уже все собрали для вас.\n\ncanva. в вашем распоряжении миллионы фотографий, векторных изображений и иллюстраций, сотни шрифтов, множество иконок и фильтров. создавайте картинки для любых целей. соцсети, обложки для блогов и книг, открытки, инфографика, ресторанное меню — canva может почти все.\n\ncrello. здесь можно работать не только со статичными изображениями, но даже с анимацией. не нравятся имеющиеся шрифты? загрузите свои, в этом нет никаких ограничений. кстати, для вашего вдохновения собрана целая галерея готовых работ.\n\nadobe spark. в сервисе есть готовые шаблоны, по которым очень удобно работать. ничего, если у вас нет опыта в дизайне: adobe spark на каждом шагу дает подсказки.\n\ngiphy gif maker. простой и удобный сервис для создания гифок. перед началом работы необязательно скачивать видео: достаточно дать на него ссылку. к гифке можно добавить надписи, фильтры и стикеры. из недостатков: сервис работает только с видео до 15 минут.\n\nget stencill. в этом сервисе за несколько секунд можно сделать крутые картинки для соцсетей, привлекательную рекламу, броские обложки для блогов и многое другое. более 60 нестандартных размеров, свыше 1000 шаблонов, 100 000 цитат для мотивирующих изображений, около 3 000 шрифтов.\n\n#в_закладки_skillbox #skillbox_дизайн #skillbox_маркетинг'

```python
# Вывод постов вызвавшие наибольший интерес в Мае месяце
month_df = posts_df[['date', 'text', 'likes.count', 'reposts.count', 'comments.count']]
month_df = month_df.query('date > "2020.05.01" & date < "2020.05.15"')
month_df = month_df[month_df['text'].str.contains('#skillbox_дизайн')]

# Вывод постов
month_df
```
<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>text</th>
      <th>likes.count</th>
      <th>reposts.count</th>
      <th>comments.count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>21</th>
      <td>2020-05-14 11:55:07</td>
      <td>когда внутренний мир вырывается наружу. \n\nма...</td>
      <td>140</td>
      <td>5</td>
      <td>12</td>
    </tr>
    <tr>
      <th>23</th>
      <td>2020-05-13 12:08:31</td>
      <td>как бы выглядело вдохновение, если бы его визу...</td>
      <td>162</td>
      <td>10</td>
      <td>16</td>
    </tr>
    <tr>
      <th>26</th>
      <td>2020-05-11 16:52:19</td>
      <td>каждый вид искусства по-своему прекрасен. но в...</td>
      <td>47</td>
      <td>6</td>
      <td>5</td>
    </tr>
    <tr>
      <th>32</th>
      <td>2020-05-06 12:20:16</td>
      <td>срочно нужна анимация в соцсети, а у дизайнера...</td>
      <td>917</td>
      <td>179</td>
      <td>20</td>
    </tr>
    <tr>
      <th>33</th>
      <td>2020-05-05 12:30:12</td>
      <td>как вам такой парадоксальный жанр: гиперреальн...</td>
      <td>140</td>
      <td>12</td>
      <td>19</td>
    </tr>
    <tr>
      <th>35</th>
      <td>2020-05-04 11:34:37</td>
      <td>да пребудут с вами скиллы! сегодня праздник у ...</td>
      <td>150</td>
      <td>7</td>
      <td>19</td>
    </tr>
    <tr>
      <th>36</th>
      <td>2020-05-03 15:38:01</td>
      <td>превратить стилус в кисти эдварда мунка? и так...</td>
      <td>148</td>
      <td>13</td>
      <td>0</td>
    </tr>
    <tr>
      <th>37</th>
      <td>2020-05-02 16:05:33</td>
      <td>самая большая сложность в ux-дизайне — объясни...</td>
      <td>214</td>
      <td>35</td>
      <td>27</td>
    </tr>
    <tr>
      <th>38</th>
      <td>2020-05-01 13:58:50</td>
      <td>мир! труд! май! дизайн!\n\nпраздник весны и тр...</td>
      <td>120</td>
      <td>3</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>

```python
# Вывод текста поста с наибольшим количеством лайков
month_df['text'][month_df['likes.count'].idxmax()]
```
    'срочно нужна анимация в соцсети, а у дизайнера майские праздники?\n\nловите подборку сервисов, которые помогут «оживить» картинку, даже если вы не умеете рисовать. читайте в карточках!\n\n#в_закладки_skillbox #skillbox_дизайн'

### Вывод

Одна из самых публикуемых рубрик на паблике Skillbox - "#Skillbox_Дизайн" (203 публикации). Анализ вовлеченности пользователей по данной рубрике, за весь период ее публикации, показывает, что на Декабрь, Февраль и Май приходится наибольшая активность. Активность в Декабре обусловлена публикацией поста с подборкой из семи фильмов и сериалов, что получило наибольший отклик от пользователей. В Феврале активность пришлась на пост с бесплатными сервисами по созданию изображений, в Мае - пост с сервисами по "оживлению" картинок.

Из проведенного анализа по рубрике "#Skillbox_Дизайн" следует, что наибольшей популярностью среди пользователей пользуются посты с бесплатными и уникальными сервисами, а также посты с нестандартной подачей материала.

### Рекомандации для SMM-отдела

Учитывая сезонность интереса пользователей к информации, публикуемой на стене Skillbox, основную маркетинговую активность по продвижению курсов лучше планировать в весенне-летний период.

Также необходимо обратить внимание на подачу материала постов - нестандартная подача информации пользуется популярностью, например, видео материал. Среди пользователей популярны посты с хорошей практической ценностью - полезная информация, которую можно применить на практике и получить хороший результат.
