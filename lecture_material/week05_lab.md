# elasticsearch 설치

```bash
# elasticsearch 컨테이너 실행
$ docker run -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -it -d --name es01 docker.elastic.co/elasticsearch/elasticsearch:8.7.0

# elasticsearch 설정파일을 컨테이너에서 로컬환경으로 copy
$ docker cp es01:/usr/share/elasticsearch/config <로컬 경로>

# elasticsearch 설정파일 vim으로 열기
$ vim elasticsearch.yml

```

```bash
cluster.name: "docker-cluster"
network.host: 0.0.0.0

#----------------------- BEGIN SECURITY AUTO CONFIGURATION -----------------------
#
# The following settings, TLS certificates, and keys have been automatically
# generated to configure Elasticsearch security features on 01-08-2024 04:23:52
#
# --------------------------------------------------------------------------------

# Enable security features
xpack.security.enabled: false

xpack.security.enrollment.enabled: false

# Enable encryption for HTTP API client connections, such as Kibana, Logstash, and Agents
xpack.security.http.ssl:
  enabled: false
  keystore.path: certs/http.p12

# Enable encryption and mutual authentication between cluster nodes
xpack.security.transport.ssl:
  enabled: false
  verification_mode: certificate
  keystore.path: certs/transport.p12
  truststore.path: certs/transport.p12
#----------------------- END SECURITY AUTO CONFIGURATION -------------------------
```

```bash
# 로컬에서 수정한 elasticsearch.yml을 컨테이너로 복사
$ docker cp <로컬 elasticsearch.yml 경로> es01:/usr/share/elasticsearch/config/elasticsearch.yml

# 컨테이너 재실행
$ docker restart es01

# 컨테이너 정상수행 확인
$ curl -XGET "localhost:9200"
```

## kibana 설치

```bash
# kibana 컨테이너 실행
$ docker run -it -d --link es01 -p 5601:5601 --name kibana [docker.elastic.co/kibana/kibana:8](http://docker.elastic.co/kibana/kibana:8).14.3

$ docker cp kibana:/usr/share/kibana/config <로컬 경로>

$ vim kibana.yml
```

```bash
#
# ** THIS IS AN AUTO-GENERATED FILE **
#

# Default Kibana configuration for docker target
server.host: "0.0.0.0"
server.shutdownTimeout: "5s"
elasticsearch.hosts: [ "http://es01:9200" ]
monitoring.ui.container.elasticsearch.enabled: true
```

```bash
# 컨테이너 재실행
$ docker restart kibana

# localhost:5601 접속
```

```bash
$ wget http://media.sundog-soft.com/es/ml-latest-small.zip
$ unzip ml-latest-small.zip
```

```json
# movies 인덱스 맵핑정보 추가
PUT /movies
{
  "mappings": {
    "properties": {
      "year":{
        "type": "date"
      }
    }
  }
}

# movies 인덱스의 맵핑정보 조회
GET /movies/_mapping

# movies 인덱스에 document 추가
PUT /movies/_doc/109487
{
  "genre" : ["IMAX","Sci-Fi"],
  "title" : "Interstellar",
  "year" : 2014
}

# movies 인덱스에 존재하는 모든 documents 조회
GET /movies/_search

# bulk로 다양한 연산을 한 번에 가능
# http://media.sundog-soft.com/es8/movies.json 에서 다운로드 가능
PUT /_bulk
{ "create" : { "_index" : "movies", "_id" : "135569" } }
{ "id": "135569", "title" : "Star Trek Beyond", "year":2016 , "genre":["Action", "Adventure", "Sci-Fi"] }
{ "create" : { "_index" : "movies", "_id" : "122886" } }
{ "id": "122886", "title" : "Star Wars: Episode VII - The Force Awakens", "year":2015 , "genre":["Action", "Adventure", "Fantasy", "Sci-Fi", "IMAX"] }
{ "create" : { "_index" : "movies", "_id" : "109487" } }
{ "id": "109487", "title" : "Interstellar", "year":2014 , "genre":["Sci-Fi", "IMAX"] }
{ "create" : { "_index" : "movies", "_id" : "58559" } }
{ "id": "58559", "title" : "Dark Knight, The", "year":2008 , "genre":["Action", "Crime", "Drama", "IMAX"] }
{ "create" : { "_index" : "movies", "_id" : "1924" } }
{ "id": "1924", "title" : "Plan 9 from Outer Space", "year":1959 , "genre":["Horror", "Sci-Fi"] }

# PUT으로 동일한 id에 덮어쓰기 가능, 이 때 매번 version이 증가
PUT /movies/_doc/109487
{
  "genre" : ["IMAX","Sci-Fi"],
  "title" : "Interstellar foo",
  "year" : 2014
}

# 특정 필드만 변경할 때는 POST로 _update수행하면됨. 이 때는 변경사항 있을 때만 version 증가
POST /movies/_update/109487
{
  "doc":{
    "title": "Interstellar poo"
  }
}

# document 삭제
DELETE /movies/_doc/109487

# title이 star를 포함하는 document를 반환한다.
# URL에 검색할 정보를 담으면 보안에 취약하고 인코딩하는 cost가 발생한다.
GET /movies/_search?q=title:star

# query DSL을 이용하여 title에 star가 포함되는 document를 반환한다.
# match는 해당 text를 포함하는 document를 반환한다.
GET /movies/_search
{
  "query": {
    "match": {
      "title" : "star"
    }
  }
}

# term은 정확하게 일치하는 document를 반환한다.
GET /movies/_search
{
  "query": {
    "match": {
      "term" : "star"
    }
  }
}

# analyzer는 0개이상의 캐릭터필터, 1개의 토크나이저, 0개 이상의 토큰 필터로 구성된다.
# 캐릭터 필터는 텍스트를 캐릭터의 스트림으로 받아서 특정한 문자를 추가, 변경, 삭제한다.

# 문자열을 standard analyzer로 분석
POST _analyze
{
  "analyzer": "standard",
  "text": "Hello, HELLO, WORLD!"
}

# 문자열을 html_stript 캐릭터필터로 필터링
POST _analyze
{
  "char_filter": ["html_strip"],
  "text": "<p>HEllo hello</p>!"
}

# 문자열을 keyword 토크나이저로 토큰화
POST _analyze
{
  "tokenizer": "keyword",
  "text": "Hello, HELLO, World!"
}

# 문자열을 ngram 토크나이저로 토큰화
POST _analyze
{
  "tokenizer": {
    "type": "ngram",
    "min_gram": 3,
    "max_gram": 4
    },
  "text": "Hello, HELLO, World!"
}

# 문자열을 ngram으로 토큰화할 때 토큰에 담을 내용을 지정
# Letter, Digit, whitespace, punctuation, symbol, custom
POST _analyze
{
  "tokenizer": {
    "type": "ngram",
    "min_gram": 3,
    "max_gram": 4,
    "token_chars": ["letter"]
    },
  "text": "Hello, HELLO, World!"
}

# edge_ngram은 token_chars에 지정되지 않은 문자를 기준으로 삼아 단어 단위로 쪼갠 후 ngram한다.
POST _analyze
{
  "tokenizer": {
    "type": "edge_ngram",
    "min_gram": 3,
    "max_gram": 4,
    "token_chars": ["letter"]
    },
  "text": "Hello, HELLO, World!"
}

# 토큰 필터는 토큰 스트림을 받아서 토큰을 추가, 변경, 삭제한다.
# lowercase/uppercase, stop, synonym, patter_replace, stemmer, trim, truncate
POST _analyze
{
  "filter": ["lowercase"],
  "text": "Hello, World!"
}

# 내장 analyzer에서는 standard, simple, whitespace, stop, keyword, pattern, language, fingerprint가 있다.

# custom tokenizer와 token filter를 만들고 custom analyzer를 생성할 수 있다.
# 인덱스 초기 맵핑시 analzer를 필드별로 지정해주어야한다. 수정시 인덱스를 다시 생성해야한다.
PUT /movies
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "my_tokenizer": {
          "type": "standard"
        }
      },
      "filter": {
        "my_filter": {
          "type": "lowercase"
        }
      },
      "analyzer": {
        "my_custom_analyzer": {
          "type": "custom",
          "tokenizer": "my_tokenizer",
          "filter": ["my_filter"]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "my_custom_analyzer",
        "search_analyzer": "standard_analyzer"
      },
      "id": {
        "type": "keyword"
      },
      "year": {
        "type": "integer"
      },
      "genre": {
        "type": "keyword"
      }
    }
  }
}

# 쿼리 검색
GET /movies/_search
{
  "query": {
    "match": {
      "title" : "Star"
    }
  }
}

DELETE /movies

# ngram으로 생성하자.
PUT /movies
{
  "settings": {
    "analysis": {
      "tokenizer": {
        "ngram_tokenizer": {
          "type": "ngram",
          "min_gram": 3,
          "max_gram": 4
        }
      },
      "filter": {
        "my_filter": {
          "type": "lowercase"
        }
      },
      "analyzer": {
        "my_custom_analyzer": {
          "type": "custom",
          "tokenizer": "ngram_tokenizer",
          "filter": ["my_filter"]
        },
        "standard_analyzer": {ㅁㅁ 
          "type": "standard"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "my_custom_analyzer",
        "search_analyzer": "standard_analyzer"
      },
      "id": {
        "type": "keyword"
      },
      "year": {
        "type": "integer"
      },
      "genre": {
        "type": "keyword"
      }
    }
  }
}

# ngram으로 검색가능하다.
GET /movies/_search
{
  "query": {
    "match": {
      "title" : "Sta"
    }
  }
}
```

```bash
# mysql 도커 컨테이너 실행
$ docker run -it -d --name mysql -e MYSQL_ROOT_PASSWORD=root -p 3306:3306 mysql:latest

# mysql 컨테이너 내부 접속
$ docker exec -it mysql /bin/bash

# mysql 클라이언트 접속
$ mysql -u root -p

> CREATE DATABASE movies;
> USE movies;
> GRANT ALL PRIVILEGES ON *.* to root@'%';
> FLUSH PRIVILEGES;

>CREATE TABLE movies (
    id INT PRIMARY KEY,
    title VARCHAR(255),
    genres VARCHAR(255)
);

$ pip3 install pandas mysql-connector-python

$ vim insert_movie.py

import pandas as pd
import mysql.connector
from mysql.connector import Error
```

```python
# CSV 파일 경로
csv_file_path = 'path/to/movies.csv'

# MySQL 데이터베이스 연결 설정
db_config = {
    'user': 'root',
    'password': 'your_password',
    'host': 'localhost',
    'database': 'movies_db'
}

def load_csv_to_mysql(csv_file_path, db_config):
    try:
        # MySQL 데이터베이스에 연결
        connection = mysql.connector.connect(**db_config)
        if connection.is_connected():
            cursor = connection.cursor()

            # CSV 파일 읽기
            df = pd.read_csv(csv_file_path)

            # 데이터베이스에 삽입
            for _, row in df.iterrows():
                sql = "INSERT INTO movies (id, title, genres) VALUES (%s, %s, %s)"
                cursor.execute(sql, tuple(row))
                connection.commit()

            print("CSV 파일의 데이터가 MySQL 데이터베이스에 성공적으로 삽입되었습니다.")

    except Error as e:
        print(f"Error: {e}")
    finally:
        if connection.is_connected():
            cursor.close()
            connection.close()
            print("MySQL 연결이 닫혔습니다.")

# 스크립트 실행
load_csv_to_mysql(csv_file_path, db_config)

$ python3 ./insert_movie.py

```

```bash
import mysql.connector
from mysql.connector import Error
from elasticsearch import Elasticsearch, helpers
```

```python

# MySQL 데이터베이스 연결 설정
db_config = {
    'user': 'root',
    'password': 'root',
    'host': 'localhost',
    'database': 'movies'
}

# Elasticsearch 연결 설정
es = Elasticsearch([{'host': 'localhost', 'port': 9200, 'scheme': 'http'}])

def fetch_movies_from_db():
    try:
        connection = mysql.connector.connect(**db_config)
        if connection.is_connected():
            cursor = connection.cursor(dictionary=True)
            cursor.execute("SELECT * FROM movies")
            result = cursor.fetchall()
            return result
    except Error as e:
        print(f"Error: {e}")
    finally:
        if connection.is_connected():
            cursor.close()
            connection.close()

def index_movies_to_es(movies):
    actions = [
        {
            "_index": "movies",
            "_id": movie["id"],
            "_source": {
                "title": movie["title"],
                "genres": movie["genres"]
            }
        }
        for movie in movies
    ]
    helpers.bulk(es, actions)

def main():
    movies = fetch_movies_from_db()
    if movies:
        index_movies_to_es(movies)
        print("Movies have been indexed successfully.")
    else:
        print("No movies found in the database.")

if __name__ == "__main__":
    main()
```

```java
@Data
@Document(indexName = "movies")
public class Movie {
    private int id;
    private String title;
    private String genres;
}
```

```java
spring.application.name=ass1
spring.datasource.url=jdbc:mysql://localhost:3306/movies
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.jpa.hibernate.ddl-auto=update

q

    implementation 'org.springframework.boot:spring-boot-starter-web'
    compileOnly 'org.projectlombok:lombok'
    annotationProcessor 'org.projectlombok:lombok'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
    implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    runtimeOnly 'com.mysql:mysql-connector-j'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
    implementation 'org.springframework.boot:spring-boot-starter-data-elasticsearch'
```

```java
public interface MovieRepository {
    public List<Movie> getByTitle(String title);
}
```

```java
@RequiredArgsConstructor
@Repository
public class MovieRDBRepository implements MovieRepository{

   private final JdbcTemplate jdbcTemplate;

    @Override
    public List<Movie> getByTitle(String title) {
        String sql = "SELECT * FROM movies WHERE title LIKE ?";
        String searchTitle = "%" + title + "%";
        return jdbcTemplate.query(sql, new Object[]{searchTitle}, new MovieRowMapper());
    }
}

class MovieRowMapper implements RowMapper<Movie> {
    @Override
    public Movie mapRow(ResultSet rs, int rowNum) throws SQLException {
        Movie movie = new Movie();
        movie.setId(rs.getInt("id"));
        movie.setTitle(rs.getString("title"));
        movie.setGenres(rs.getString("genres"));
        return movie;
    }
}
```

```java
@Repository
public interface MovieESRepository extends ElasticsearchRepository<Movie, String> {
    List<Movie> findByTitle(String title);
}
```

```java
    @GetMapping("/movies/rdb/{title}")
    public List<Movie> getMoviesByTitleFromRDB(@PathVariable("title") String title){
        return movieRDBRepository.getByTitle(title);
    }

    @GetMapping("/movies/es/{title}")
    public List<Movie> getMoviesByTitleFromES(@PathVariable("title") String title){
        return movieESRepository.findByTitle(title);
    }
```
