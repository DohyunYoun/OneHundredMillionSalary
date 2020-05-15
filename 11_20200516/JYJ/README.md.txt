# Rxjava
### Rx (ReactiveX)?
- An API for asynchronous programming  
with observable streams
- 관측가능한(Observable) 스트림을 이용한 비동기 프로그래밍 API
- 비동기방식(Observer패턴 + Iterator패턴) + Functional Programming
- 이벤트를 발행하는 Observable (onNext())
- Observable을 구독하는 Observer (subscribe())

### Rxjava 사용처
- API Call
- 다중 비동기 처리
- 이벤트 처리

### Observable Type?
- Observable : 기본타입, 큰 특징이 없다
- Single : 1개의 데이터만 반환
- Maybe : Null 반환 가능성이 있는 1개의 데이터만 반환
- Completable : 데이터를 반환하지 않음
- Flowable : BackPressure 지원

### Thread 관리
- 스케줄러 종류 (RxJava 2.x에서 지원)
-- newThread() : 요청받을 때마다 새로운 스레드 생성하여 사용
-- single() : RxJava내부에서 단일 스레드 생성하여 사용
-- **computation()** : 빠른 계산을 위해 사용
-- **io()** : 네트워크, 입출력 작업을 위해 사용
-- **trampoline()** : 새로운 스레드 생성하지 않고 현재 스레드에 무한한 대기 Queue를 생성하여 사용
- 스케줄러 지정
-- onSubScribe() : 데이터발행을 어디서 할지
-- onObserveOn() : 처리된 데이터를 어디서 사용할지


### Cold Observable
- subscribe(구독) 하였을 때 이벤트 발행
- 단일사용
- ex ) 서버 API 호출, Database 질의

### Hot Observable
- subscribe(구독) 여부와 상관 없이 connect() 후 이벤트 발행
- 지속적으로 데이터 발행
- Subject : Cold Observable -> Hot Observable
- ex) EventBus

### Subject
- AsyncSubject : 발행된 데이터 중 가장 마지막(onComplete 직전) 데이터 받음
- BehaviorSubject : subscribe(구독) 이전의 마지막 데이터부터 이후 발행되는 데이터 받음
- PublishSubject : subscribe(구독) 이후부터 발행되는 데이터 받음
- ReplaySubject : subscribe(구독)하자마자 구독 이전데이터와 이후의 데이터 모두 받음
- ConnectableObservable.publish() : connect 이후 모든 구독자에게 데이터 발행을 시작

### BackPressure (배압)
- Observable의 빠른 데이터 발행속도를 Observer에서 따라가지 못하여, OutOfMemory 가 발생하는것
- 처리방법
-- onBackpressureBuffer() : 배압이슈 발생 시, 별도 버퍼에 데이터 저장 (Flowable의 버퍼 사이즈는 최소 16, 최대 128)
-- onBackpressureBuffer(int size) : 버퍼사이즈 조정 가능
-- onBackpressureDrop() : 배압이슈 발생된 해당 데이터 무시
-- onBackpressureLatest() : 배압이슈 발생된 해당 데이터 무시, 이전의 가장 최근 데이터 사용

### Flowable
- RxJava 1.x에서는 Observable에서 배압처리가 가능하였으나, RxJava 2.x부터 Flowable로 처리 해야한다.
- Cold Observable에서 자주 사용, but 데이터 처리양이 많지 않다면 배압처리 하지 않아도 무관

### Exception
- RxJava는 내부에서 Exception을 관리하므로 try-catch문으로 처리 불가
- 에러발생시(onError) 이후의 스트림은 동작하지 않으므로 처리 필요
- 처리 방법
-- onErrorReturn() : 에러발생시, 지정한 데이터로 대체
-- onErrorResumeNext() : 에러발생시, 추가작업 후 지정한 데이터로 대체
-- retry() : 에러발생시 , 재시도
-- retryUntil() : 에러발생시, 조건에 충족하는 경우에만 재시도 (ex : 네트워크 연결상태)

### RxJavaPlugins (Global Error Handler)
- onError 처리 이전에 스트림이 닫혀, 숨겨진 Exception이 생기는 상황 방지
- [https://developer88.tistory.com/215](https://developer88.tistory.com/215)

### Memory Leak
- Observer가 Activity를 참조하게 되면 화면이 종료되어도 참조 유지
- onComplete(), onError() 후 자동 dispose되지만 수동으로 dispose 시킬필요 있음
- dispose 방법
-- 단일 : Disposable.dispose()
-- 여러개 : CompositeDisposable에 add시킨 후, 한번에 dispose

### RxBinding
- Android UI를 Rx로 Wrapping
- 주의사항
-- View Context와 강한참조(strong reference)로 인한 메모리 누수
-- 한번에 하나의 Observable만 view에 사용가능 (.share()로 해결 가능)
- [DroidKnights 2019 - Track 1] 하동현 - 지금까지 이런 간단한 Logic 처리는 없었다
-- [https://www.youtube.com/watch?v=uyUK65AUQOI](https://www.youtube.com/watch?v=uyUK65AUQOI)

### Site
- ReativeX
-- [http://reactivex.io/](http://reactivex.io/)
- RxJava Github
-- [https://github.com/ReactiveX/RxJava](https://github.com/ReactiveX/RxJava)
- Reactive How
-- [https://reactive.how/](https://reactive.how/)
- 이승민 : 패스트캠퍼스 rxjava 강의 프로젝트 코드
-- [https://github.com/maryangmin/fastcampus-rxjava2](https://github.com/maryangmin/fastcampus-rxjava2)