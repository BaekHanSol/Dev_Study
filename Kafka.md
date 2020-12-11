현재 설정
- 구조
mongodb - mongodb connector(Producer 역할) -> kafka -> logstash, app consumer (Consumer 역할)

-Kafka 버전
Scala 2.12, 2.5 ver
```
- mongo connector 설정
name=mongo-source
connector.class=com.mongodb.kafka.connect.MongoSourceConnector
tasks.max=1
#tasks.max는 partition보다 높게 설정해야 함

 # Connection and source configuration
connection.uri=mongodb://{MONGO_ID}:{MONGO_PASSWORD}@{IP_ADDRESS}:{PORT},{IP_ADDRESS}:{PORT}/thingarx_receiver?replicaSet=rs0
database=thingarx_receiver
collection=

topic.prefix=
poll.max.batch.size=1000
poll.await.time.ms=5000

# Change stream options
pipeline=[]
batch.size=0
change.stream.full.document=updateLookup
collation=
```
문제점 : logstash로 가는 데이터와 app consumer쪽에서 수신받는 데이터에 차이가 있음. app consumer쪽에서 선택해서 읽어 데이터 손실이 있음
   원인분석
   Q1. producer쪽에서 일부만 읽어 가는 것이 아닐까???
   A1. NO. logstash는 제대로 읽어가고 있었음
   Q2. Consumer가 2개 이니까 logstash와의 경쟁에서 밀려서 제대로 못 읽고 있는 것이 아닐까?
   A2. NO. 읽고 있는 데이터 양을 확인해 보니 logstash와 app consumer는 비슷한 양으로 읽고 있었음
   Q3. producer 쪽에서 보내는 데이터 양이 많아서 한번에 처리를 못하는 것이 아닐까?
   A3. NO. 만약 그러하다면 lag가 쌓이고 최신 데이터를 읽지 못하고 과거 데이터를 가지고 와야하는데 그것도 아님 lag는 0이고 offset도 최신이었음
   ???????????????????????

해결방안
Producer 설정
1.Acks 이용
- acks는 producer가 메시지를 보내고 그 메시지를 kafka가 잘 받았는 지 확인을 하는 옵션이다.
- acks = 0 : producer는 자신이 보낸 메시지에 대해 카프카로부터 확인을 기다리지 않음 (메시지 손실 O, 속도 빠름). producer는 계속 보내기만 함
- acks = 1 : 메시지를 보내고 난 뒤 producer는 자신이 보낸 메시지를 받았는 지 확인함. 하지만 leader가 복제 했을 때만 완료메시지를 받기 때문에 하위 follower가 복제를 못했을 때 메시지 유실이 있다.
            ( 메시지 손실 O, 속도 보통 )
- acks = all(-1) : 메시지를 보내고 난 뒤 producer는 모든 follwer이 복제가 완료되었을 때 완료 메시지를 받음 (메시지 손실X, 속도 느림)
- 이용 : acks = option number
=> logstash와 mongodb를 확인해 보았을 때 producer쪽에서는 문제가 없음을 확인함.

Consumer 설정
1. Acks 이용 / 수동 커밋 이용
- 읽었다고 바로 commit하는 것이 아니라(자동커밋) 실제 수행하는 함수를 실행하면 커밋 전송
- commit이 수행되어야 offset이 변경됨
Q1. 이렇게 설정한다고 해서 유실이 없을까???
A1. 사례) https://wfreud.tistory.com/351?category=725448 
    사례) https://hahahia.tistory.com/158
해당 블로그의 글을 보면 다음 일부 메시지가 유실되어도 다음 메시지가 성공했다는 응답을 전송하면 해결하지 않고 그냥 넘어가는 문제점이 있다고 나와 있다.
=> retry 메소드를 추가하여 유실이 없도록 변경할 필요가 있음
2. 1번 + retry 메소드

