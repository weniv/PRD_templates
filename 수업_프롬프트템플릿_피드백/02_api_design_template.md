* 대괄호는 모두 실제 값으로 대체해야 합니다.

# [서비스명] 서비스 API 설계 명세서

## 1. API 개요

**Base URL**: [URL 주소]
**인증 방식**: [인증방식]  
**응답 형식**: [응답형식]

## 2. 공통 규칙
### 2.1 API 주소 설계
### 2.2 요청 형식
#### 2.2.1 [독서 기록] 요청 및 응답 형식
1. 요청 메서드: [POST]
2. 권한: [인증 필요 (Bearer Token)]
3. 엔드포인트: [/reading-records]
4. 요청 본문 예시:
[
```json
{
  "user_id": "uuid-string",
  "book_id": "uuid-string",
  "rating": 5,
  "review": "이 책은 정말 좋았습니다!"
}
```
]

5. 요청 예시:
[
```
fetch('https://api.example.com/reading-records', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer {access_token}'
  },
  body: JSON.stringify({
    user_id: "uuid-string",
    book_id: "uuid-string",
    rating: 5,
    review: "이 책은 정말 좋았습니다!"
  })
});
```
]
6. 응답 형식 예시:
[
```json
{
  "success": true,
  "data": {
    "reading_record_id": "uuid-string",
    "user_id": "uuid-string",
    "book_id": "uuid-string",
    "rating": 5,
    "review": "이 책은 정말 좋았습니다!",
    "created_at": "2025-10-24T10:30:00Z"
  },
  "message": "독서 기록이 성공적으로 생성되었습니다."
}
```
]
7. 에러 응답
[
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "에러 메시지",
    "details": [
      {
        "field": "email",
        "message": "유효하지 않은 이메일 형식입니다."
      }
    ]
  }
}
```
]

## 3. Rate Limiting

모든 API 엔드포인트에는 요청 횟수 제한이 적용됩니다.

### 3.1 기본 제한
- **사용자**: [00]회

### 3.2 특수 제한
- **회원가입**: 시간당 [5]회
- **로그인**: 시간당 [10]회
- **이메일 전송**: 시간당 [3]회
- **이미지 업로드**: 분당 [5]회

### 3.3 제한 초과 시 응답
- [응답값]

## 4. API 버전 관리

- 현재 버전: [`v1`]
- Base URL에 버전 명시: [`/api/v1`]

## 5. 개발 환경

### 5.1 개발 서버
- Base URL: [`http://localhost:8000/api/v1`]
- 자동 생성 API 문서: [`http://localhost:8000/docs` (Swagger UI)]

### 5.2 프로덕션 서버
- Base URL: [`https://api.bookreview.com/api/v1`]
- CORS 설정: [허용된 도메인만 접근]

---

**작성일**: 2025-10-24  
**작성자**: [이름]  
**버전**: 1.0
