# OpenAlexAPIUsage

В проекте представлен пример использования OpenAlex API для получения подробной информации о научных статьях на заданную тему

## Contents

Функция __get_pages(keyword, date_from, date_to)__ принимает ключевое слово, год для старта поиска и год для окончания поиска (ввод от пользователя). Количество найденных публикаций чаще всего оказывается большим, при этом единоразово выводится только 25 (чаще всего). Поэтому в программе реализован функционал постраничного вывода данных.

С помощью `get-запроса` получаем объект типа `Response`, далее формируется `json-файл`, результат записывается в переменную __data__.

```python
url = f'https://api.openalex.org/works?filter=abstract.search:{keyword},publication_year:{date_from}-{date_to}'
response = requests.get(url)
data = response.json()
```

Для начала обрабатывается объект `meta`, откуда извлекается информация об общем количестве публикаций ('count') и о количестве публикаций на странице ('per_page'). Производится расчет количества страниц.

```python
sheets = keyword_articles_count / articles_per_page
sheets = math.ceil(sheets)
```
_Стоит отметить, что если количество страниц более 10 000, то для постраничного вывода стоит использовать cursor paging_


Далее использован цикл `for`, в каждой итерации которого происходит get-запрос, формирование json-файла, запись результата в переменную data. После обрабатывается объект __results__ (в новом цикле for). 

```python
result = []
for elem in data['results']:
    publication = []
    publication.append(elem['title'])
    publication.append(elem['publication_year'])
    publication.append(elem['cited_by_count'])
    location = elem.get('primary_location')
```

Из собранных данных формируется `DataFrame`, страница выводится пользователю. Данные записываются в общий список __keyword_articles_result__.

```python
pubs = pd.DataFrame(result, columns=['title', 'publication_year', 'cited_by_count', 'journal_name'])
if pubs.empty:
    continue
else:
    print(f'\nСТРАНИЦА НОМЕР {i}')
    display(pubs)
```