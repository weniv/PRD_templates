# DB 스키마 설계 프롬프트

## 프로젝트 정보
- 프로젝트명: Django 동영상 스트리밍 플랫폼
- 데이터베이스: PostgreSQL

## 요청 템플릿

```
Django 온라인 강의 플랫폼의 [모듈명/도메인]에 대한 데이터베이스 스키마를 설계해주세요.

[주요 엔티티]:
[비즈니스 요구사항]:

답변 형식:
# [모듈명] DB 스키마 설계

## 1. ERD (Entity Relationship Diagram)
```
[사용자] 1:N [강의수강]
[강사] 1:N [강의]
[강의] 1:N [섹션]
[섹션] 1:N [강의영상]
```

## 2. 엔티티 정의

### [엔티티명 1] - users
**설명:** 사용자 계정 정보

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 사용자 ID |
| email | VARCHAR(255) | UNIQUE, NOT NULL | - | 이메일 (로그인용) |
| username | VARCHAR(100) | UNIQUE, NOT NULL | - | 사용자명 |
| password_hash | VARCHAR(255) | NOT NULL | - | 비밀번호 해시 |
| first_name | VARCHAR(50) | NULL | - | 이름 |
| last_name | VARCHAR(50) | NULL | - | 성 |
| profile_image | VARCHAR(500) | NULL | - | 프로필 이미지 URL |
| role | VARCHAR(20) | NOT NULL | 'student' | 역할 (student/instructor/admin) |
| is_active | BOOLEAN | NOT NULL | true | 활성화 여부 |
| is_verified | BOOLEAN | NOT NULL | false | 이메일 인증 여부 |
| created_at | TIMESTAMP | NOT NULL | NOW() | 생성일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |
| last_login | TIMESTAMP | NULL | - | 마지막 로그인 |

**인덱스:**
- PRIMARY KEY (id)
- UNIQUE INDEX idx_email (email)
- UNIQUE INDEX idx_username (username)
- INDEX idx_role (role)
- INDEX idx_created_at (created_at)

**관계:**
- 1:N → enrollments (사용자가 수강한 강의 목록)
- 1:N → courses (강사가 생성한 강의 목록)
- 1:N → reviews (사용자가 작성한 리뷰)

---

### [엔티티명 2] - courses
**설명:** 강의 정보

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 강의 ID |
| instructor_id | BIGINT | FK(users.id), NOT NULL | - | 강사 ID |
| title | VARCHAR(200) | NOT NULL | - | 강의 제목 |
| slug | VARCHAR(250) | UNIQUE, NOT NULL | - | URL 슬러그 |
| description | TEXT | NULL | - | 강의 설명 |
| thumbnail | VARCHAR(500) | NULL | - | 썸네일 이미지 URL |
| preview_video | VARCHAR(500) | NULL | - | 미리보기 영상 URL |
| category_id | BIGINT | FK(categories.id), NULL | - | 카테고리 ID |
| level | VARCHAR(20) | NOT NULL | 'beginner' | 난이도 (beginner/intermediate/advanced) |
| language | VARCHAR(10) | NOT NULL | 'ko' | 언어 코드 |
| price | DECIMAL(10,2) | NOT NULL | 0.00 | 가격 |
| discount_price | DECIMAL(10,2) | NULL | - | 할인 가격 |
| duration_minutes | INT | NULL | - | 총 강의 시간 (분) |
| status | VARCHAR(20) | NOT NULL | 'draft' | 상태 (draft/published/archived) |
| is_featured | BOOLEAN | NOT NULL | false | 추천 강의 여부 |
| total_enrollments | INT | NOT NULL | 0 | 총 수강생 수 |
| average_rating | DECIMAL(3,2) | NULL | - | 평균 평점 |
| total_reviews | INT | NOT NULL | 0 | 총 리뷰 수 |
| created_at | TIMESTAMP | NOT NULL | NOW() | 생성일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |
| published_at | TIMESTAMP | NULL | - | 발행일시 |

**인덱스:**
- PRIMARY KEY (id)
- UNIQUE INDEX idx_slug (slug)
- INDEX idx_instructor_id (instructor_id)
- INDEX idx_category_id (category_id)
- INDEX idx_status (status)
- INDEX idx_created_at (created_at)
- INDEX idx_price (price)
- INDEX idx_average_rating (average_rating)

**관계:**
- N:1 → users (강사)
- N:1 → categories (카테고리)
- 1:N → sections (섹션 목록)
- 1:N → enrollments (수강 등록)
- 1:N → reviews (리뷰 목록)

---

### [엔티티명 3] - sections
**설명:** 강의 섹션 (챕터)

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 섹션 ID |
| course_id | BIGINT | FK(courses.id), NOT NULL | - | 강의 ID |
| title | VARCHAR(200) | NOT NULL | - | 섹션 제목 |
| description | TEXT | NULL | - | 섹션 설명 |
| order | INT | NOT NULL | 0 | 섹션 순서 |
| created_at | TIMESTAMP | NOT NULL | NOW() | 생성일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |

**인덱스:**
- PRIMARY KEY (id)
- INDEX idx_course_id (course_id)
- INDEX idx_order (order)

**관계:**
- N:1 → courses (강의)
- 1:N → lectures (강의 영상 목록)

---

### [엔티티명 4] - lectures
**설명:** 개별 강의 영상

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 강의영상 ID |
| section_id | BIGINT | FK(sections.id), NOT NULL | - | 섹션 ID |
| title | VARCHAR(200) | NOT NULL | - | 영상 제목 |
| description | TEXT | NULL | - | 영상 설명 |
| video_url | VARCHAR(500) | NOT NULL | - | 영상 파일 URL |
| duration_seconds | INT | NOT NULL | 0 | 영상 길이 (초) |
| order | INT | NOT NULL | 0 | 영상 순서 |
| is_preview | BOOLEAN | NOT NULL | false | 미리보기 가능 여부 |
| resources | JSON | NULL | - | 첨부 자료 정보 |
| created_at | TIMESTAMP | NOT NULL | NOW() | 생성일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |

**인덱스:**
- PRIMARY KEY (id)
- INDEX idx_section_id (section_id)
- INDEX idx_order (order)

**관계:**
- N:1 → sections (섹션)
- 1:N → lecture_progress (학습 진행도)

---

### [엔티티명 5] - enrollments
**설명:** 강의 수강 등록

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 수강등록 ID |
| user_id | BIGINT | FK(users.id), NOT NULL | - | 사용자 ID |
| course_id | BIGINT | FK(courses.id), NOT NULL | - | 강의 ID |
| enrollment_date | TIMESTAMP | NOT NULL | NOW() | 등록일시 |
| completion_date | TIMESTAMP | NULL | - | 완료일시 |
| progress_percentage | DECIMAL(5,2) | NOT NULL | 0.00 | 진행률 (%) |
| last_accessed_at | TIMESTAMP | NULL | - | 마지막 접속일시 |
| is_completed | BOOLEAN | NOT NULL | false | 완료 여부 |
| certificate_issued | BOOLEAN | NOT NULL | false | 수료증 발급 여부 |

**인덱스:**
- PRIMARY KEY (id)
- UNIQUE INDEX idx_user_course (user_id, course_id)
- INDEX idx_user_id (user_id)
- INDEX idx_course_id (course_id)
- INDEX idx_enrollment_date (enrollment_date)

**관계:**
- N:1 → users (사용자)
- N:1 → courses (강의)
- 1:N → lecture_progress (강의영상별 진행도)

---

### [엔티티명 6] - lecture_progress
**설명:** 강의 영상별 학습 진행도

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 진행도 ID |
| enrollment_id | BIGINT | FK(enrollments.id), NOT NULL | - | 수강등록 ID |
| lecture_id | BIGINT | FK(lectures.id), NOT NULL | - | 강의영상 ID |
| watched_seconds | INT | NOT NULL | 0 | 시청 시간 (초) |
| is_completed | BOOLEAN | NOT NULL | false | 완료 여부 |
| last_position | INT | NOT NULL | 0 | 마지막 재생 위치 (초) |
| completed_at | TIMESTAMP | NULL | - | 완료일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |

**인덱스:**
- PRIMARY KEY (id)
- UNIQUE INDEX idx_enrollment_lecture (enrollment_id, lecture_id)
- INDEX idx_enrollment_id (enrollment_id)
- INDEX idx_lecture_id (lecture_id)

**관계:**
- N:1 → enrollments (수강등록)
- N:1 → lectures (강의영상)

---

### [엔티티명 7] - reviews
**설명:** 강의 리뷰 및 평점

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 리뷰 ID |
| user_id | BIGINT | FK(users.id), NOT NULL | - | 사용자 ID |
| course_id | BIGINT | FK(courses.id), NOT NULL | - | 강의 ID |
| rating | INT | NOT NULL, CHECK(rating BETWEEN 1 AND 5) | - | 평점 (1-5) |
| title | VARCHAR(200) | NULL | - | 리뷰 제목 |
| content | TEXT | NULL | - | 리뷰 내용 |
| is_public | BOOLEAN | NOT NULL | true | 공개 여부 |
| helpful_count | INT | NOT NULL | 0 | 도움됨 카운트 |
| created_at | TIMESTAMP | NOT NULL | NOW() | 생성일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |

**인덱스:**
- PRIMARY KEY (id)
- UNIQUE INDEX idx_user_course_review (user_id, course_id)
- INDEX idx_user_id (user_id)
- INDEX idx_course_id (course_id)
- INDEX idx_rating (rating)
- INDEX idx_created_at (created_at)

**관계:**
- N:1 → users (사용자)
- N:1 → courses (강의)

---

### [엔티티명 8] - categories
**설명:** 강의 카테고리

**필드:**
| 필드명 | 타입 | 제약조건 | 기본값 | 설명 |
|--------|------|---------|--------|------|
| id | BIGINT | PK, AUTO_INCREMENT | - | 카테고리 ID |
| name | VARCHAR(100) | UNIQUE, NOT NULL | - | 카테고리명 |
| slug | VARCHAR(120) | UNIQUE, NOT NULL | - | URL 슬러그 |
| description | TEXT | NULL | - | 카테고리 설명 |
| parent_id | BIGINT | FK(categories.id), NULL | - | 상위 카테고리 ID |
| icon | VARCHAR(200) | NULL | - | 아이콘 |
| order | INT | NOT NULL | 0 | 정렬 순서 |
| is_active | BOOLEAN | NOT NULL | true | 활성화 여부 |
| created_at | TIMESTAMP | NOT NULL | NOW() | 생성일시 |
| updated_at | TIMESTAMP | NOT NULL | NOW() | 수정일시 |

**인덱스:**
- PRIMARY KEY (id)
- UNIQUE INDEX idx_slug (slug)
- INDEX idx_parent_id (parent_id)
- INDEX idx_order (order)

**관계:**
- 1:N → courses (강의 목록)
- N:1 → categories (상위 카테고리)
- 1:N → categories (하위 카테고리)

## 3. 비즈니스 규칙
### 데이터 무결성
- 사용자는 같은 강의를 중복 수강할 수 없음
- 사용자는 강의당 하나의 리뷰만 작성 가능
- 섹션과 강의영상의 순서는 1부터 시작하며 중복될 수 없음

### 계산 필드
- courses.total_enrollments: enrollments 테이블에서 집계
- courses.average_rating: reviews 테이블에서 평균 계산
- courses.total_reviews: reviews 테이블에서 집계
- enrollments.progress_percentage: lecture_progress에서 계산

### 삭제 정책
- courses 삭제 시: CASCADE (sections, enrollments, reviews 함께 삭제)
- sections 삭제 시: CASCADE (lectures 함께 삭제)
- users 삭제 시: 강사인 경우 강의는 유지, 수강생인 경우 enrollments는 유지

## 4. 성능 최적화
### 인덱스 전략
- 조회가 빈번한 필드에 인덱스 설정
- 복합 인덱스: (user_id, course_id), (course_id, status)
- 전문 검색: courses.title, courses.description

### 파티셔닝
- lecture_progress: enrollment_id 기준 해시 파티셔닝
- reviews: created_at 기준 범위 파티셔닝 (월별)

### 캐싱 전략
- 강의 목록: Redis 캐싱 (TTL: 5분)
- 카테고리: Redis 캐싱 (TTL: 1시간)
- 사용자 진행도: Redis 캐싱 (실시간 업데이트)

## 5. 마이그레이션 고려사항
- 초기 데이터: 카테고리 시드 데이터
- 기본 인덱스 생성
- 트리거: 강의 통계 자동 업데이트
```
