# 기말고사

![image](https://github.com/user-attachments/assets/cd3719e7-0a4d-4e42-b50d-3e4bb8d947a7)

## 목표
- 썸머코딩 기간동안 배운 기술들에 대한 기본적인 개념과 사용법을 이해했는지 확인
- 썸머코딩 기간동안 배운 내용을 복습

## 요구사항
### nginx 웹서버 구축
- 127.0.0.1:80 네트워크 사용하여 구축
- https로 외부네트워크와 통신하도록 ssl 인증환경 구축
- /api/redis로 시작하는 url 요청은 was3으로 전달
- `/api`로 시작하는 url 요청들을 was1, was2로 부하분산
- was1: 127.0.0.1:8081, was2: 127.0.0.1:8082

### 부하분산 WAS 동작방식
- /api/msg/{msg} url을 처리하고 해당 pathvariable을 string 형태로 kafka로 전송
#### kafka 정보
- 127.0.0.1:10000, 127.0.0.1:10001을 이용하여 2개의 브로커 생성
- bootstrap.server는 자유롭게 선정
- topic이름: topic_msg partition은 2개로 설정

### msg 처리 서버 개발
- kafka topic_msg 토픽으로부터 메시지를 전달받아 해당 메시지를 redis의 list에 저장
- 127.0.0.1:8082에 구축
- /api/redis로 요청이 오면 redis의 summer_coding을 모두 출력하는 페이지 반환
#### kafka 정보
- bootstrap.server는 127.0.0.1:10000으로 설정
- topic 이름: topic_msg
- consumer group id: summer_coding
#### redis 정보
- 127.0.0.1:6379에 구축
- list 자료구조 생성하고 key는 summer_coding으로 설정
