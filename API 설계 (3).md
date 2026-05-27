# API 엔드포인트 설계 프롬프트

## 프로젝트 정보
- 프로젝트명: Django 동영상 스트리밍 플랫폼
- API 프레임워크: Django REST Framework

## 요청 템플릿

```
Django 온라인 강의 플랫폼의 [리소스/도메인]에 대한 RESTful API를 설계해주세요.

[주요 기능]:
[권한 요구사항]:

답변 형식:
# [리소스명] API 엔드포인트 설계

## 1. API 개요
- Base URL: `https://api.example.com/api/v1`
- 인증 방식: JWT (JSON Web Token)
- 응답 형식: JSON
- API 버전: v1

## 2. 공통 응답 형식
### 성공 응답
```json
{
  "success": true,
  "data": {},
  "message": "Success",
  "timestamp": "2024-01-01T00:00:00Z"
}
```

### 에러 응답
```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error description",
    "details": {}
  },
  "timestamp": "2024-01-01T00:00:00Z"
}
```

### 페이지네이션 응답
```json
{
  "success": true,
  "data": {
    "results": [],
    "count": 100,
    "next": "https://api.example.com/api/v1/resource?page=2",
    "previous": null,
    "page": 1,
    "page_size": 20,
    "total_pages": 5
  }
}
```

## 3. 리소스별 엔드포인트

### [리소스명 1] - Courses (강의)

#### 3.1.1 강의 목록 조회
- **Endpoint:** `GET /api/v1/courses`
- **설명:** 강의 목록을 조회합니다.
- **인증:** 선택 (비로그인 사용자도 조회 가능)
- **권한:** Public

**Query Parameters:**
| 파라미터 | 타입 | 필수 | 기본값 | 설명 |
|---------|------|------|--------|------|
| page | integer | N | 1 | 페이지 번호 |
| page_size | integer | N | 20 | 페이지당 항목 수 (최대 100) |
| category | string | N | - | 카테고리 슬러그 |
| level | string | N | - | 난이도 (beginner/intermediate/advanced) |
| search | string | N | - | 검색 키워드 (제목, 설명) |
| sort | string | N | -created_at | 정렬 (created_at, price, rating, popularity) |
| min_price | decimal | N | - | 최소 가격 |
| max_price | decimal | N | - | 최대 가격 |
| instructor_id | integer | N | - | 강사 ID |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": 1,
        "title": "Django 웹 개발 완전 정복",
        "slug": "django-web-development",
        "description": "Django로 실전 웹 애플리케이션을 만들어봅니다",
        "thumbnail": "https://cdn.example.com/thumbnails/course1.jpg",
        "instructor": {
          "id": 10,
          "username": "john_doe",
          "profile_image": "https://cdn.example.com/profiles/john.jpg"
        },
        "category": {
          "id": 1,
          "name": "웹 개발",
          "slug": "web-development"
        },
        "level": "intermediate",
        "price": 49000,
        "discount_price": 39000,
        "duration_minutes": 1200,
        "total_enrollments": 1520,
        "average_rating": 4.7,
        "total_reviews": 340,
        "created_at": "2024-01-01T00:00:00Z",
        "updated_at": "2024-01-15T12:30:00Z"
      }
    ],
    "count": 150,
    "next": "https://api.example.com/api/v1/courses?page=2",
    "previous": null,
    "page": 1,
    "page_size": 20,
    "total_pages": 8
  }
}
```

---

#### 3.1.2 강의 상세 조회
- **Endpoint:** `GET /api/v1/courses/{id}`
- **설명:** 특정 강의의 상세 정보를 조회합니다.
- **인증:** 선택
- **권한:** Public

**Path Parameters:**
| 파라미터 | 타입 | 설명 |
|---------|------|------|
| id | integer | 강의 ID 또는 슬러그 |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Django 웹 개발 완전 정복",
    "slug": "django-web-development",
    "description": "Django로 실전 웹 애플리케이션을 만들어봅니다",
    "thumbnail": "https://cdn.example.com/thumbnails/course1.jpg",
    "preview_video": "https://cdn.example.com/previews/course1.m3u8",
    "instructor": {
      "id": 10,
      "username": "john_doe",
      "first_name": "John",
      "last_name": "Doe",
      "profile_image": "https://cdn.example.com/profiles/john.jpg",
      "bio": "10년 경력의 웹 개발자"
    },
    "category": {
      "id": 1,
      "name": "웹 개발",
      "slug": "web-development"
    },
    "level": "intermediate",
    "language": "ko",
    "price": 49000,
    "discount_price": 39000,
    "duration_minutes": 1200,
    "sections": [
      {
        "id": 1,
        "title": "Django 시작하기",
        "order": 1,
        "lectures_count": 5,
        "total_duration": 120
      }
    ],
    "total_sections": 10,
    "total_lectures": 85,
    "requirements": ["Python 기초", "HTML/CSS 기초"],
    "learning_objectives": ["Django MTV 패턴 이해", "REST API 개발"],
    "target_audience": ["웹 개발 입문자", "Python 개발자"],
    "status": "published",
    "is_featured": true,
    "total_enrollments": 1520,
    "average_rating": 4.7,
    "total_reviews": 340,
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-15T12:30:00Z",
    "published_at": "2024-01-05T09:00:00Z"
  }
}
```

**Response (404):**
```json
{
  "success": false,
  "error": {
    "code": "COURSE_NOT_FOUND",
    "message": "강의를 찾을 수 없습니다."
  }
}
```

---

#### 3.1.3 강의 생성
- **Endpoint:** `POST /api/v1/courses`
- **설명:** 새로운 강의를 생성합니다.
- **인증:** 필수
- **권한:** Instructor, Admin

**Request Headers:**
```json
{
  "Authorization": "Bearer {access_token}",
  "Content-Type": "application/json"
}
```

**Request Body:**
```json
{
  "title": "Django 웹 개발 완전 정복",
  "description": "Django로 실전 웹 애플리케이션을 만들어봅니다",
  "category_id": 1,
  "level": "intermediate",
  "language": "ko",
  "price": 49000,
  "requirements": ["Python 기초", "HTML/CSS 기초"],
  "learning_objectives": ["Django MTV 패턴 이해", "REST API 개발"],
  "target_audience": ["웹 개발 입문자", "Python 개발자"]
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Django 웹 개발 완전 정복",
    "slug": "django-web-development",
    "status": "draft",
    "created_at": "2024-01-01T00:00:00Z"
  },
  "message": "강의가 생성되었습니다."
}
```

**Response (400):**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "입력값이 올바르지 않습니다.",
    "details": {
      "title": ["이 필드는 필수입니다."],
      "price": ["0 이상의 값이어야 합니다."]
    }
  }
}
```

**Response (403):**
```json
{
  "success": false,
  "error": {
    "code": "PERMISSION_DENIED",
    "message": "강의를 생성할 권한이 없습니다."
  }
}
```

---

#### 3.1.4 강의 수정
- **Endpoint:** `PUT /api/v1/courses/{id}` (전체 수정)
- **Endpoint:** `PATCH /api/v1/courses/{id}` (부분 수정)
- **설명:** 강의 정보를 수정합니다.
- **인증:** 필수
- **권한:** Owner (본인 강의), Admin

**Request Body (PATCH):**
```json
{
  "title": "Django 웹 개발 완전 정복 (2024 최신판)",
  "price": 59000,
  "discount_price": 45000
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "title": "Django 웹 개발 완전 정복 (2024 최신판)",
    "price": 59000,
    "discount_price": 45000,
    "updated_at": "2024-01-20T10:00:00Z"
  },
  "message": "강의 정보가 수정되었습니다."
}
```

---

#### 3.1.5 강의 삭제
- **Endpoint:** `DELETE /api/v1/courses/{id}`
- **설명:** 강의를 삭제합니다.
- **인증:** 필수
- **권한:** Owner, Admin

**Response (204):**
```
No Content
```

**Response (403):**
```json
{
  "success": false,
  "error": {
    "code": "PERMISSION_DENIED",
    "message": "강의를 삭제할 권한이 없습니다."
  }
}
```

---

### [리소스명 2] - Enrollments (수강 등록)

#### 3.2.1 강의 수강 등록
- **Endpoint:** `POST /api/v1/enrollments`
- **설명:** 강의에 수강 등록합니다.
- **인증:** 필수
- **권한:** Student, Instructor

**Request Body:**
```json
{
  "course_id": 1
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": 100,
    "course": {
      "id": 1,
      "title": "Django 웹 개발 완전 정복"
    },
    "enrollment_date": "2024-01-20T10:00:00Z",
    "progress_percentage": 0,
    "is_completed": false
  },
  "message": "강의 수강 등록이 완료되었습니다."
}
```

**Response (400):**
```json
{
  "success": false,
  "error": {
    "code": "ALREADY_ENROLLED",
    "message": "이미 수강 중인 강의입니다."
  }
}
```

---

#### 3.2.2 내 수강 강의 목록
- **Endpoint:** `GET /api/v1/enrollments/me`
- **설명:** 내가 수강 중인 강의 목록을 조회합니다.
- **인증:** 필수
- **권한:** Authenticated

**Query Parameters:**
| 파라미터 | 타입 | 필수 | 기본값 | 설명 |
|---------|------|------|--------|------|
| status | string | N | all | 상태 (all/in_progress/completed) |
| page | integer | N | 1 | 페이지 번호 |

**Response (200):**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": 100,
        "course": {
          "id": 1,
          "title": "Django 웹 개발 완전 정복",
          "thumbnail": "https://cdn.example.com/thumbnails/course1.jpg"
        },
        "enrollment_date": "2024-01-20T10:00:00Z",
        "progress_percentage": 45.5,
        "last_accessed_at": "2024-01-25T15:30:00Z",
        "is_completed": false,
        "certificate_issued": false
      }
    ]
  }
}
```

---

### [리소스명 3] - Lectures (강의 영상)

#### 3.3.1 강의 영상 스트리밍 URL
- **Endpoint:** `GET /api/v1/lectures/{id}/stream`
- **설명:** 강의 영상 스트리밍 URL을 발급합니다.
- **인증:** 필수
- **권한:** Enrolled (수강생), Owner, Admin

**Response (200):**
```json
{
  "success": true,
  "data": {
    "stream_url": "https://cdn.example.com/streams/lecture123/playlist.m3u8",
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expires_at": "2024-01-20T11:00:00Z",
    "quality_options": [
      {"resolution": "1080p", "bitrate": 5000},
      {"resolution": "720p", "bitrate": 2500},
      {"resolution": "480p", "bitrate": 1000}
    ]
  }
}
```

**Response (403):**
```json
{
  "success": false,
  "error": {
    "code": "NOT_ENROLLED",
    "message": "강의를 수강하지 않아 영상을 시청할 수 없습니다."
  }
}
```

---

#### 3.3.2 학습 진행도 업데이트
- **Endpoint:** `POST /api/v1/lectures/{id}/progress`
- **설명:** 강의 영상 학습 진행도를 업데이트합니다.
- **인증:** 필수
- **권한:** Enrolled

**Request Body:**
```json
{
  "last_position": 450,
  "watched_seconds": 450,
  "is_completed": false
}
```

**Response (200):**
```json
{
  "success": true,
  "data": {
    "lecture_id": 1,
    "last_position": 450,
    "watched_seconds": 450,
    "is_completed": false,
    "course_progress_percentage": 15.5
  }
}
```

---

### [리소스명 4] - Reviews (리뷰)

#### 3.4.1 강의 리뷰 작성
- **Endpoint:** `POST /api/v1/courses/{course_id}/reviews`
- **설명:** 강의에 리뷰를 작성합니다.
- **인증:** 필수
- **권한:** Enrolled

**Request Body:**
```json
{
  "rating": 5,
  "title": "정말 좋은 강의입니다!",
  "content": "Django를 처음 배우는데 이해하기 쉽게 설명해주셔서 좋았습니다."
}
```

**Response (201):**
```json
{
  "success": true,
  "data": {
    "id": 50,
    "user": {
      "id": 100,
      "username": "student123"
    },
    "rating": 5,
    "title": "정말 좋은 강의입니다!",
    "content": "Django를 처음 배우는데 이해하기 쉽게 설명해주셔서 좋았습니다.",
    "created_at": "2024-01-25T10:00:00Z"
  },
  "message": "리뷰가 등록되었습니다."
}
```

---

## 4. 에러 코드 정의
| 에러 코드 | HTTP 상태 | 설명 |
|----------|----------|------|
| VALIDATION_ERROR | 400 | 입력값 검증 실패 |
| AUTHENTICATION_REQUIRED | 401 | 인증 필요 |
| INVALID_TOKEN | 401 | 유효하지 않은 토큰 |
| TOKEN_EXPIRED | 401 | 만료된 토큰 |
| PERMISSION_DENIED | 403 | 권한 없음 |
| RESOURCE_NOT_FOUND | 404 | 리소스를 찾을 수 없음 |
| COURSE_NOT_FOUND | 404 | 강의를 찾을 수 없음 |
| ALREADY_ENROLLED | 400 | 이미 수강 중 |
| NOT_ENROLLED | 403 | 수강하지 않음 |
| DUPLICATE_REVIEW | 400 | 이미 리뷰 작성함 |
| RATE_LIMIT_EXCEEDED | 429 | 요청 제한 초과 |
| INTERNAL_SERVER_ERROR | 500 | 서버 내부 오류 |

## 5. 인증 및 권한
### 인증 방식
- JWT (Access Token + Refresh Token)
- Access Token: 유효기간 15분
- Refresh Token: 유효기간 7일

### 권한 레벨
- **Public:** 누구나 접근 가능
- **Authenticated:** 로그인한 사용자
- **Owner:** 리소스 소유자
- **Instructor:** 강사 권한
- **Admin:** 관리자 권한

## 6. Rate Limiting
- 비인증 사용자: 100 요청/시간
- 인증 사용자: 1000 요청/시간
- API 키 사용: 10000 요청/시간

## 7. 버전 관리
- URL 경로에 버전 포함: `/api/v1/`
- 하위 호환성 유지
- Deprecated API는 6개월 전 공지

## 8. 보안 고려사항
- HTTPS 필수
- CORS 설정
- Rate Limiting
- SQL Injection 방지
- XSS 방지
- CSRF 토큰 (세션 인증 시)
```
