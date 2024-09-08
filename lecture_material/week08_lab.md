```
$ docker run -it -d --name nginx -p 80:80 -p 443:443 nginx
```

```
# 컨테이너의 nginx.conf 로컬로 복사
$ docker cp nginx:/etc/nginx/nginx.conf <옮길 디렉토리 경로>

# 기존 nginx.conf를 백업본으로 복사
$ cp nginx.conf nginx_backup.conf
```

```
$ docker cp <bloggingtemplate 경로> nginx:/
```

```nginx
events {
}


http {

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

  }
}
```

```
# nginx.conf 정상인지 확인
$ nginx -t

# nginx.conf를 반영하며 reload (이 때 nginx가 종료되지 않음)
$ nginx -s reload

# js 파일을 text/plain으로 전송하고 있는 것을 확인
$ curl -I http://127.0.0.1/assets/css/bootsnav.css
```

```nginx
events {
}


http {

  types{
    text/html html;
    text/css css;
  }

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

  }
}
```

```
# text/css로 전송하는 것을 확인
$ curl -I http://127.0.0.1/assets/css/bootsnav.css

# nginx에서 기본적으로 type에 대한 설정을 가짐을 확인
$ cat /etc/nginx/mime.types
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

  }
}
```

```nginx
location /images {
    # 이 블록은 "/images"로 시작하는 모든 URI에 매칭됩니다.
}

location = /images {
    # 정확히 "/images" 요청일 때만 이 블록이 사용됩니다.
}

location ~ \.jpg$ {
    # ".jpg"로 끝나는 URI에 대소문자 구분하여 매칭
}

location ~* \.jpg$ {
    # ".jpg", ".JPG" 등 대소문자 상관없이 매칭
}

location ^~ /images/ {
    # "/images/"로 시작하는 요청은 정규 표현식 매칭 없이 이 블록을 사용
}


# 우선순위: = > ^~(가장 긴 접두사) > (~, ~*)

server {
    location = /exact {
        # 정확히 "/exact" 요청일 때만 이 블록 사용
    }

    location ^~ /images/ {
        # "/images/"로 시작하면 정규 표현식 매칭 없이 이 블록 사용
    }

    location ~* \.jpg$ {
        # 대소문자 무시하고 ".jpg"로 끝나는 모든 요청에 매칭
    }

    location / {
        # 위 조건에 모두 해당되지 않으면 기본적으로 이 블록이 사용됨
    }
}
```

```nginx
events {
}


http {

include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    location /contactus {
      return 200 "Hello! you are inside custom location!!!";
    }

  }
}
```

```nginx
events {
}


include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    location = /contactus {
      return 200 "Hello! you are inside custom location!!!";
    }

  }
}
```

```nginx
events {
}


include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    location  /contactus {
      return 200 "Hello! you are inside custom location!!!";
    }

    location = /contactus {
      return 200 "Hello! you are inside custom location- EXACT MATCH!!!";
    }

  }
}
```

```nginx
events {
}


include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    location ~ /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE SENSITIVE MATCH!!!";
    }

  }
}
```

```nginx
events {
}


include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    location ~* /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE INSENSITIVE MATCH!!!";
    }

  }
}
```

```nginx
events {
}


http {

include /etc/nginx/mime.types;

  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    location ~* /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE INSENSITIVE MATCH!!!";
    }

    location /find {
      return 200 "$hostname \n $args \n $connection_requests \n $nginx_version";
    }

  }
}
```

```
http://127.0.0.1/find
http://127.0.0.1/find?apiKeys=3
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    if ($arg_name = 'landvibe'){
      return 200 "Landvibe!!!";
    }

    location ~* /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE INSENSITIVE MATCH!!!";
    }

    location /find {
      return 200 "$hostname \n $args \n $connection_requests \n $nginx_version";
    }

  }
}
```

```
127.0.0.1?name=landvibe
127.0.0.1?name=adfaf
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    if ($arg_name = 'landvibe'){
      return 200 "Landvibe!!!";
    }

    location /welcome{
      return 301 /assets/images/about/welcome-banner.jpg;
    }

    location ~* /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE INSENSITIVE MATCH!!!";
    }

    location /find {
      return 200 "$hostname \n $args \n $connection_requests \n $nginx_version";
    }

  }
}
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    if ($arg_name = 'landvibe'){
      return 200 "Landvibe!!!";
    }

    rewrite ^/guest/\w+ /welcome;

    location /welcome{
      return 301 /assets/images/about/welcome-banner.jpg;
    }

    location ~* /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE INSENSITIVE MATCH!!!";
    }

    location /find {
      return 200 "$hostname \n $args \n $connection_requests \n $nginx_version";
    }

  }
}
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    if ($arg_name = 'landvibe'){
      return 200 "Landvibe!!!";
    }

    rewrite ^/guest/\w+ /welcome;

    rewrite ^/user/(\w+) /welcome/$1;

    location = /welcome/landvibe{
      return 200 "Hello Landvibe, Welcome";
    }

    location /welcome/landvibe{
      return 307 "This is Landvibe!!!";
    }

    location /welcome{
      return 301 /assets/images/about/welcome-banner.jpg;
    }

    location ~* /*.ctus {
      return 200 "Hello! you are inside custom location- REGEX CASE INSENSITIVE MATCH!!!";
    }

    location /find {
      return 200 "$hostname \n $args \n $connection_requests \n $nginx_version";
    }

  }
}
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    try_files /testobject /video;

    location /video {
      return 200 "Enjoy the Movie!!!";
    }

  }
}
```

```nginx
events {                                                                                        │~
}                                                                                               │~
                                                                                                │~
                                                                                                │~
http {                                                                                          │~
                                                                                                │~
  include /etc/nginx/mime.types;                                                                │~
                                                                                                │~
                                                                                                │~
  server {                                                                                      │~
                                                                                                │~
    listen 80;                                                                                  │~
    server_name 127.0.0.1;                                                                      │~
    root /bloggingtemplate/;                                                                    │~
                                                                                                │~
    try_files /assets/images/about/profile_image.jpg /video;                                    │~
                                                                                                │~
    location /video {                                                                           │~
      return 200 "Enjoy the Movie!!!";                                                          │~
    }                                                                                           │~
                                                                                                │~
  }                                                                                             │~
}
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    try_files $uri /assets/images/about/profile_image.jpg /video;

    location /video {
      return 200 "Enjoy the Movie!!!";
    }

  }
}
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    try_files $uri /assets/images/profile_image.jpg /video;

    location /video {
      return 200 "Enjoy the Movie!!!";
    }

  }
}
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 80;
    server_name 127.0.0.1;
    root /bloggingtemplate/;

    try_files $uri /assets/images/profile_image.jpg /video /404;

    location /video {
      return 200 "Enjoy the Movie!!!";
    }

    location /404 {
      return 404 "Sorry, this resource doesn't existing";
    }

  }
}
```

```
# ssl 인증서(서버신원 및 공개키를 CA 개인키로 암호화) 및 서버 개인키 발급 
$ openssl req -x509 -days 100 -nodes -newkey rsa:2048 -keyout ./self.key -out ./self.cert

# 컨테이너에 ssl 디렉토리 생성
$ cd /etc/nginx
$ mkdir ssl

# 로컬에서 만든 인증서와 키를 이동
$ docker cp self.key nginx:/etc/nginx/ssl
$ docker cp self.cert nginx:/etc/nginx/ssl
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 443 ssl;
    listen 80;
    server_name 127.0.0.1;

    ssl_certificate /etc/nginx/ssl/self.cert;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    root /bloggingtemplate/;

  }
}
```

```
nginx -s reload
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;


  server {

    listen 443 ssl http2;
    listen 80;
    server_name 127.0.0.1;

    ssl_certificate /etc/nginx/ssl/self.cert;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    root /bloggingtemplate/;

  }
}
```
```
# 경량 부하테스트 도구 siege 설치
$ brew install siege

# 2개의 요청씩 5개의 커넥션으로 보낸다 (총 10개)
$ siege -v -r 2 -c 5 https://127.0.0.1/assets/js/custom.js
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;

  limit_req_zone $request_uri zone=test_zone:10m rate=1r/s;


  server {

    listen 443 ssl http2;
    listen 80;
    server_name 127.0.0.1;

    ssl_certificate /etc/nginx/ssl/self.cert;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    root /bloggingtemplate/;

    location / {
      limit_req zone=test_zone;
    }

  }
}
```

```
# 2개의 요청씩 5개의 커넥션으로 보낸다 (총 10개)
$ siege -v -r 2 -c 5 https://127.0.0.1/assets/js/custom.js
```

```nginx
# burst = 5로 주면 초과된 5개의 요청은 임시보관되고 토큰이 생길 때마다 요청처리한다
events {
}


http {

  include /etc/nginx/mime.types;

  limit_req_zone $request_uri zone=test_zone:10m rate=1r/s;


  server {

    listen 443 ssl http2;
    listen 80;
    server_name 127.0.0.1;

    ssl_certificate /etc/nginx/ssl/self.cert;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    root /bloggingtemplate/;

    location / {
      limit_req zone=test_zone burst=5;
    }

  }
}
```

```
# 2개의 요청씩 5개의 커넥션으로 보낸다 (총 10개)
$ siege -v -r 2 -c 5 https://127.0.0.1/assets/js/custom.js
```

```nginx
# nodelay 옵션을 주면 요청들 사이에 허용되는 시간을 제한하지 않는다
events {
}


http {

  include /etc/nginx/mime.types;

  limit_req_zone $request_uri zone=test_zone:10m rate=1r/s;


  server {

    listen 443 ssl http2;
    listen 80;
    server_name 127.0.0.1;

    ssl_certificate /etc/nginx/ssl/self.cert;
    ssl_certificate_key /etc/nginx/ssl/self.key;

    root /bloggingtemplate/;

    location / {
      limit_req zone=test_zone burst=5 nodelay;
    }

  }
}
```

```
# 2개의 요청씩 5개의 커넥션으로 보낸다 (총 10개)
$ siege -v -r 2 -c 5 https://127.0.0.1/assets/js/custom.js
```

```dockerfile
# 스프링 프로젝트 적당히 만든다음에 도커파일 생성
FROM openjdk:17
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

```
$ docker build --tag xeonu/lbtest:1.0.0 .

# 도커 컨테이너 생성 (3개 생성)
$ docker run -it -d -p 8081:8080 --name was_1 <컨테이너 이름> 

# 도커 네트워크 생성
$ docker create network my-network

# 기존 컨테이너에 도커 네트워크 추가
$ docker network connect nginx my-network
```

```nginx
events {
}


http {

  include /etc/nginx/mime.types;

    upstream appserver {
      server was_1:8080;
      server was_2:8080;
      server was_3:8080;
    }

  server {
    location / {
      proxy_pass http://appserver;
    }
  }
}
```

```
worker_processes 4;
worker_processes auto;
```
```
$ docker top <컨테이너 아이디>
```

