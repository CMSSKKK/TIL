### 학습자료

- 김영한, 자바 ORM 표준 JPA 프로그래밍 (+인프런 활용편1)
- https://jojoldu.tistory.com/415



## dirty cheking 과 merge 비교하기

- JPA에서 dirty checking 또는 merge를 통한 update의 동작방식과 차이에 대해서 알아보자
- 단순 예시를 위해서 setter를 활용해서 코드를 짰다. entity에는 기본적으로 setter는 열어두지 않는 것을 권장하며, data 수정은 명시적인 메서드(ex. changeName("updatedName") )를 만드는 것이 좋다!



## Dirty Checking

- JPA에서는 트랜잭션이 끝나는 시점에 변화가 있는 모든 엔티티 객체를 DB에 자동으로 반영한다.
- 엔티티의 조회 상태 그대로 영속성 컨텍스트에 보관하는데, 이것이  **스냅샷**

상태 변경 감지 대상은 영속성 컨텍스트가 관리하는 엔티티에만 적용된다. 

- detach된 엔티티 (준영속)
- DB에 반영되기 전 처음 생성된 엔티티 (비영속)

이러한 데이터는 dirty checking 대상이 아니고 엔티티(값)이 update 되지 않는다.

1. 트랜잭션 커밋 시점에 엔티티매니저가 내부적으로 flush()를 호출
2. 엔티티와 스냅샷을 변경 검사
3. 수정쿼리를 생성해서 쓰기 지연 sql 저장소로 전송
4. sql 저장소에서 update query를 DB로 전송
5. 트랜잭션 커밋

JPA의 기본전략으로 생성되는 수정쿼리는 **엔티티의 모든 필드를 업데이트** --> 권장

전체 필드를 업데이트하는 방식(정적 수정 쿼리)의 장점? **수정 쿼리가 항상 같다** 

- 생성되는 쿼리가 같아 부트 실행시점에 미리 만들어서 재사용 가능 
- 데이터베이스 입장에서 쿼리 재사용이 가능하다
  - 동일한 쿼리를 받으면 이전에 파싱된 쿼리를 재사용한다.

동적 수정 쿼리를 사용하려면? `@DynamicUpdate` 어노테이션을 활용한다. 

- 변경된 필드에 대해서만 수정하는 쿼리를 생성하여 보낸다.

> `@DynamicInsert ` : null이 아닌 데이터(필드)에 대해서만 insert query를 생성하는 어노테이션



## Merge

- 준영속, 비영속 상태의 엔티티를 다시 영속 상태로 바꾸는 기법
- 식별자를 통해서 엔티티를 조회해서 조회된 엔티티에 값을 채운다. 변경이 있든, 없든 채운다.
- 잘못 알고 사용해서 필드를 하나만 수정하기 위해서 다른 필드를 null로 두고서, 수정하려는 데이터를 제외하고 모두 null값으로 수정될 수 있음

1. merge를 호출하면 1차캐시에 엔티티가 존재하는지 확인하고, 없다면 DB에서 식별자를 통해서 엔티티를 가져온다.
2. 영속 엔티티에 값을 밀어넣는다. 
3. 이후 트랜잭션 커밋시에 변경 감지를 통해서 update 된다.

- 비영속 상태의 엔티티라면? DB에서 엔티티를 찾지못한다면, 엔티티를 새로 생성해서 영속성 컨텍스트에 넣는다
- save or update 기능을 수행



## 예제코드

```java
import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class DirtyCheckingAndMerge {

    public static void main(String[] args) {
        EntityManagerFactory entityManagerFactory = Persistence.createEntityManagerFactory("hello");

        EntityManager entityManager = entityManagerFactory.createEntityManager();

        EntityTransaction transaction = entityManager.getTransaction();
        transaction.begin();

        try {
            Member member = new Member();
            member.setName("ron2");
            member.setCity("seoul");
            member.setStreet("gonghangdaero");
            member.setZipcode("111-111"); 
            
            entityManager.persist(member); // member 생성 

            member.setName("updatedRon2"); // update

            entityManager.flush(); // (1) member insert, (2) update (dirty checking)
            entityManager.clear();


            Member findMember = entityManager.find(Member.class, 1L); // (3) select
            entityManager.clear(); // 준영속 병합을 위해서 clear
            
            System.out.println("findmember 영속? " + entityManager.contains(findMember)); // false -> 준영속
            
            findMember.setName("mergedRon2"); // 이름 변경
          
            Member mergedMember = entityManager.merge(findMember); // 병합(데이터 밀어넣기) (4) 영속성 컨텍스트에 해당 member 있는지 조회 -> 없어서 DB에서 조회 						

            System.out.println("mergedMemer 영속? " + entityManager.contains(mergedMember)); // true


            transaction.commit(); // (5) update 쿼리 (내부적으로 flush()시점)
        } catch (Exception e) {
            transaction.rollback();
        } finally {
            entityManager.close();
        }

        entityManagerFactory.close();
    }
}

```

- 주석의 번호는 쿼리가 나가는 시점 기준

```sql
-- (1) member 생성 
Hibernate: 
    /* insert jpashop.Member
        */ insert 
        into
            MEMBER
            (MEMBER_ID, city, name, street, zipcode) 
        values
            (default, ?, ?, ?, ?)
            
--  (2) update (dirty checking)
Hibernate: 
    /* update
        jpashop.Member */ update
            MEMBER 
        set
            city=?,
            name=?,
            street=?,
            zipcode=? 
        where
            MEMBER_ID=?
            
-- (3) select       
Hibernate: 
    select
        member0_.MEMBER_ID as member_i1_1_0_,
        member0_.city as city2_1_0_,
        member0_.name as name3_1_0_,
        member0_.street as street4_1_0_,
        member0_.zipcode as zipcode5_1_0_ 
    from
        MEMBER member0_ 
    where
        member0_.MEMBER_ID=?
        
-- findmember 영속? false

-- (4) 영속성 컨텍스트에 해당 member 있는지 조회
Hibernate: 
    /* load jpashop.Member */ select
        member0_.MEMBER_ID as member_i1_1_0_,
        member0_.city as city2_1_0_,
        member0_.name as name3_1_0_,
        member0_.street as street4_1_0_,
        member0_.zipcode as zipcode5_1_0_ 
    from
        MEMBER member0_ 
    where
        member0_.MEMBER_ID=?
        
-- mergedMemer 영속? true

-- (5) update 쿼리 
Hibernate: 
    /* update
        jpashop.Member */ update
            MEMBER 
        set
            city=?,
            name=?,
            street=?,
            zipcode=? 
        where
            MEMBER_ID=?
```



### @DynamicUpdate

- merge 또한 변경 감지를 통해서 update, `@DynamicUpdate`어노테이션을 붙이면, merge를 통한 업데이트 또한 동적 쿼리로 생성된다. 

