다음과 같이 Member와 Team 객체가 있을때

```java
// Member
...
@ManyToOne(fetch = FetchType.LAZY)
@JoinColumn(name = "team_id")
private Team team;
// Team
...
@OneToMany(mappedBy = "team")
private List<Member> members = new ArrayList<>();
```

지연로딩 관계이기 때문에 team의 데이터를 조회할 때 마다 쿼리가 실행된다(N+1)

```java
@Test
void findMemberLazy() {
    // given
    Team teamA = new Team("teamA");
    Team teamB = new Team("teamB");
    teamRepository.save(teamA);
    teamRepository.save(teamB);

    Member member1 = new Member("member1", 10, teamA);
    Member member2 = new Member("member2", 10, teamB);
    memberRepository.save(member1);
    memberRepository.save(member2);

    em.flush();
    em.clear();

    // when N+1
    List<Member> members = memberRepository.findAll();

    for (Member member : members) {
        System.out.println("member = " + member.getUsername());
        // member.teamClass = class study.datajpa.entity.Team$HibernateProxy$vA60yI9s
        System.out.println("member.teamClass = " + member.getTeam().getClass()); // proxy
        // tema의 name을 조회하기 위해서 쿼리가 한번더 나간다
        System.out.println("member.team = " + member.getTeam().getName());
    }
}
```

연관된 엔티티를 한번에 조회하려면 패치 조인을 사용하거나 엔티티 그래프를 사용할 수 있다(JPQL + 엔티티그래프도 가능)

```java
// fetch join
@Query("select  m from Member m left join fetch m.team")
List<Member> findMemberFetchJoin();
// EntityGraph
@EntityGraph(attributePaths = {"team"})
List<Member> findAll();
// 쿼리가 복잡한 경우 JPQL + 엔티티 그래프도 가능
@EntityGraph(attributePaths = {"team"})
@Query("select m from Member m")
List<Member> findMemberEntityGraph();
```

기존에는 member.getTeam.getClass()가 가짜 객체 프록시 객체로 나오는 반면

패치 조인 후에는 class study.datajpa.entity.Team 와 같이 진짜 객체가 나온다

