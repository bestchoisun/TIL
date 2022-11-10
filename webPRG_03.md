

### 3-2 복습
 
```
let row = response["movies"]
```
 로 적어야 함. ["movies"]["row"]해서 실행안되었음

```JSON
"movies": [
{...},
{...},
]
```
원본 자료 위 내용


### 3-7 웹스크래핑 기초

```python
title = soup.select_one('#old_content > table > tbody > tr:nth-child(2) > td.title > div > a')
```
에서 ' ' 를 빠뜨렸음


### 3-11

- 몽고DB 연결이 안되어서 즉문즉답 참고하여 certifi 패키지 설치함

```
from pymongo import MongoClient
import certifi
ca = certifi.where()
client = MongoClient('mongodb+srv://test:sparta@cluster0.owfw2bj.mongodb.net/Cluster0?retryWrites=true&w=majority', tlsCAFile=ca)
db = client.dbsparta
```


### 3-14

- 연습문제 

```
movie_a = db.movies.find_one({'title':'가버나움'})
star_a = movie_a['star']

movies_a = list(db.movies.find({'star':star_a}))

for movie_b in movies_a:
    print(movie_b['title'])

```

### homework

```python
import requests
from bs4 import BeautifulSoup


headers = {'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64)AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36'}
data = requests.get('https://www.genie.co.kr/chart/top200?ditc=M&rtm=N&ymd=20210701',headers=headers)

soup = BeautifulSoup(data.text, 'html.parser')


musics = soup.select('#body-content > div.newest-list > div > table > tbody > tr')

#body-content > div.newest-list > div > table > tbody > tr:nth-child(3) >
for music in musics:
    title = music.select_one('td.info > a.title.ellipsis').text.strip()
    artist = music.select_one('td.info > a.artist.ellipsis').text
    rank = music.select_one('td.number').text[0:2]
    print(rank, title, artist)
```

##마치며
- name:value 로 적어야 하는걸 name=value 로 자꾸 적음. 주의해야 겠다