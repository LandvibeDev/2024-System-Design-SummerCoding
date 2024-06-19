# 0주차 과제 (사전과제)
## 1. Docker 학습하기
컨테이너의 개념과 docker 사용법을 학습하세요

### 학습해야할 것
- 컨테이너의 개념
- 도커 이미지와 컨테이너 생성 종료 삭제 명령어
- 도커 볼륨/네트워크, 마운트
- docker exec 명령어

### 추천 학습자료
- [가장 빨리 만나는 도커](https://pyrasis.com/docker.html)
- [시작하세요 도커/쿠버네티스](https://product.kyobobook.co.kr/detail/S000001766450)

## 2. 모니터링 대시보드 구축
[스프링 프로젝트](https://github.com/LandvibeDev/2024-Ass-System-Design-SummerCoding/tree/ass0)를 모니터링 할 수 있는 대시보드를 구축합니다. 위 스프링 프로젝트는 다음과 같은 API를 제공합니다.
| URL | 기능 |
|-----|------|
|/     |현재 점유중인 메모리 사용량 출력|
|/{count}     |count MB만큼 메모리 사용량 증가|
|/clear     |전체 메모리 점유 해제|
|/gc     |강제로 gc 수행|
<br>
스프링 프로젝트를 로컬pc에 git clone 후 적절하게 수정하면서 prometheus와 grafana를 이용하여 모니터링 대시보드를 구축해보세요.

### 예시
<img width="70%" alt="image" src="https://github.com/LandvibeDev/2024-System-Design-SummerCoding/assets/86287506/e7c65a42-f398-43fa-a786-9fa58bb591fe">

