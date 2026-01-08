
### 이벤트 패턴 수정

- EventBridge Rule에서는 이벤트 패턴에 prefix와 suffix를 동시에 지정하지 못함
- prefix만 지정
```
{
  "source": ["aws.s3"],
  "detail-type": ["Object Created"],
  "detail": {
    "bucket": {
      "name": ["imfact-news"]
    },
    "object": {
      "key": [{
        "prefix": "summarized/"
      }]
    }
  }
}
```

### 이벤트 대상 수정

- EventBridge Rule에서는 Target에 대한 정확한 ARN을 필요로 함
- EventBridge Scheduler는 ECS Task 실행 API를 호출할 때 내부적으로 latest revision을 자동으로 선택하지만 EventBridge Rule에서는 자동으로 선택하지 않음
- EventBridge Rule에서는 TaskDefinitionArn에 revision을 명시해야 함

### S3 버킷의 이벤트 알림 설정 수정

- `이 버킷의 모든 이벤트에 대해 Amazon EventBridge로 알림 전송` 설정을 활성화해야 함
![[Pasted image 20251117035131.png]]

