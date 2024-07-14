# Redis 컨테이너 생성

## redis.conf 생성

bind 0.0.0.0 작성 후 저장

## Redis 컨테이너 실행

```docker
docker run -i -t -d -v <로컬 redis.conf 경로>:/usr/local/etc/redis/redis.conf \\
-v <로컬 /data 경로>:/data --name redis -p 6379:6379 redis \\
redis-server /usr/local/etc/redis/redis.conf

```

## Redis 접속

```bash
$ docker exec -it <컨테이너id> /bin/bash
```

# 자료구조

## String

```bash
> SET hello world
OK

> GET hello
"world"

# NX 옵션을 사용하면 지정한 키가 없을 때에만 새로운 키를 저장한다
> SET hello newval NX
(nil)

# XX 옵션을 사용하면 반대로 키가 이미 있을 때에만 새로운 값으로 덮는다
> SET hello newval XX
OK

> Get hello
"newval"

> SET counter 100
OK

# INCR을 사용하면 저장된 데이터를 1씩 증가시킨다
> INCR counter
(integer) 101

> INCR counter
(integer) 102

# INCRBY를 사용하면 저장된 데이터를 입력한 값만큼 데이터를 증가시킨다
> INCRBY counter 50
(integer) 152

## DECR, DECRBY도 INCR, INCRBY와 동일하게 동작하고 감소시킨다
> DECR counter
(integer) 151

> DECRBY counter 50
(integer) 101

# MSET, MGET을 이용하면 한 번에 여러 키를 조작할 수 있다.
> MSET a 10 b 20 c 30
OK

> MGET a b c 
1) "10"
2) "20"
3) "30"
```

## List

```bash
> LPUSH mylist E
(integer) 1

> RPUSH myllist B
(intger) 2

> LPUSH mylist D A C B A
(integer) 7

# 맨 왼쪽 값은 인덱스 0에서부터 하나씩 증가 맨 오른쪽 값은 인덱스 -1에서부터 하나씩 감소한다
> LRANGE mylist 0 -1
1) "A"
2) "B"
3) "C"
4) "A"
5) "D"
6) "E"
7) "B"

> LRANGE mylist 0 3
1) "A"
2) "B"
3) "C"
4) "A"

> LPOP mylist
"A"

> LPOP mylist 2
1) "B"
2) "C"

> LRANGE mylist 0 -1
1) "A"
2) "D"
3) "E"
4) "B"

[x, y]에 해당하는 범위의 값만 남기고 자른다. LPOP과 다르게 값을 반환하지 않는다.
> LTRIM myllist 0 1
OK

> LRANGE mylist 0 -1
1) "A"
2) "D"

> RPUSH xlist A
(integer) 1

> RPUSH xlist B
(integer) 2

> RPUSH xlist C
(integer) 3

> RPUSH xlist D
(integer) 4

> LRANGE xlist 0 -1
1) "A"
2) "B"
3) "C"
4) "D"

> LINSERT xlist BEFORE B E
(integer) 5

> LRANGE xlist 0 -1
1) "A"
2) "E"
3) "B"
4) "C"
5) "D"

> LINDEX xlist 3
"C"
```

## Hash

```bash
> HSET Product:123 Name "Happy Hacking"
(integer) 1

> HSET Product:123 TypeID 35
(integer) 1

> HSET Product:123 Version 2002
(integer) 1

> HSET Product:234 Name "Track Ball" TypeID 32
(integer) 2

> HGET Product:123 TypeID
"35"

> HMGET Product:234 Name TypeID
1) "Track Ball"
2) "32"

> HGETALLL Product:234
1) "Name"
2) "Track Ball"
3) "TypeID"
4) "32"
```

## Set

```bash
> SADD myset A
(integer) 1

> SADD myset A A A C B D D E F F F F F G
(integer) 6

> SMEMBERS myset
1) "D"
2) "F"
3) "C"
4) "G"
5) "B"
6) "A"
7) "E"

> SREM myset B
(integer) 1

# 랜덤한 값이 제거된다
> SPOP myset
"C"

> SADD xset D E F G H I
(integer) 6

# 두 set의 교집합
> SINTER myset xset
1) "D"
2) "E"
3) "F"
4) "G"

# 두 set의 합집합
> SUNION myset xset
1) "A"
2) "D"
3) "E"
4) "F"
5) "G"
6) "H"
7) "I"

# myset - xset 차집합
> SDIFF myset xset
1) "A"
```

## Sorted Set

```bash
> ZADD score:220817 100 user:B
(integer) 1

> ZADD score:220817 150 user:A 150 user:C 200 user:F 300 user:E
(integer) 4

# 인덱스를 이용하여 스코어를 기준으로 정렬된 데이터를 조회할 수 있다
# WITHSCORE 옵션을 사용하면 데이터와 함께 스코어 값이 차례대로 출력된다
> ZRANGE score:220817 1 3 WITHSCORES
1) "user:A"
2) "150"
3) "user:C"
4) "150"
5) "user:F"
6) "200"

# REV 옵션을 사용하면 데이터는 역순으로 출력된다
> ZRANGE score:220817 1 3 WITHSCORES REV
1) "user:F"
2) "200"
3) "user:C"
4) "150"
5) "user:A"
6) "150"

> ZRANGE score:220817 0 -1
1) "user:B"
2) "user:A"
3) "user:C"
4) "user:F"
5) "user:E"

> ZRANGE score:220817 0 -1 REV
1) "user:E"
2) "user:F"
3) "user:C"
4) "user:A"
5) "user:B"

# BYSCORE 옵션을 사용하면 스코어를 이용해 데이터를 조회할 수 있다
> ZRANGE score:220817 100 150 BYSCORE WITHSCORES
1) "user:B"
2) "100"
3) "user:A"
4) "150"
5) "user:C"
6) "150"

# 인수로 전달하는 스코어에 ( 문자를 추가하면 해당 스코어를 포함하지 않는 값만 조회할 수 있다
> ZRANGE score:220817 (100 150 BYSCORE WITHSCORES
1) "user:A"
2) "150"
3) "user:C"
4) "150"

> ZRANGE score:220817 100 (150 BYSCORE WITHSCORES
1) "user:B"
2) "100"

# +inf -inf는 최댓값 최솟값을 의미한다
> ZRANGE score:220817 200 +inf BYSCORE WITHSCORES
1) "user:F"
2) "200"
3) "user:E"
4) "300"

> ZRANGE score:220817 +inf 200 BYSCORE WITHSCORES REV
1) "user:E"
2) "300"
3) "user:F"
4) "200"

> ZADD mySortedSet 0 apple 0 banana 0 candy 0 dream 0 egg 0 frog
(integer) 6

# BYLEX 옵션을 사용하면 사전식 순서를 이용해 특정 아이템을 조회할 수 있다
# 이 때 반드시 (나 [ 문자를 함께 입력해야한다
> ZRANGE mySortedSet (b (f BYLEX
1) "banana"
2) "candy"
3) "dream"
4) "egg"
```

## Bitmap

```bash
> SETBIT mybitmap 2 1
(integer) 1

> GETBIT mybitmap 2
(integer) 1

> STRLEN mybitmap 
(integer) 1

> SETBIT mybitmap 255 1
(integer) 1

> STRLEN mybitmap
(integer) 32

# 100번째 비트부터 8비트를 설정하여 값 255를 저장합니다
> BITFILED mybitfield SET u8 100 255
1) (integer) 0

# 100(8*12 + 4) + 8(8*1) = 108(8*13 + 4)
> STRLEN mybitfield
(integer) 14

> BITFIELD mybitmap SET u1 6 1 SET u1 10 1 SET u1 14 1
1) (integer) 0
2) (integer) 0
3) (integer) 0

> GETBIT mybitmap 6
(integer) 1
```

## 키의 자동 생성과 삭제

```bash
> flushall
OK

# 키가 존재하지 않을 때 아이템을 넣으면 아이템을 삽입하기 전에 빈 자료구조를 생성한다
> LPUSH mylist 1 2 3
(integer) 3

# 저장하고자 하는 키에 다른 자료구조가 이미 생성돼 있을 때 아이템을 추가하는 작업을 에러를 반환한다
> SET hello world
OK

> LPUSH hello 1 2 3
(error) WRONGTYPE Operation against a key holding the wrong kind of value

> TYPE hello
string

# 모든 아이템을 삭제하면 키도 자동으로 삭제된다(stream은 예외)
> LPUSH mylist 1 2 3
(integer) 3

> EXISTS mylist
(integer) 1

> LPOP mylist
"3"

> LPOP mylist
"2"

> LPOP mylist
"1"

> EXISTS mylist
(integer) 0

# 키가 없는 상태에서 키 삭제, 아이템 삭제, 자료구조 크기 조회 같은 읽기전용 커맨드를
# 수행하면 에러를 반환하는 대신 키가 있으나 아이템이 없는 것처럼 동작한다
> DEL mylist
(integer) 0

> LLEN mylist
(integer) 0

> LPOP mylist
(nil)
```

## 키와 관련된 커맨드

```bash
# EXIST는 키가 존재하는지 확인하는 커맨드다. 키가 존재하면 1, 없으면 0을 반환한다
> SET hello world
OK

> EXISTS hello
(integer) 1

> EXISTS world
(integer) 0

# KEYS는 저장된 모든 키를 조회하는 커맨드고 글롭 패턴 스타일의 패턴을 추가하여 조회할 수 있다
# 수백만개의 키가 있을 때 모든 key를 조회하면 싱글스레드로 동작하는 레디스가 멈출 수 있다
> SET tello world

> KEYS *
1) "tello"
2) "hello"

> KEYS t*
1) "tello"

# SCAN 실습을 위해 무작정 많은 키를 추가한다
> MSET dfaf gva adfav asdf adfa dafa
OK

# SCAN은 커서를 기반으로 특정 범위의 키만 조회할 수 있다
# SCAN을 수행하면 다음 인수로 사용해야 하는 커서 위치와 key들이 반환된다
> SCAN 0
1) "30"
2)  1) "faf"
    2) "00s0"
    3) "0afs0f00d0f00sdf0s0sdf"
    4) "qerq"
    5) "va"
    6) "f0"
    7) "f0adf"
    8) "czv"
    9) "c"
   10) "afa"
   
> SCAN 30
1) "29"
2)  1) "d"
    2) "tello"
    3) "b"
    4) "aff"
    5) "gg"
    6) "df"
    7) "0asf0a0as0"
    8) "aa"
    9) "fad"
   10) "agbadf"
   11) "a"   

# 다음 인수가 0이면 더 이상 조회할 KEY가 없다는 뜻이다   
> SCAN 29
1) "0"
2)  1) "qer"
    2) "adfa"
    3) "f"
    4) "af0asf0af0af0"
    5) "hello"
    6) "werw"
    7) "f00f"
    8) "fa0"
    9) "afaf"
   10) "0f0"   
   
# TYPE을 이용하여 원하느 자료구조의 key만 조회할 수 있다.
> SCAN 0 TYPE STRING
1) "30"
2)  1) "faf"
    2) "00s0"
    3) "0afs0f00d0f00sdf0s0sdf"
    4) "qerq"
    5) "va"
    6) "f0"
    7) "f0adf"
    8) "czv"
    9) "c"
   10) "afa" 
   
> LPUSH mylist 1
(integer) 1

> LPUSH mylist 3
(integer) 2

> LPUSH mylist 2
(integer) 3

# SORT를 이용하면 list, set, sorted set의 내부 아이템을 정렬해 반환한다
> SORT mylist
1) "1"
2) "2"
3) "3"

> DEL mylist
(integer) 1

> LPUSH mylist banana
(integer) 1

> LPUSH mylist cat
(integer) 2

> LPUSH mylist apple
(integer) 3

> SORT mylist
(error) ERR One or more scores cant be converted into double

# ALPHA를 이용하여 사전순으로 정렬할 수 있다
> SORT mylist ALPHA
1) "apple"
2) "banana"
3) "cat"

> SET a apple
OK

> SET b banana
OK

> RENAME a aa
OK

> GET aa
"apple"

# RENAMENX는 변경할 키가 존재하지 않을 때에만 동작한다
> RENAMENX aa b
(integer) 0

> GET aa
"apple"

> GET b
"banana"

> flushall
OK

> SET B BANANA
OK

# COPY를 이용하여 키를 복사할 수 있다. 
> COPY B BB
(integer) 1

> GET B
"BANANA"

> GET BB
"BANANA"

> SET A APPLE
OK

> COPY B A
(integer) 0

> GET A
"APPLE"

# 이미 존재하는 key에 대해 복사할경우 REPLACE 옵션을 사용해야한다.
> COPY B A REPLACE
(integer) 1

> GET A
"BANANA"

> SET time x
OK

# EXPIRE을 이용하여 키 만료시간을 지정할 수 있고 TTL을 이용하여 몇 초 뒤에 만료되는지 알 수 있다
> EXPIRE time 60
(integer) 1

> TTL time
(integer) 57

```
