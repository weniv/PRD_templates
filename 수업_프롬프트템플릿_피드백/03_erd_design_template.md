* 대괄호는 모두 실제 값으로 대체해야 합니다.

# 독서 기록 및 리뷰 공유 서비스 ERD 설계 명세서

## 1. 데이터베이스 개요

[**DBMS**]: [PostgreSQL 14+]   
[**타임존**]: [UTC]  
[**ORM**]: [SQLAlchemy]

## 2. 설계 원칙

### 2.1 네이밍 컨벤션
- [**테이블명**]: [복수형, snake_case (예: `users`, `reading_records`)]
- [**컬럼명**]: [snake_case (예: `created_at`, `user_id`)]
- [**Primary Key**]: [`id` (UUID)]
- [**Foreign Key**]: [`{참조테이블}_id`] (예: `user_id`, `book_id`)
- [**인덱스명**]: [`idx_{테이블명}_{컬럼명}`]
- [**제약조건명**]: [`{타입}_{테이블명}_{컬럼명}`]

## 3. ERD 다이어그램

### 3.1 전체 ERD (Mermaid 형식)

[mermaid]


## 4. 테이블 상세 명세

### 4.1 [users (사용자)]

[**설명**]: [서비스 사용자 정보를 저장하는 테이블]

| 컬럼명 | 데이터 타입 | 제약조건 | 설명 |
|--------|------------|---------|------|
| id | UUID | PK | 사용자 고유 ID |
| email | VARCHAR(255) | NOT NULL, UNIQUE | 이메일 (로그인 ID) |
| password_hash | VARCHAR(255) | NOT NULL | 해시된 비밀번호 (bcrypt) |
| nickname | VARCHAR(20) | NOT NULL, UNIQUE | 닉네임 (2~20자) |
| profile_image | VARCHAR(500) | NULL | 프로필 이미지 URL |
| bio | TEXT | NULL | 자기소개 (최대 500자) |
| favorite_genres | JSON | NULL | 선호 장르 배열 |
| is_verified | BOOLEAN | DEFAULT FALSE | 이메일 인증 여부 |
| is_active | BOOLEAN | DEFAULT TRUE | 계정 활성화 여부 |
| deleted_at | TIMESTAMP | NULL | 삭제 시각 (Soft Delete) |
| created_at | TIMESTAMP | NOT NULL | 생성 시각 |
| updated_at | TIMESTAMP | NOT NULL | 수정 시각 |

**인덱스**:
- `idx_users_email` on `email`
- `idx_users_nickname` on `nickname`
- `idx_users_created_at` on `created_at`

**제약조건**:
- `CHECK (char_length(nickname) >= 2 AND char_length(nickname) <= 20)`
- `CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$')`

**비즈니스 로직**:
- 회원가입 시 `is_verified = false`
- 이메일 인증 완료 시 `is_verified = true`
- 회원 탈퇴 시 `deleted_at` 설정 (Soft Delete)
- 30일 후 실제 삭제 (배치 작업)

IMESTAMP | NULL | 완독 시각 |
| rating | INTEGER | NULL | 별점 (1~5) |
| created_at | TIMESTAMP | NOT NULL | 생성 시각 |
| updated_at | TIMESTAMP | NOT NULL | 수정 시각 |

**인덱스**:
- `idx_reading_records_user_id` on `user_id`
- `idx_reading_records_book_id` on `book_id`
- `idx_reading_records_status` on `status`
- `idx_reading_records_completed_at` on `completed_at`

**제약조건**:
- `UNIQUE (user_id, book_id)` - 한 사용자는 같은 책을 하나만 등록
- `CHECK (status IN ('want_to_read', 'reading', 'completed'))`
- `CHECK (rating IS NUL---

## 5. 관계 요약

### 5.1 일대다 (One-to-Many) 관계
- [`users` → `reading_records`: 한 사용자는 여러 독서 기록을 가짐]
- [`users` → `reviews`: 한 사용자는 여러 리뷰를 작성]

### 5.2 다대다 (Many-to-Many) 관계
- [`reviews` ↔ `hashtags`]: [리뷰와 해시태그 (중간 테이블: `review_hashtags`)]
- [`users` ↔ `users`]: [팔로우 관계 (중간 테이블: `follows`)]

### 5.3 일대일 (One-to-One) 관계
- [없음 (모든 관계는 일대다 또는 다대다)]

---

**작성일**: 2025-10-24  
**작성자**: [이름]  
**버전**: 1.0  
**ERD 도구**: Mermaid, dbdiagram.io, draw.io 등 사용 가능
