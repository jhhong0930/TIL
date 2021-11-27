이번 실전 프로젝트에서는 다음과 같은 기능을 추가하였다

- 이번주 좋아요를 제일 많이 받은 3명의 프로필 정보와 개수 조회
- 이번주 게시글을 제일 많이 작성한 3명의 프로필 정보와 개수 조회
- 이번주 팔로우를 제일 많이 받은 3명의 프로필 정보와 개수 조회
- 이번주 댓글을 제일 많이 작성한 3명의 프로필 정보와 개수 조회

```java
// service
public class RankingService {

    private final UserRepository userRepository;

    // 현재시간
    LocalDateTime time = LocalDateTime.now();
    // 이번주 월요일 0시
    LocalDateTime monday = time.with(TemporalAdjusters.previousOrSame(DayOfWeek.MONDAY))
            .withHour(0)
            .withMinute(0)
            .withSecond(0)
            .withNano(0);

    // 좋아요를 제일 많이 받은 유저 TOP3
    public List<GetThisWeekRankingResponseDto> getTop3ByMostLiked() {

        List<Object[]> top3ByMostLiked = userRepository.findTop3ByMostLiked(monday, time);

        return top3ByMostLiked.stream().map(like -> new GetThisWeekRankingResponseDto(
                (String) like[0],
                (String) like[1],
                ((BigInteger) like[2]).longValue()
        )).collect(Collectors.toList());
    }
    
    ...
}

// repository
@Query(value = "select nickname, image, count(cafe_like_id) cnt " +
               "from user u " +
               "join cafe c on u.user_id = c.user_id " +
               "join cafe_like cl on c.cafe_id = cl.cafe_id " +
               "where cl.reg_date between :startDate and :endDate " +
               "group by nickname " +
               "order by cnt desc limit 3",
               nativeQuery = true)
List<Object[]> findTop3ByMostLiked(@Param("startDate") LocalDateTime startDate,
                                   @Param("endDate") LocalDateTime endDate);
```

모두 공통적으로 이번주 월요일 0시부터 지금까지의 데이터들을 조회해오는 방식이다

그런데 새로운 데이터들이 들어왔음에도 불구하고 조회시 반영이 안되는 문제가 발생하였다

문제는 바로 LocalDateTime의 선언 위치였다

위 코드를 보면 전역변수로 설정이 되어있는데, RankingService가 Bean에 등록이 되면서 LocalDateTime 객체가 서버를 구동하는 즉시 만들어진 것이다. 따라서 언제 요청을 하던 time은 계속 같은 시간으로 고정이 되어있었던 것이다

그래서 아래와 같이 함수로 따로 빼니깐 잘 작동하였다

```java
// 좋아요를 제일 많이 받은 유저 TOP3
public List<GetThisWeekRankingResponseDto> getTop3ByMostLiked() {

    List<Object[]> top3ByMostLiked = userRepository.findTop3ByMostLiked(getTime().get(0), getTime().get(1));

    return top3ByMostLiked.stream().map(like -> new GetThisWeekRankingResponseDto(
        (String) like[0],
        (String) like[1],
        ((BigInteger) like[2]).longValue()
    )).collect(Collectors.toList());
}

// 이번주 월요일 부터 현재까지의 시간
public List<LocalDateTime> getTime() {

    List<LocalDateTime> list = new ArrayList<>();

    LocalDateTime now = LocalDateTime.now();
    LocalDateTime monday = now.with(TemporalAdjusters.previousOrSame(DayOfWeek.MONDAY))
        .withHour(0)
        .withMinute(0)
        .withSecond(0)
        .withNano(0);

    list.add(monday);
    list.add(now);

    return list;
}
```

