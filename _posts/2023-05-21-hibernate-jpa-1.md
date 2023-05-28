# 0. 사전 지식

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/24401081-d26d-4d66-846e-0fe0b6ac8890)

- 흔히 3 Tier Architecture를 설명할때 위의 그림과 같이 설명한다.
- 맨 밑에 그려져 있는 Data Tier를 Data Access Layer, 즉 Persistence Layer라고 부른다.

- 여기서 Persistence라는 단어는 영속성이란 의미를 가지고 있다.

- (질문) 영속성이란 단어의 의미가 무엇이고, 왜 여기에 사용될까?
    - 영속성이란 데이터를 생성한 프로그램이 종료되더라도 사라지지 않는 데이터의 특성을 얘기 한다.
    - 영속성을 갖지 않는 데이터(예 : list)는 단순히 메모리 상에서만 존재하기 때문에 프로그램을 종료하면 해당 데이터는 사라지게 된다.
    - Persistence Layer는 데이터에 영속성을 부여해주는 계층이라는 의미이다.
    

- DB처리를 통한 영속성을 부여하는 경우 이를 직접 JDBC를 이용하여 구현 하기도 하고, Persistence framework를 이용하여 구현한다.
- 모든 Persistence Framework는 내부적으로 JDBC API를 이용한다.

### 참고) 데이터베이스 접근 기술 분류

- JDBC
- Persistence Framework
    - ORM
        - JPA
    - SQL Mapper
        - MyBatis, JdbcTemplate

- (질문) ORM과 SQL Mapper의 차이점 - 지향점(목표) 관점에서
    - ORM은 관계형 데이터베이스의 ‘관계’를 Object에 반영하자는 것이 목적이라면, SQL Mapper는 단순히 필드를 매핑시키는 것이 목적이라는 점에서 지향점의 차이가 있다.

## JPA

- 자바 ORM 기술에 대한 API 표준 명세로서, Java에서 제공하는 API이다.

### JPA 구성 요소

1. javax.persistance 패키지로 정의된 API 그 자체
2. JPQL(Java Persistence Query Language)
3. 객체/관계 메타데이터

- JPA의 대표적인 구현체는
    - Hibernate
    - EclipseLink
    - DataNucleus
    - OpenJPA
    - TopLink Essentials
- 등이 있다.

### Hibernate

- JPA의 구현체 중의 하나이다.
- Hibernate는 Repository계층을 관리함으로서 객체 지향적으로 데이터를 관리하게 되어, 개발자가 비지니스 로직에 집중 할 수 있는 환경을 제공한다.

# 1. Hibernate 아키텍처

## 전체 아키텍처 관점

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/a3980668-1848-4358-9207-b7124823bbe9)

## Hibernate API 관점

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/d45f9eab-d931-4160-8f82-4ee4b2e8d66b)

### **Configuration(설정)**

- 일반적으로 hybernate.properties 또는 hibernate.cfg.xml 파일로 작성된다. Java환경에서는 @Configuration으로 주석을 단 클래스가 Configuration이다. Session Factory에서 Java Application 및 Database와 함께 작업할 때 사용한다.

### **Session Factory**

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/d102c522-75fc-476d-a476-16835fe5235c)

- 사용자 어플리케이션에서 Session Factory에 Session 객체를 요청하면 Session Factory는 Configuration 정보를 사용하여 Session Object를 인스턴스화 한다.

> Most applications create a **Hibernate SessionFactory singleton that’s cached for the lifecycle of the app** because the object is resource-intensive to create.
> 

### **Session**

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/c6f41bdf-aac1-4270-a7a7-36d68c7f48e8)

- 응용 프로그램과 데이터베이스 간의 상호작용을 나타낸다.
- 세션의 인스턴스는 SessionFactory Bean으로 부터 생성된다.
- 세션 객체는 응용 프로그램과 데이터베이스에 저장된 데이터 간의 인터페이스를 제공한다.
- 수명이 짧은 객체이며 JDBC 연결을 래핑한다.
- 데이터의 1 차 수준 캐시 (필수)를 보유한다.
- org.hibernate.Session 인터페이스는 객체를 삽입, 갱신, 삭제하는 메소드를 제공한다.
- 또한 Transaction, Query 및 Criteria에 대한 팩토리 메소드를 제공한다.

### **Query**

- 응용프로그램이 하나 이상의 저장된 객체(Persistence Object)에 대해 데이터베이스를 쿼리할 수 있게한다.

### **Transaction**

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/7b1db08e-4b66-49bb-a89e-06e1ba40adc0)

- 데이터의 영속성을 달성시키고(commit) 예기치 못한 상황이 발생할 경우 롤백할 수 있게한다.

### **Persistent objects**

- Persistent objects는 관계형 데이터베이스의 한 필드로서 유지되는 평범한 오래된 Java 객체들(POJOs)이다.
- 구성 파일(hibernate.cfg.xml 또는 hybernate.properties)에서 구성하거나 @Entity 어노테이션으로 선언될 수 있다.

### **First-level cache**

- 데이터베이스와 상호 작용하는 동안 Hibernate Session 객체가 사용하는 기본 캐시를 나타낸다. Session cache라고도 하며, 현재 세션 내에서 객체를 현재 Session에 저장한다. Session에 저장된 객체에서 DB에 하는 모든 요청은 Session cache를 거친다. Session 객체가 활성중일 때만 Session cache를 사용할 수 있다.
- 1차 캐시는 영속성 컨텍스트 내부에 있고, 트랜잭션이 시작하고 종료할 때까지만 유효하다.

### **Second-level-cache**

- 여러 세션에 걸쳐 객체를 저장하는 데 사용된다(application 단위의 cache). Second-level-cache는 명시적으로 사용되며, 해당 캐시에 대한 캐시 공급자를 제공해야 한다. 일반적인  Second-level-cache 공급자 중 하나로 EhCache가 있다.
- 어플리케이션 단위의 캐시로서, 어플리케이션을 종료할 때까지 캐시가 유지된다.
- 엔티티 매니저를 통해 데이터를 조회할 때 우선 2차 캐시에서 찾고 없으면 데이터베이스에서 찾게된다.
- 2차 캐시는 동시성을 극대화하려고 캐시한 객체를 직접 반환하지 않고 복사본을 만들어서 반환한다.

### 1차, 2차 캐시 및 Hibernate & EhCache 관련 내용

[[JPA & Hibernate] First Level Cache & Second Level Cache](https://velog.io/@dnjscksdn98/JPA-Hibernate-First-Level-Cache-Second-Level-Cache)

# 2. 도메인 모델

## 매핑 유형

- Hibernate는 다음과 같은 유형으로 데이터를 구분한다.
    - 값 타입
        - 기본 타입
        - Embeddable 타입
        - Collection 타입 ( @ElementCollection 사용할때의 컬렉션을 의미 )
    - 엔티티 타입

## 기본 값

### 기본 값 타입은 흔히 말하는 primitive type 과 Wrapper Class와 
더불어 몇가지 클래스들을 포함

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/2b9921e0-65f8-41cd-9dc9-8458ecb7e266)

### `@Basic`

- 위 어노테이션은 기본 값 타입에 붙이는 용도지만, 기본적으로 묵시적으로 인식 되므로 생략 가능하다.

```java
@Entity(name = "Product")
public class Product {

	@Id
	@Basic
	private Long id;

	@Basic
	private String name;
}
```

- 어노테이션의 옵션들
    - optional=true (기본값)
    - fetch=FetchType.EAGER (기본값)

- fetch 에서 LAZY는 런타임 바이트 코드 향상 기능을 통해 사용 가능하다.

[Hibernate ORM 6.2.3.Final User Guide](https://docs.jboss.org/hibernate/orm/current/userguide/html_single/Hibernate_User_Guide.html#tooling-enhancement-runtime)

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/195df29c-d93f-4896-bea6-a30aecc4a0cc)

### `@Column`

- name=””
- unique=false
- nullable=true
- insertable=true
- updatable=true
- columnDefinition=””
    - 데이터베이스 컬럼 정보를 직접 줄 수 있다.
- length=255
    - String 타입에만 사용
- precision=0, scale=0
    - precision은 소수점을 포함한 전체 자리수를, scale은 소수의 자리수다.

```java
@Entity
public class User {
    @Id
    Long id;

    @Column(columnDefinition = "varchar(255) default 'John Snow'")
    private String name;

    @Column(columnDefinition = "integer default 25")
    private Integer age;

    @Column(columnDefinition = "boolean default false")
    private Boolean locked;
}
```

### `@Formula`

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/0aa9347a-e8af-48e5-add0-869d499eae3d)

- 조회용으로만 사용 가능
- 서브 쿼리 포함 가능
- 해당 필드로 접근할때 쿼리가 나간다.

### Hibernate는 기본 값을 어떻게 SQL에 매핑할까?

해당 내용은 Hibernate 6.0 버전에서 수정되었다…

- Hibernate 6.0 이전 버전 방식
    - org.hibernate.type.BasicTypeRegistry라고 불리는 하이버네이트 내부의 서비스가 이를 수행한다.
    - org.hibernate.type.BasicTypeRegistry는 필수적으로 org.hibernate.type.BasicType 인스턴스의 이름이 키로 지정된 맵 데이터 구조를 유지한다.
    - BasicTypeRegistry의 내부 기준선으로, 하이버네이트는 Java 타입에 대한 JDBC의 추천 매핑을 따른다.
- Hibernate 6.0 이후 방식 → 공식문서 및 다른 레퍼런스 부족…
    - 리플렉션을 주로 사용한다.
    - 적절한 타입을 찾지 못한 경우 JDBC 권장 타입을 사용한다.
    - 최악의 경우는 Serializable인 경우이고, binary 직렬화를 통해 처리한다.

### `@Enumerated`

- 열거형 매핑에 사용

```java
public enum PhoneType {
		LAND_LINE, MOBILE;
}
```

- ORDINAL
    - 열거형의 본래 값인 숫자로 저장 (LAND_LINE의 경우 0, MOBILE은 1)
- STRING
    - 열거형 자체의 문자열로 저장 (LAND_LINE의 경우 “LAND_LINE”, MOBILE은 “MOBILE”)

### Boolean

- boolean을 엔티티 클래스의 필드로 사용할 경우
    - Boolean을 DBMS에서 지원하는 경우 Boolean 사용
    - 그 외에 BIT, TINYINT, SMALLINT 중 하나를 사용

### Byte, Short, Integer, Long, BigInteger

- Byte ⇒ `TINYINT`
- Short ⇒ `SMALLINT`
- Integer ⇒ `INTEGER`
- Long ⇒ `BIGINT`
- BigInteger ⇒ `NUMERIC`

### Double, Float, BigDemical

- Double ⇒ `DOUBLE`, `FLOAT`, `REAL` or `NUMERIC`
- Float ⇒ `FLOAT`, `REAL` or `NUMERIC`
- BigDemical ⇒ `NUMERIC`

### Character

- `CHAR`

### String

- `VARCHAR`

### `@Lob` → String에 사용

- `TEXT`

### `@Nationalized` → String에 사용

- NVARCHAR (가변 유니코드 문자열)
- 읽어볼만한 레퍼런스 (NVARCHAR 관련)

[SQL Server 에게 String 이란? (NVARCHAR 인가 VARCHAR 인가) | 우아한형제들 기술블로그](https://techblog.woowahan.com/2605/)

### LocalDate, LocalDateTime, LocalTime

- LocalDate ⇒ `DATE`
- LocalDateTime ⇒ `TIMESTAMP`
- LocalTime ⇒ `TIME`

### UUID

- `BINARY`

## Embeddable & Embedded

- @Embeddable가 있는 상태로 구현된클래스를 다른 클래스의 필드로 사용할 때, 필드 위에  @Embedded를 붙인다.
- Embedded 타입의 사용 장점
    - 재사용
    - 높은 응집도
    - 객체에 해당 값 타입만 사용하는 의미있는 메서드를 만들 수 있다.

### Embedded 타입 재정의

- 임베디드 타입에 정의한 매핑정보를 재정의하려면 엔티티에 `@AttributeOverride`를 사용하면 된다. (같은 타입을 두개 이상 사용하는데, 컬렉션은 사용하지 않고 바로 여러 필드를 사용할 때)

```java
@Entity
public class Member {
  
  @Id @GeneratedValue
  private Long id;
  private String name;
  
  @Embedded
  Address homeAddress;
  
  @Embedded
  @AttributeOverrides({
    @AttributeOverride(name="city", column=@Column(name="COMPANY_CITY")),
    @AttributeOverride(name="street", column=@Column(name="COMPANY_STREET")),
    @AttributeOverride(name="zipcode", column=@Column(name="COMPANY_ZIPCODE"))
  })
  Address companyAddress;
}
```

### @EmbeddedId

- 복합키, 사용자가 정의한 새로운 키를 사용하고 싶을 때 사용한다.
- 클래스를 Id와 같이 사용하는 방법이다.
- 다음과 같은 기준을 만족해야 한다.
    - 클래스에는 **@Embeddable**이 붙어야 한다.
    - **@EmbeddedId** 어노테이션을 붙여줘야 한다. (필드로 사용하는 부분에)
    - **Serializable** 인터페이스를 구현해야 한다.
    - **기본 생성자**가 있어야 한다.
    - 식별자 클래스는 **public** 이어야 한다.

## 엔티티 타입

- 엔티티 POJO 요구사항
    - @Entity 어노테이션이 붙어야 한다.
    - 기본 생성자가 Public 혹은 Protected로 정의되어 있어야 한다.
    - enum이나 interface는 사용할 수 없다.

### @Id

- 식별자 PK 지정

### @Entity

- 엔티티임을 명시

### @Table

- catalog, schema, name 설정 가능
- 스키마를 DBMS가 지원하는 경우에만 schema 속성 사용 가능
    - 스키마는 카탈로그의 별칭이다.

### 엔티티에서의 Equals와 HashCode 재정의

- Java 공식문서의 Equals의 특성 (참고)
    - reflexive: `x.equals(x)` 는 항상 참이어야 한다.
    - symmetric: `x.equals(y)` 가 참이라면 `y.equals(x)` 역시 참이어야 한다.
    - transitive: `x.equals(y)` 가 참이고 `y.equals(z)` 가 참일 때 `x.equals(z)` 역시 참이어야 한다.
    - consistent: `x.equals(y)` 가 참일 때 equals 메서드에 사용된 값이 변하지 않는 이상 몇 번을 호출해도 같은 결과가 나와야 한다.
    - x가 null이 아닐 때 `x.equals(null)` 은 항상 거짓이어야 한다.
- (질문) 엔티티에 Equals와 HashCode 를 재정의하지 않았을 때의 문제점은?
    
    ![image](https://github.com/ohksj77/server-mentoring/assets/89020004/e9e85b71-9d93-4cff-9bec-7f86fe3cde95)
    
    - 일반적으로는 한 엔티티 매니저의 영속성 컨텍스트에서 1차 캐시를 이용해 같은 ID의 엔티티를 항상 같은 객체로 가져올 수 있다. 하지만 1차 캐시를 초기화한 후 다시 데이터베이스에서 동일한 엔티티를 읽어오는 경우 초기화 전에 얻었던 객체와 이후에 얻은 객체가 서로 다른 객체로 생성된다.
    - 그렇다면 어떻게 재정의해야 할까?
        - 가장 먼저 생각나는 방식은 PK 하나로 재정의 하는 것이다.
            - 하지만 이는 NPE가 발생할 수 있는 부분을 내재하고 있다.
            - 엔티티가 아직 영속성 컨텍스트에 관리되지 않는 trasient한 경우 (DB에 아직 저장하지 않은 경우 등) PK로만 만든 equals를 통해 비교하면 NPE가 발생한다.
        
        ### 해결 방법
        
        - NaturalId 혹은 비즈니스 키를 통해 해결하는 방법이 있다.
        - @NaturalId 어노테이션은 Unique와 인덱스 생성을 자동으로 도와주는 어노테이션이며, 비즈니스 키는 개발자가 스스로 Unique 하게 설정해놓은 회원의 이메일 혹은 책의 isbn 아이디 등을 말한다.

## 네이밍 전략

- 논리 이름 (테이블 명, 어트리뷰트 명) 등을 명시하지 않았을 때의 네이밍 전략에 대해 설명하고자 한다.

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/2118c1be-da60-4e27-ae09-f3de032761b5)

- ImplicitNamingStrategyJpaCompliantImpl 라는 구현체가 JPA에서 사용된다.
- 예시
    - StudyGroup 이 엔티티 클래스명이고, 따로 @Table 을 사용하지 않았다면
        - STUDY_GROUP 이 Database의 테이블 명으로 자동으로 설정된다.

## 접근 전략

- 엔티티의 어트리뷰트에 접근하는 방법을 정의할 수 있다.

### 필드 접근 방식

- 리플렉션을 사용한다.
- 필드를 직접 읽고 쓸 수 있다고 한다.
- @Access(AccessType.FIELD) 를 통해 사용한다.

### 속성 기반 접근 방식

- 함수 (getter, setter)를 통해 접근한다.
- @Id 어노테이션을 필드에 붙이지 않고, getter 함수 위에 붙여 암시적으로 속성 기반 접근 방식이라는 것을 보여준다.

```java
@Id
public Long getId() {
		return this.id;
}
```

### 필드 접근 방식의 장점

1. 불필요한 getter, setter 구현 불필요
2. 프록시 작업시 버그 방지
    1. Hibernate는 getter 메소드를 호출하는 시점에 값을 초기화 한다.
    2. equals를 프록시 객체로 사용하는 코드가 실행되면, equals는 필드를 직접 접근하기 때문에 npe와 같은 오류가 발생한다.

## 식별자

- 특징
    - UNIQUE, NOT NULL, IMMUTABLE

### @Id

- PK로 사용할 필드 위에 붙임
- 모든 primitive 타입, Wrapper 타입, String, java.util.Date, java.sql.Date, BigDemical, BigInteger에 사용할 수 있다.

### @GeneratedValue

- AUTO (default)
    - UUID의 경우 org.hibernate.id.UUIDGenerator를 사용한다.
    - 숫자 (Integer, Long) 타입의 경우 `org.hibernate.id.enhanced.SequenceStyleGenerator` 를 사용한다.
- IDENTITY
    - 공식문서에 따르면 SEQUENCE 사용을 더 권장한다. 이유는다음과 같다:
        - Hibernate는 플러시 모드가 AUTO가 설정해주는 모드와 같지 않다면 엔티티 삽입을 지연하려고 시도한다.
        - 동일한 트랜잭션에서 다른 엔터티와의 어떤 형태로든 연관되는 식별자를 사용하거나 생성한 엔터티의 경우 약간 문제가 있다.
        - 이러한 문제를 Hibernate는 삽입이 지연되어야 하는지 또는 즉시 삽입이 필요한지를 결정하는 알고리즘을 사용하여 해결하려고 시도한다.
        - 해당 엔티티에 대한 여러 INSERT 문을 한 번에 처리할 수 없다. (하나씩 처리하게 되고, 성능 이슈가 있을 수 있다.)
- SEQUENCE
    - @GeneratedValue의 strategy=SEQUENCE를 사용하고, generator=””를 사용해 sequence generator를 명시한다.
    - sequence generator는 @SequenceGenerator를 사용해 정의하고, 이 어노테이션은 name, sequenceName, allocationSize등의 속성을 가진다.
    
    ```java
    @SequenceGenerator(
    		name = "sequence-generator",
    		sequenceName = "explicit_product_sequence",
    		allocationSize = 5
    	)
    ```
    

### Optimizer

- GeneratedValue를 최적화 하는 방법을 설명하고자 한다.
    - pooled-io
    - 값을 요청할 때, 여러개의 id를 한 번에 만들어 놓고 그 값들을 다 사용할 때까지 사용한다. (개수는 설정 가능)
    
    ### 예시) 20개로 설정
    
    1. 시퀀스 첫 호출 → 1 ~ 20 까지 만들어서 가져옴
    2. 두 번째 호출 → 21 ~ 40

### @EmbeddedId

- 복합 키를 사용할 때 활용
- IdClass보다 좀 더 객체지향적인 방법
- @Embeddable 을 붙여서 구현한 클래스를 @EmbeddedId와 함께 필드로 사용하면 된다.

### @IdClass

- 복합 키를 사용할 때 활용

```java
@IdClass(UserId.class)
@Entity
public class User {

    @Id
    @Column(name = "USER_ID")
    private String id;

    @Id
    @Column(name = "REG_DATE")
    private LocalDateTime regDate;

    // 생략

}
```

- @Id가 붙은 필드가 두 개 이상이다.
- UserId 클래스

```java
@AllArgsConstructor
@NoArgsConstructor
public class UserId implements Serializable {

    private String id;
    private LocalDateTime regDate;

    // equals & hashCode 부분 생략
}
```

- id로 사용할 클래스 요구사항
    - **`public`** 클래스일 것
    - 기본 생성자 필수
    - 엔티티 클래스에서 작성한 필드 명과 동일하게 작성할 것 (컬럼명이 아님에 주의)
    - **`Serializable`**을 구현해야 함
    - **`equals`**와 **`hashCode`**를 구현해야 함

## Associations (관계 설정)

### ManyToOne

- FetchType.EAGER 가 디폴트

### OneToMany

- FetchType.LAZY가 디폴트

### OneToOne

- FetchType.EAGER 가 디폴트

### Any

- 상속관계의 다양한 엔티티를 연관관계로 가져야 할 때 사용

```java
@Entity
@Table(name = "property_holder")
public class PropertyHolder {

    @Id
    private Long id;

    @Any
    @AnyDiscriminator(DiscriminatorType.STRING)
    @AnyDiscriminatorValue(discriminator = "S", entity = StringProperty.class)
    @AnyDiscriminatorValue(discriminator = "I", entity = IntegerProperty.class)
    @AnyKeyJavaClass(Long.class)
    @Column(name = "property_type")
    @JoinColumn(name = "property_id")
    private Property property;

    //Getters and setters are omitted for brevity

}
```

- 위코드에서 Property는 인터페이스, StringProperty, IntegerProperty는 구현체다.

### ManyToAny

- Any의 양방향 연관관계 반대 방향에 사용

```java
@Entity
@Table(name = "property_repository")
public class PropertyRepository {

    @Id
    private Long id;

    @ManyToAny
    @AnyDiscriminator(DiscriminatorType.STRING)
    @Column(name = "property_type")
    @AnyKeyJavaClass(Long.class)
    @AnyDiscriminatorValue(discriminator = "S", entity = StringProperty.class)
    @AnyDiscriminatorValue(discriminator = "I", entity = IntegerProperty.class)
    @Cascade(ALL)
    @JoinTable(name = "repository_properties",
            joinColumns = @JoinColumn(name = "repository_id"),
            inverseJoinColumns = @JoinColumn(name = "property_id")
   )
    private List<Property<?>> properties = new ArrayList<>();

    //Getters and setters are omitted for brevity

}
```

### ManyToMany

- 다대다 관계에서 쓸 수 있고, 중간 엔티티를 자동으로 만들어주지만, 사용하지 말자.
- (질문) ManyToMany를 사용하면 안되는 이유
    
    중간 테이블에 칼럼 추가가 불가능하고, 세밀한 쿼리를 실행하기 어렵기 때문에 사용하지 않는 편이 좋다.
    

## 컬렉션

### `@ElementCollection`

- 값 타입을 리스트와 같은 컬렉션으로 가지고 있는 필드를 엔티티에 넣을 때 사용
- 값 타입은 Embeddable 타입도 포함

## Natural Id

### `@NaturalId`

- 값 타입에 사용한다고 생각할 수 있는데, 엔티티 필드 위에 사용할 수도 있고, 여러 필드에 한 번에 사용할 수도 있다.

## Partitioning

- (질문) 파티셔닝이 무엇인지와 얻는 이점은?
    - 논리적인 데이터 element들을 다수의 entity로 쪼개는 행위를 뜻하는 일반적인 용어
    - 즉 큰 table이나 index를, 관리하기 쉬운 partition이라는 작은 단위로 물리적으로 분할하는 것을 의미한다.
    - 물리적인 데이터 분할이 있더라도, DB에 접근하는 application의 입장에서는 이를 인식하지 못한다.
    
    ![image](https://github.com/ohksj77/server-mentoring/assets/89020004/fc109615-23f2-4170-b10c-f51b73a0853d)
    
    ![image](https://github.com/ohksj77/server-mentoring/assets/89020004/8693c2da-515a-4692-9ebc-178b3fbeeafa)
    
    - DBMS는 분할에 대해 각종 기준(분할 기법)을 제공하고 있다. 분할은 ‘분할 키(partitioning key)’를 사용한다.
    
    [[DB] DB 파티셔닝(Partitioning)이란 - Heee's Development Blog](https://gmlwjd9405.github.io/2018/09/24/db-partitioning.html)
    

### `@PartitionKey`

파티셔닝을 위한 파티셔닝 키를 지정한다. 필드위에 붙인다.

## Dynamic Model

→ XML 파일 사용하는 내용이므로 PASS

## 상속

- 공통 부분이 있는 DB 테이블을 상속하여 사용하는 방식으로 Java언어에서도 상속을 통해 구현한다.

### `@MappedSuperclass`

- 상속 관계의 상위 클래스( 상위 테이블 ) 임을 명일 테이블 전략

### 단일 테이블 전략

### `@Inheritance(strategy = InheritanceType.SINGLE_TABLE)`

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/9cd49c76-0332-446d-9bbc-bd095f36782d)

- `DTYPE`열은 연관된 하위 클래스 이름을 저장하는 판별자로 사용
- 어떤 하위 테이블인지 확인하기 위한 테이블 판별자 사용법

```java
@DiscriminatorFormula(
	"case when debitKey is not null " +
	"then 'Debit' " +
	"else (" +
	"   case when creditKey is not null " +
	"   then 'Credit' " +
	"   else 'Unknown' " +
	"   end) " +
	"end "
)
```

- 위와 같은 어노테이션으로 case문을 작성해 알아낸다.

### 조인 테이블 전략

### `@Inheritance(strategy = InheritanceType.JOINED)`

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/59948ab9-b82f-4220-8b8d-2137c07b3c89)

- 하위 테이블들이 상위 클래스의 id를 가지는 방법이다.

### 구현 클래스별 테이블 전략

### `@Inheritance(strategy = InheritanceType.TABLE_PER_CLASS)`

![image](https://github.com/ohksj77/server-mentoring/assets/89020004/80dce85d-84fa-4a43-be2c-a5194ed1a872)

- 각 하위 테이블이 상위 클래스의 필드를 다 가지고 있게 하는 방법이다.
- (질문) 위 세개 전략 중 쓰면 안좋은 전략과 이유
    
    ### 클래스별 테이블 전략은 쓰면 안좋다
    
    - ORM을 하다보면 데이터 쪽과 객체 쪽에서 trade off를 할 때가 있는데, 이 전략은 둘 다 추천하지 않는다.
        - 여러자식 테이블을 함께 조회할때 성능이 느리다. (SQL에 UNION을 사용해야한다)
        - 자식 테이블을 통합해서 쿼리하기 어렵다.
- 각 전략의 장단점 정리
    - 조인 전략
        - 장점
            - 테이블이 정규화가 되어있다.
            - 외래 키 참조 무결성 제약조건 활용 가능
                - ITEM의 PK가 ALBUM, MOVIE, BOOK의 PK이자 FK이다. 그래서 다른 테이블에서 아이템 테이블만 바라보도록 설계하는 것이 가능하다.
            - 저장공간 효율화
                - 테이블 정규화로 저장공간이 딱 필요한 만큼 소비된다.
        - 단점
            - 조회시 조인을 많이 사용한다. 단일 테이블 전략에 비하면 성능이 안나온다. 조인하니까.
            - 그래서 조회 쿼리가 복잡하다
            - 데이터 저장시에 INSERT 쿼리가 상위, 하위 테이블 두번 발생한다.
        - 정리
            - 성능 저하라고 되어있지만, 실제로는 영향이 크지 않다.
            - 오히려 저장공간이 효율화 되기 때문에 장점이 크다.
            - **기본적으로는 조인 정략이 정석**이라고 보면 된다. 객체랑도 잘 맞고, 정규화도 되고, 그래서 설계가 깔끔하게 나온다.
    - 단일 테이블 전략
        - 장점
            - 조인이 필요 없으므로 일반적인 조회 성능이 빠르다.
            - 조회 쿼리가 단순하다.
        - 단점
            - 자식 엔티티가 매핑한 컬럼은 모두 NULL을 허용해야 한다.
            - 단일 테이블에 모든 것을 저장하므로 테이블이 커질 수 있다.
            - 상황에 따라서 조인 전략보다 성능이 오히려 느려질 수 있다.
                - 보통 이 상황에 해당하는 임계점을 넘을 일은 많지 않다.
    - 구현 클래스마다 테이블 전략
        - 장점
            - 서브 타입을 명확하게 구분해서 처리할 때 효과적이다
            - NOT NULL 제약조건을 사용할 수 있다.
        - 단점
            - 여러 자식 테이블을 함께 조회할 때 성능이 느리다(UNION SQL)
            - 자식 테이블을 통합해서 쿼리하기 어렵다.
            - 변경이라는 관점으로 접근할 때 굉장히 좋지 않다.
                - 예를 들어, ITEM들을 모두 정산하는 코드가 있다고 가정할 때, ITEM 하위 클래스가 추가되면 정산 코드가 변경된다. 추가된 하위 클래스의 정산 결과를 추가하거나 해야 한다.
    - 상속관계 매핑 정리
        - 기본적으로는 조인 전략을 가져가자.
        - 그리고 조인 전략과 단일 테이블 전략의 trade off를 생각해서 전략을 선택하자.
        - 굉장히 심플하고 확장의 가능성도 적으면 단일 테이블 전략을 가져가자. 그러나 비즈니스 적으로 중요하고, 복잡하고, 확장될 확률이 높으면 조인 전략을 가져가자.

## Mutability

### 불변 엔티티 → `@Immutable` 을 클래스 레벨에 붙인다.

- 가질 수 있는 이점
    - 더티 체킹을 위해 로드된 상태로 유지시킬 메모리 감소
    - 영속성 컨텍스트 플러시 단계의 속도 향상

불변 속성 → `@Immutable` 을 필드 레벨에 붙인다.

불변 컬렉션 → `@Immutable` 을 컬렉션 필드에 붙인다.

## 도메인 모델 **Customizing**

→ 지엽적인 내용이므로 PASS