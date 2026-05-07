# `멱등성` 

<h5>멱등하다는 것은 동잏한 작업을 여러 번 수행해도 결과를 변경시키지 않는 속성입니다.</h5> 

<h4>왜 멱등성이라는 개념이 중요할까?</h4>

사용자가 브라우저를 통해 `결제요청`을 할 떄, `의도치 않게 기술적 결함 및 상황`으로 동일한 요청이 2번 이상 수행될 수도 있습니다. <br>


```html
1. 네트워크 타임아웃과 재시도 (Retry Logic) (`가장 흔한 문제 원인입니다.`)

사용자가 결제요청을 하였지만, 브라우저는 네트워크 상태가 안좋아 네트워크가 끊기거나 서버로부터 요청을 받았다는 응답이 지연될 수 있습니다. 클라이언트에 지정된 `타임아웃` 시간 내에 응답을 받지 못한다면 사용자 브라우저는 동일한 결제 요청을 반복적으로 수행하게 됩니다. 

--------------------------------------------------------------------------------------------
2. 사용자의 중복 클릭

결제 버튼을 눌렀는데 화면이 즉시 넘어가지 않거나 반응이 느리면, 사용자는 불안한 마음에 버튼을 여러 번 연타하게 됩니다.

버튼 비활성화(Disable) 처리가 되어 있지 않다면, 클릭한 횟수만큼 API 요청이 서버로 날아갑니다.
--------------------------------------------------------------------------------------------

3. 분산 시스템의 "At Least Once" 전략에서의 구조적 문제 

메시지 큐(Kafka, RabbitMQ 등)를 사용하는 분산 환경에서는 메시지 유실을 방지하기 위해 "적어도 한 번은 전달한다(At Least Once)"는 전략을 취합니다.

상황: 컨슈머(수신자)가 메시지를 받아 처리를 완료했지만, 완료했다는 `신호(ACK)를 보내기 직전에 장애`가 발생하면, 큐는 "처리가 안 됐구나"라고 판단하고 똑같은 메시지를 다른 컨슈머나 동일한 컨슈머에게 다시 보냅니다.
[장애 발생 시,확인 응답을 받지 못한다면 MQ가 동일한 요청을 다른 컨슈머가 처리하도록 지시합니다.]
--------------------------------------------------------------------------------------------

4. 브라우저의 새로고침 (F5)

상황: 특정 POST 요청 이후 결과 페이지로 리다이렉트(Redirect)되지 않은 상태에서 새로고침을 하면, 브라우저는 "이전 요청을 다시 보내시겠습니까?"라는 팝업과 함께 동일한 데이터를 다시 전송할 수 있습니다.

```




- HTTP 메서드의 멱등성


🟢 멱등성이 보장되는 메서드 (호출할 때마다 서버의 최종 상태가 같음)<br>

GET (조회): <br>서버의 데이터를 읽기만 할 뿐 변경하지 않습니다.<br>GET /api/pins/1 요청은 네트워크 지연 때문에 100번이 `재요청되더라도 DB의 데이터는 안전합니다.`<br>

PUT (대체/전체 수정):<br> 해당 리소스를 통째로 `덮어씌우는 동작`입니다.<br> 내 프로필 이름을 "김싸피"로 수정하는 PUT /api/users/1 요청을 생각해 보세요. <br>이 요청이 두 번 날아가더라도 `최종적으로 DB에 기록된 이름`은 여전히 `"김싸피"` 하나뿐입니다.<br>

DELETE (삭제):<br> 특정 리소스를 삭제합니다. DELETE /api/pins/1을 처음 호출하면 데이터가 지워지고 200 OK가 떨어지겠죠.<br> 만약 재요청이 들어가면 이미 지워졌기 때문에 404 Not Found 에러가 날 수 있습니다.<br>
`"응답 코드는 달라질 수 있지만,` `서버의 최종 상태`(해당 핀이 존재하지 않음)는 `동일`합니다.<br> 그래서 DELETE도 멱등성이 보장된다고 봅니다.<br>
<br>
🔴 멱등성이 보장되지 않는 메서드 (호출할 때마다 서버 상태가 달라짐)<br>

POST (생성):<br>
앞서 보셨던 중복 결제 대참사의 주범입니다. <br>POST /api/payments 처럼 새로운 리소스를 생성하는 요청은, `네트워크 지연으로 인`해 `재요청`이 발생할 때마다<br> DB에 `새로운 로우(Row)가 계속해서 INSERT` 됩니다.<br>



PATCH (부분 수정):<br>
이 녀석은 조금 특이합니다.<br> PUT과 달리 데이터의 '일부'만 수정하는데, 어떻게 구현하느냐에 따라 결과가 달라져 스펙상 멱등하지 않다(X)고 규정합니다.<br>

멱등한 경우:<br> { "status": "COMPLETED" } (상태를 완료로 변경해 줘 -> 여러 번 보내도 완료 상태임)<br>

멱등하지 않은 경우:<br> { "$inc": { "visit_count": 1 } } (조회수를 1 올려줘 -> 여러 번 보내면 계속 조회수가 올라감)<br>

## 💡 `먼저 바로잡아야 할 포인트`

HTTP 비연결성(Connectionless)의 기준: 과거에는 한 번 응답을 받으면 TCP 연결을 칼같이 끊었지만,<br> 요즘(HTTP/1.1 이상)은 성능을 위해 Keep-Alive라는 기능으로 일정 시간 TCP 연결을 열어둡니다.<br> 하지만, "하나의 요청에 하나의 응답이 오면 해당 트랜잭션(작업)은 끝난다"는 논리적인 비연결성 구조는 여전히 유효합니다.<br>

`즉, 응답을 못 받았다고 해서 기존 연결망 위에서 서버가 알아서 다시 알려주지 않기 때문에 클라이언트가 '새로운 HTTP 요청'을 다시 날려야 합니다.`<br>

## 의도적 지연 (서버에서 응답 늦게 주도록)
```JAVA
@PostMapping("/api/payment")
public ResponseEntity<?> processPayment(@RequestBody PaymentDto request) throws InterruptedException {
    
    // 1. 실제 결제 처리 로직 (DB에 결제 내역 저장 등)
    paymentService.doPayment(request); 

    // 2. 네트워크 지연 상황 시뮬레이션! (응답을 5초간 멈춤)
    Thread.sleep(5000); 

    // 3. 5초 뒤에야 클라이언트에게 성공 응답을 보냄
    return ResponseEntity.ok("결제 성공");
}
```

# 멱등성을 해결하는 대표적인 방법 
## (다음 아래 사항 중 적어도 1,2번은 모두 적용합니다.)

```HTML
1차 방어선
`Idempotency Key` (멱등 키): 
클라이언트가 요청마다 `고유한 ID(UUID 등)`를 생성해 헤더에 실어 보냅니다.
서버는 이 키를 확인해 `이미 처리된 키라면 로직을 수행하지 않고` 기존 결과만 반환합니다.

2차 방어선
데이터베이스 제약 조건: `테이블에 Unique Key`를 설정하여 `동일한 주문 번호가 두 번 저장되지 않도록` 물리적으로 막습니다.

사용자의 실수 방지
프런트엔드 제어: 클릭 즉시 버튼을 비활성화하거나 로딩 스피너를 보여주어 추가 클릭을 방지합니다.
```

### 결론, 결제 작업을 여러 번 요청해도 동일한 작업임을 식별하기 위해 클라이언트에서 유일한 식별자를 부여하고 DB에서 `주문 Row에` 해당 키(속성)을 `유일키로 지정해` 중복된 결제가 저장되지 않도록 처리합니다. 

추가로 생각해 볼 점?:
물리적으로 동일한 결제 요청이 2번 적용되지는 않습니다. 근데, 피드백을 받을 수있나요? 동일한 연산에 대해  DB 연산이 수행되지 않을 뿐 피드백을 받을 수는 없습니다.  

- Redis를 통해 피드백 결과를 임시 저장

1. 전체적인 설계 흐름 (Workflow)

1. `클라이언트:` 요청을 보낼 때 헤더에 고유한 키(예: `Idempotency-Key`: uuid-1234)를 포함합니다.<br>

2. `서버(인터셉터/필터):` 요청을 받자마자 `해당 키`가 `저장소 Redis`에 있는지 확인합니다.

3. `결과에 따른 처리:`

키가 없는 경우:<br> 첫 요청입니다.<br> 요청을 처리하고, 그 결과(Status Code, Body 등)를 `키와 함께 저장`한 뒤 응답합니다.<br>

키가 있는 경우:<br> 중복 요청입니다. <br>실제 로직(결제 등)을 수행하지 않고, 저장되어 있던 `기존 응답 결과를 꺼내서 즉시 반환`합니다.<br>


# 동시성 제어하기 ( atomic Update)


## `원자적 업데이트(Atomic Update)`는 데이터베이스나 `동시성 프로그래밍`에서 `매우 중요`하게 다루어지는 핵심 개념입니다.

동시에 서버여 여러 요청을 수행하면 재고가 않았음에도 구매처리 되는 상황이 발생할 수 있습니다.


```sql
A: select 10  # select stock from items  
B: select 10 # select stock from items
A: update 9
B: update 9
원하는 결과는 8이었지만 왜 9가 됐을까요???

해답 : atomic 하게 동작하도록 재고 업데이트를 수행합니다.
update items 
     set stock = stock- #{cnt} 
     where stock >= #{cnt} and id = 1
```

왜 이런 상황이 일어나냐? <br>
AA -> BB 순서대로 쿼리가 수행되면 상관없습니다.<br>
근데 동시에 수많은 스레드가 병렬적으로 처리되면 개수를 조회하는 작업과 갱신하는 작업이 분리되어 하나의 트랙젝션으로 동작합니다.<br>하지만 `이 둘은 분리되지 않고` `하나의 트랜잭션으로` 동작해야 의도된 동작을 수행합니다. <br> `서버에서 쿼리를 나눠서 2번` 쏘면 `RaceCondition에 의한 db오염`을 막을 수 없습니다,<br>


### 쓰레드 많이 생성하여 동시에 SELECT(조회) + UPDATE(서버에서 연산 후 세팅) 구조로 재고 감소 연산
```JAVA
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
public class ConcurrencyTest {
    @Autowired
    private ItemService itemService; // 테스트할 비즈니스 로직 (재고 감소 등)
    @Autowired
    private ItemRepository itemRepository; // 결과 검증을 위한 DB 접근
    @Test
    @DisplayName("1000개의 스레드가 동시에 재고 감소를 요청한다")
    public void test1000ConcurrentRequests() throws InterruptedException {
        // given
        int threadCount = 1000;
        Long itemId = 1L;
        
        // 1. 스레드 풀 생성 (동시에 활성화될 스레드 수를 제한, 보통 32~100 사이 사용)
        ExecutorService executorService = Executors.newFixedThreadPool(32);
        
        // 2. 1000개의 작업이 모두 끝날 때까지 메인 스레드를 대기시키기 위한 안전장치
        CountDownLatch latch = new CountDownLatch(threadCount);

        // when
        for (int i = 0; i < threadCount; i++) {
            executorService.submit(() -> {
                try {
                    // 비즈니스 로직 호출 (예: 재고 1개 감소)
                    itemService.decreaseStock(itemId, 1);
                } finally {
                    // 성공하든 예외가 터지든 래치 카운트는 무조건 감소시켜야 무한 대기에 빠지지 않음
                    latch.countDown(); 
                }
            });
        }
        // 모든 스레드의 작업이 끝나서 카운트가 0이 될 때까지 대기
        latch.await();
        // then
        // 모든 스레드 종료 후 최종 상태 검증
        Item item = itemRepository.findById(itemId).orElseThrow();
        
        // 초기 재고가 1000개였다고 가정할 때, 정상적으로 동시성 제어가 되었다면 0이어야 함
        System.out.println("최종 남은 재고: " + item.getStock());
        assertThat(item.getStock()).isEqualTo(0);
    }
}

```

## 인덱스로 성능 개선 Full Table Scan방지

1. 인텍스로 지정 하는 것은 자주 JOIN WHERE의 조건으로 사용되는 속성이어야 효과가 좋습니다.. 
2. 인덱스로 지정한 속성은 B-tree 구조에서 서치기준이 되어 o(log n)시간 내에 빠르게 조회하여 원하는 Row를 찾아냅니다.
3. 너무 많은 인덱스를 지정하는 것은 좋지 않습니다. 
4. 인덱스 속성의 cardinality(값의 범위)가 넓을 수록 효율이 좋습니다. ( o(log n)시간 내에 빠르게 조회 )


## Massage Queue를 이용한 DB성능 안정화

KAFKA나 rabbit MQ를 사용합니다. 
rabbitMQ: <br>한번에 너무 많은 요청이 오는 경우 버퍼역할을 하는 메시지큐에 요청을 담아두고 
컨슈머 스레드들 MQ로부터 요청들을 가져가서 DB에 요청을하는 트래픽 분산 구조를 가집니다.<br>
KAFKA:<br> 메시지 로그 기반이며, 대용량 요청 및 사용자 로그 분석에 유용하고 실시간 속도가 더 우선시 된다면 rabbit MQ를 주로 사용합니다.<br>
# 그외 Reddis 캐싱을 통한 db 최적화 
update 시에는 reddis에 저장된 row 정보를 지우고 select시 에는 레디스에서 정보를 가져오고<br> 
캐시미스 시에 db를 방문하는 (캐시히트)구조로 설계하여 db의 성능을 향상킬 수있습니다.<br>

disk로 접근하는 것보다 ram을 접근하는 것이 더 빠르므로 redis를 이용한 접근도 좋습니다.<br>

join 결과는 dto 등으로 잘 정의해서 레디스에 저장합니다.( 귀찬....)