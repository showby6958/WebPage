## @ManyToOne
어떤 foregign key 컬럼이 가리키는 테이블 내용도 붙여서 같이 가져올 수 있다.
단점: SELECT 쿼리문이 많이 실행될 수 있다.
- N+1 문제

> N+1 문제: JPA에서 특정 객체를 대상으로 수행한 쿼리가 해당 객체가 가지고 있는 연관관계도 조회해서 N번의 추가적인 쿼리가 발생하는 문제
-> 데이터베이스 성능 저하 발생

-> @Query로
SQL 또는 JPQL을 사용하는것이 좋다.(참고로 JPQL은 alias 필수로 해야함)

모든 컬럼을 다 가져오기 때문에 password 같은 정보도 같이 들고오기 때문에 Dto를 만들어서 필요한 정보만 들고오는것이 좋다.

```
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "member_id", foreignKey = @ForeignKey(ConstraintMode.NO_CONSTRAINT))
private Member member;
```

### FetchType
```@ManyToOne(fetch = FetchType.EAGER)```
- 디폴트 값. 연관된 객체를 일단 다 들고오기 때문에 데이터베이스 부담이 큼
  
```@ManyToOne(fetch = FetchType.LAZY)```
- 해당 객체가 요청될 때만 조회가 일어나기 때문에 데이터베이스 부담이 적음

## 패치조인
JPQL은 결과를 반환할 때 연관관계까지 고려하지 않는다. 단지 SELECT 절에 지정한 엔티티만 조회할 뿐이다.
member 엔티티 조회를 위한 SELECT 쿼리가 데이터베이스 별도로 나간다.
이러한 문제를 해결하기 위해 페치조인을 사용한다.
```
@Query(value = "SELECT s FROM Sales s JOIN FETCH s.member")
List<Sales> customFindAll();
```
: 호출횟수 1번

SQL 한 번으로 연관된 엔티티들을 함께 조회할 수 있어서 SQL 호출 횟수를 줄여 성능을 최적화할 수 있다.

## @OneToMany
@ManyToOne 쓸 때, 상대편에 @OneToMany 를 꼭 붙이 필요는 없다.

- 테이블끼리 관계파악에 도움은 될 수 있다.
- 행 지울 때 관련된 행 자동삭제 기능 사용가능(orphanRemoval)



## 정리
1. 제2정규화 하면 데이터 정확도를 지킬 수 있다는 장점이 있다. 대신 나중에 데이터 출력시 귀찮아짐
2. @ManyToOne 쓰면 어떤foreign key 컬럼이 가리키는 테이블 내용도 붙여서 가져올 수 있다.
3. @ManyToOne 성능문제는 JOIN FETCH 문법을 써보자
4. 반대로 @OneToMany도 가능. 내 테이블의 id를 훔쳐서 쓰는 중인 다른 테이블의 행들을 출력가능.
