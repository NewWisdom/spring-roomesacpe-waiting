# spring-roomesacpe-waiting

## 1단계 - 인증 로직 리팩처링

### 프로그래밍 요구사항

- [x] auth 패키지를 nextstep 패키지로부터 분리한다
    - [x] auth 패키지에서 nextstep로 의존하는 부분을 제거한다.
    - [x] auth 패키지 내에서 스프링 빈으로 사용되던 객체를 Component Scan이 아닌 Java Configuration으로 빈 등록한다.

### 요구사항 설명

#### 패키지 분리

- auth 패키지를 nextstep 패키지로부터 분리한다.

#### Java Configuration 활용

- 기존에 Component Scan으로 등록하던 객체를 Java Configuration을 이용하여 등록한다.

## 2단계

### 상황 설명

- 예약 취소가 빈번해져서 다음 예약자를 구하기 번거로워졌다.
- 예약이 이뤄진 이후 대기를 신청할 수 있는 기능이 필요하다.

### 기능 요구사항

- [ ] 이미 예약이 된 스케줄 대상으로 예약 대기를 신청할 수 있다.
    - [ ] 예약이 없는 스케줄에 대해서 예약 대기 신청을 할 경우 예약이 된다.
- [ ] 자신의 예약 대기를 취소할 수 있다.
    - [ ] 자신의 예약 대기가 아닌경우 취소할 수 없다.
- [ ] 나의 예약 목록을 조회할 수 있다.
    - [ ] 예약과 예약 대기를 나눠서 조회한다.
    - [ ] 예약은 reservation을 조회하고 예약 대기는 reservation-waiting을 조회한다.
    - [ ] 예약 대기의 경우 대기 순번도 함께 조회할 수 있다.
    - [ ] 취소된 예약도 조회할 수 있다.

### 프로그래밍 요구사항

- [ ] 트랜잭션을 설정하고 적절한 옵션을 이용한다.

### API 설계

#### 예약 대기

```http request
POST /reservation-waitings HTTP/1.1
authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiaWF0IjoxNjYzMjk4NTkwLCJleHAiOjE2NjMzMDIxOTAsInJvbGUiOiJBRE1JTiJ9.-OO1QxEpcKhmC34HpmuBhlnwhKdZ39U8q91QkTdH9i0
content-type: application/json; charset=UTF-8
host: localhost:8080

{
    "scheduleId": 1
}
```

```http request
HTTP/1.1 201 Created
Location: /reservation-waitings/1
```

#### 예약 목록 조회

```http request
GET /reservations/mine HTTP/1.1
authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiaWF0IjoxNjYzMjk4NTkwLCJleHAiOjE2NjMzMDIxOTAsInJvbGUiOiJBRE1JTiJ9.-OO1QxEpcKhmC34HpmuBhlnwhKdZ39U8q91QkTdH9i0
```

```http request
HTTP/1.1 200
Content-Type: application/json

[
    {
        "id": 1,
        "schedule": {
            "id": 1,
            "theme": {
                "id": 1,
                "name": "테마이름",
                "desc": "테마설명",
                "price": 22000
            },
            "date": "2022-08-11",
            "time": "13:00:00"
        }
    }
]
```

#### 예약 대기 목록 조회

```http request
GET /reservation-waitings/mine HTTP/1.1
authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiaWF0IjoxNjYzMjk4NTkwLCJleHAiOjE2NjMzMDIxOTAsInJvbGUiOiJBRE1JTiJ9.-OO1QxEpcKhmC34HpmuBhlnwhKdZ39U8q91QkTdH9i0
```

```http request
HTTP/1.1 200
Content-Type: application/json

[
    {
        "id": 1,
        "schedule": {
            "id": 3,
            "theme": {
                "id": 2,
                "name": "테마이름2",
                "desc": "테마설명2",
                "price": 20000
            },
            "date": "2022-08-20",
            "time": "13:00:00"
        },
        "waitNum": 12
    }
]
```

#### 예약 대기 취소

```http request
DELETE /reservation-waitings/1 HTTP/1.1
authorization: Bearer eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxIiwiaWF0IjoxNjYzMjk5MDcwLCJleHAiOjE2NjMzMDI2NzAsInJvbGUiOiJBRE1JTiJ9.zgz7h7lrKLNw4wP9I0W8apQnMUn3WHnmqQ1N2jNqwlQ
```

```http request
HTTP/1.1 204
```