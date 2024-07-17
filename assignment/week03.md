# spring AOP로 나만의 캐싱기능 구현하기
## 요구사항
- \@CachingLandvibe 어노테이션이 붙은 메소드의 return할 객체를 캐싱. 예를 들어 return할 객체를 DB에서 가져올 수도 있지만 redis에 조회해서 가져올 수도 있음
- \@CachingLandvibe는 최소 2개의 속성을 제공해야하고 필요에따라 추가할 수 있음
  - key: redis에 저장할 key를 지정합니다. 이 때 key는 메소의 파리미터의 값으로 받아올 수 있어야함. 예를 들어 member:<member의 PK>가 key일 때ㅣ member의 PK는 메소드의 파라미터로 제공됨
  - cachemanager: 캐싱전략을 포함하고 있는 cache manager 객체의 정보. Member 객체를 조회하려고 할 경우 테이블 이름. TTL혹은 여러 캐싱전략들을 포함
- \@CacheoutLandvibe 어노테이션이 붙은 메소드는 파라미터로 넘어온 id를 기준으로 캐싱된 데이터를 삭제한다. 속성은 \@CachingLandvibe와 유사하게 구현
- MySQL에서 가져올 정보를 redis에 캐싱하는 용도로만 개발
- spring aop를 사용할 것
- DB 접근 기술은 자유롭게 사용가능. 다만 JDBC template 사용 추천
- 고민했던 부분을 블로그 포스팅으로 작성

## Hint
\@Cacheable에 대해서 알아보세요 :)
