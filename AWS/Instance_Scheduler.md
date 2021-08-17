# Instance Scheduler
EC2 및 RDS 인스턴스의 시작 및 중지 스케줄을 손쉽게 구성할 수 있는 솔루션이다. CloudWatch, Lambda, DynamoDB를 배포한다.

## EC2 시작 및 중지 스케줄링
- CLI 사용 : https://aws.amazon.com/ko/premiumsupport/knowledge-center/stop-start-instance-scheduler/  
- 콘솔 사용 : https://gentlesark.tistory.com/89  

## 샘플 스케줄
DynamoDB에 스케줄 데이터가 저장된다. 

## 기간
### 샘플 1: 월요일 09시 시작, 23:59 중지
```
{
    "begintime": {
        "S": "09:00"
    },
    "endtime": {
        "S": "23:59"
    },
    "name": {
        "S": "mon-start-9am"
    },
    "weekdays": {
        "SS": [
            "mon"
        ]
    }
}
```

### 샘플 2: 화요일부터 목요일까지 실행
```
{
    "name": {
        "S": "tue-thu-full-day"
    },
    "weekdays": {
        "SS": [
            "tue-thu"
        ]
    }
}
```

### 샘플 3: 금요일 05시 중지
```
{
    "begintime": {
        "S": "00:00"
    },
    "endtime": {
        "S": "16:59"
    },
    "name": {
        "S": "fri-stop-5pm"
    },
    "weekdays": {
        "SS": [
            "fri"
        ]
    }
}
```

## 스케줄
위의 샘플 1~3 기간을 묶어서 하나의 스케줄로 만들 수도 있다.

## 참고
https://aws.amazon.com/ko/about-aws/whats-new/2018/02/introducing-the-aws-instance-scheduler/  
https://docs.aws.amazon.com/ko_kr/solutions/latest/instance-scheduler/sample-schedule.html