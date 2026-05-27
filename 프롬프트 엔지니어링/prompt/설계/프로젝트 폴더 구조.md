# 프로젝트 폴더 구조 설계 프롬프트

## 프로젝트 정보
- 프로젝트명: Django 동영상 스트리밍 플랫폼
- 프레임워크: Django + Django REST Framework

## 요청 템플릿

```
Django 온라인 강의 플랫폼의 프로젝트 폴더 구조를 설계해주세요.

[주요 모듈]:
[아키텍처 스타일]: (Monolithic / Microservices)

답변 형식:
# Django 프로젝트 폴더 구조

## 1. 전체 프로젝트 구조

```
learning-platform/
│
├── .github/                      # GitHub 설정
│   ├── workflows/               # CI/CD 워크플로우
│   │   ├── ci.yml
│   │   ├── deploy-staging.yml
│   │   └── deploy-production.yml
│   ├── ISSUE_TEMPLATE/
│   └── PULL_REQUEST_TEMPLATE.md
│
├── backend/                      # Django 백엔드
│   ├── config/                  # 프로젝트 설정
│   │   ├── __init__.py
│   │   ├── asgi.py
│   │   ├── wsgi.py
│   │   ├── urls.py              # 메인 URL 라우팅
│   │   ├── settings/            # 환경별 설정
│   │   │   ├── __init__.py
│   │   │   ├── base.py          # 공통 설정
│   │   │   ├── development.py   # 개발 환경
│   │   │   ├── staging.py       # 스테이징 환경
│   │   │   └── production.py    # 프로덕션 환경
│   │   └── celery.py            # Celery 설정
│   │
│   ├── apps/                    # Django 앱 모음
│   │   ├── __init__.py
│   │   │
│   │   ├── users/               # 사용자 관리
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # User, Profile 모델
│   │   │   ├── serializers.py   # DRF Serializers
│   │   │   ├── views.py         # API Views
│   │   │   ├── urls.py          # URL 라우팅
│   │   │   ├── admin.py         # Django Admin
│   │   │   ├── managers.py      # Custom Managers
│   │   │   ├── permissions.py   # 권한 클래스
│   │   │   ├── signals.py       # Django Signals
│   │   │   ├── tasks.py         # Celery Tasks
│   │   │   ├── services.py      # 비즈니스 로직
│   │   │   ├── validators.py    # 커스텀 Validators
│   │   │   ├── tests/           # 테스트
│   │   │   │   ├── __init__.py
│   │   │   │   ├── test_models.py
│   │   │   │   ├── test_views.py
│   │   │   │   ├── test_serializers.py
│   │   │   │   └── factories.py # Factory Boy
│   │   │   └── migrations/      # DB 마이그레이션
│   │   │
│   │   ├── courses/             # 강의 관리
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Course, Section, Lecture 모델
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── permissions.py
│   │   │   ├── filters.py       # Django Filters
│   │   │   ├── services.py
│   │   │   ├── tasks.py
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   ├── enrollments/         # 수강 등록
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Enrollment, Progress 모델
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── services.py
│   │   │   ├── tasks.py
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   ├── videos/              # 비디오 스트리밍
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Video, VideoQuality 모델
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── services.py      # 비디오 처리 로직
│   │   │   ├── tasks.py         # 트랜스코딩 Tasks
│   │   │   ├── storage.py       # S3 Storage 백엔드
│   │   │   ├── utils.py         # 유틸리티 함수
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   ├── payments/            # 결제 처리
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Order, Payment, Transaction
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── services.py      # Stripe 연동
│   │   │   ├── webhooks.py      # Payment Webhooks
│   │   │   ├── tasks.py
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   ├── reviews/             # 리뷰 및 평점
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Review, Rating
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── services.py
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   ├── categories/          # 카테고리
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Category (Tree Structure)
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   ├── notifications/       # 알림
│   │   │   ├── __init__.py
│   │   │   ├── models.py        # Notification
│   │   │   ├── serializers.py
│   │   │   ├── views.py
│   │   │   ├── urls.py
│   │   │   ├── admin.py
│   │   │   ├── services.py      # 이메일, 푸시 알림
│   │   │   ├── tasks.py
│   │   │   ├── tests/
│   │   │   └── migrations/
│   │   │
│   │   └── analytics/           # 분석 및 통계
│   │       ├── __init__.py
│   │       ├── models.py        # ViewLog, ClickLog
│   │       ├── serializers.py
│   │       ├── views.py
│   │       ├── urls.py
│   │       ├── admin.py
│   │       ├── services.py
│   │       ├── tasks.py
│   │       ├── tests/
│   │       └── migrations/
│   │
│   ├── core/                    # 공통 모듈
│   │   ├── __init__.py
│   │   ├── models.py            # Abstract Base Models
│   │   ├── serializers.py       # Base Serializers
│   │   ├── views.py             # Base Views
│   │   ├── permissions.py       # 공통 권한
│   │   ├── pagination.py        # 커스텀 Pagination
│   │   ├── exceptions.py        # 커스텀 Exceptions
│   │   ├── validators.py        # 공통 Validators
│   │   ├── mixins.py            # Reusable Mixins
│   │   ├── throttling.py        # Rate Limiting
│   │   ├── authentication.py    # JWT 인증
│   │   └── utils/               # 유틸리티
│   │       ├── __init__.py
│   │       ├── cache.py         # 캐시 헬퍼
│   │       ├── email.py         # 이메일 헬퍼
│   │       ├── files.py         # 파일 처리
│   │       ├── time.py          # 시간 관련
│   │       └── decorators.py    # 데코레이터
│   │
│   ├── media/                   # 미디어 파일 (로컬 개발용)
│   │   ├── videos/
│   │   ├── thumbnails/
│   │   └── attachments/
│   │
│   ├── static/                  # 정적 파일
│   │   ├── admin/
│   │   ├── css/
│   │   ├── js/
│   │   └── images/
│   │
│   ├── staticfiles/             # collectstatic 결과물
│   │
│   ├── templates/               # Django 템플릿
│   │   ├── email/               # 이메일 템플릿
│   │   │   ├── base.html
│   │   │   ├── welcome.html
│   │   │   ├── enrollment_confirmation.html
│   │   │   └── receipt.html
│   │   └── admin/               # Admin 커스터마이징
│   │
│   ├── locale/                  # 다국어 지원
│   │   ├── ko/
│   │   │   └── LC_MESSAGES/
│   │   │       ├── django.po
│   │   │       └── django.mo
│   │   └── en/
│   │       └── LC_MESSAGES/
│   │
│   ├── logs/                    # 로그 파일
│   │   ├── debug.log
│   │   ├── error.log
│   │   └── celery.log
│   │
│   ├── scripts/                 # 관리 스크립트
│   │   ├── seed_data.py         # 시드 데이터
│   │   ├── migrate_legacy.py    # 데이터 마이그레이션
│   │   └── cleanup.py           # 정리 스크립트
│   │
│   ├── manage.py                # Django 관리 명령
│   ├── requirements/            # Python 의존성
│   │   ├── base.txt             # 공통
│   │   ├── development.txt      # 개발
│   │   ├── production.txt       # 프로덕션
│   │   └── testing.txt          # 테스트
│   │
│   ├── pytest.ini               # Pytest 설정
│   ├── .coveragerc              # Coverage 설정
│   └── conftest.py              # Pytest Fixtures
│
├── frontend/                    # 프론트엔드 (선택)
│   ├── public/
│   │   ├── index.html
│   │   ├── favicon.ico
│   │   └── manifest.json
│   │
│   ├── src/
│   │   ├── components/          # 재사용 컴포넌트
│   │   │   ├── common/
│   │   │   ├── course/
│   │   │   ├── video/
│   │   │   └── user/
│   │   │
│   │   ├── pages/               # 페이지 컴포넌트
│   │   │   ├── Home/
│   │   │   ├── CourseDetail/
│   │   │   ├── CoursePlayer/
│   │   │   ├── Dashboard/
│   │   │   └── Auth/
│   │   │
│   │   ├── hooks/               # Custom Hooks
│   │   ├── services/            # API 서비스
│   │   ├── store/               # 상태 관리 (Redux/Zustand)
│   │   ├── utils/               # 유틸리티
│   │   ├── styles/              # 스타일
│   │   ├── App.jsx
│   │   └── index.jsx
│   │
│   ├── package.json
│   ├── vite.config.js
│   └── .env.example
│
├── docs/                        # 문서
│   ├── README.md
│   ├── CONTRIBUTING.md
│   ├── api/                     # API 문서
│   │   ├── authentication.md
│   │   ├── courses.md
│   │   ├── enrollments.md
│   │   └── payments.md
│   ├── architecture/            # 아키텍처 문서
│   │   ├── system-design.md
│   │   ├── database-schema.md
│   │   └── deployment.md
│   └── guides/                  # 가이드
│       ├── setup.md
│       ├── development.md
│       └── testing.md
│
├── infrastructure/              # 인프라 코드
│   ├── terraform/               # Terraform
│   │   ├── modules/
│   │   ├── environments/
│   │   │   ├── dev/
│   │   │   ├── staging/
│   │   │   └── production/
│   │   └── main.tf
│   │
│   ├── kubernetes/              # K8s (선택)
│   │   ├── deployments/
│   │   ├── services/
│   │   └── configmaps/
│   │
│   └── docker/                  # Docker 설정
│       ├── Dockerfile.backend
│       ├── Dockerfile.frontend
│       ├── Dockerfile.celery
│       └── nginx.conf
│
├── scripts/                     # 프로젝트 스크립트
│   ├── setup.sh                 # 초기 설정
│   ├── deploy.sh                # 배포 스크립트
│   ├── backup.sh                # 백업
│   └── restore.sh               # 복원
│
├── tests/                       # 통합 테스트
│   ├── integration/
│   ├── e2e/
│   └── performance/
│
├── docker-compose.yml           # 로컬 개발 환경
├── docker-compose.prod.yml      # 프로덕션 환경
├── .env.example                 # 환경 변수 예시
├── .gitignore
├── .editorconfig
├── .prettierrc                  # 코드 포맷팅
├── .eslintrc.js                 # 린팅
├── Makefile                     # 편의 명령
└── README.md                    # 프로젝트 README
```

## 2. 주요 디렉토리 설명

### 2.1 backend/config/
- **역할:** Django 프로젝트의 핵심 설정
- **주요 파일:**
  - `settings/`: 환경별 설정 분리
  - `urls.py`: 전체 URL 라우팅
  - `celery.py`: Celery 초기화

### 2.2 backend/apps/
- **역할:** 도메인별 Django 앱 모음
- **구조:** 각 앱은 독립적이며 응집도 높은 단위
- **네이밍:** 복수형 사용 (users, courses)

### 2.3 backend/core/
- **역할:** 재사용 가능한 공통 모듈
- **포함 내용:**
  - Abstract Base Models
  - Custom Permissions
  - Utility Functions
  - Mixins

### 2.4 frontend/
- **역할:** React/Vue.js 프론트엔드
- **구조:**
  - `components/`: 재사용 컴포넌트
  - `pages/`: 페이지별 컴포넌트
  - `services/`: API 통신

### 2.5 infrastructure/
- **역할:** IaC (Infrastructure as Code)
- **도구:**
  - Terraform: AWS 리소스 관리
  - Kubernetes: 컨테이너 오케스트레이션
  - Docker: 컨테이너화

## 3. 설정 파일 예시

### 3.1 backend/config/settings/base.py
```python
# 공통 설정
import os
from pathlib import Path

BASE_DIR = Path(__file__).resolve().parent.parent.parent

SECRET_KEY = os.getenv('SECRET_KEY')

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Third-party apps
    'rest_framework',
    'rest_framework_simplejwt',
    'django_filters',
    'corsheaders',
    'storages',

    # Local apps
    'apps.users',
    'apps.courses',
    'apps.enrollments',
    'apps.videos',
    'apps.payments',
    'apps.reviews',
    'apps.categories',
    'apps.notifications',
    'apps.analytics',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.IsAuthenticated',
    ),
    'DEFAULT_PAGINATION_CLASS': 'core.pagination.CustomPagination',
    'PAGE_SIZE': 20,
}
```

### 3.2 docker-compose.yml
```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: learning_platform
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

  backend:
    build:
      context: ./backend
      dockerfile: ../infrastructure/docker/Dockerfile.backend
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - ./backend:/app
    ports:
      - "8000:8000"
    depends_on:
      - db
      - redis
    env_file:
      - .env

  celery:
    build:
      context: ./backend
      dockerfile: ../infrastructure/docker/Dockerfile.celery
    command: celery -A config worker -l info
    volumes:
      - ./backend:/app
    depends_on:
      - db
      - redis
    env_file:
      - .env

  frontend:
    build:
      context: ./frontend
      dockerfile: ../infrastructure/docker/Dockerfile.frontend
    command: npm run dev
    volumes:
      - ./frontend:/app
      - /app/node_modules
    ports:
      - "3000:3000"
    depends_on:
      - backend

volumes:
  postgres_data:
```

### 3.3 Makefile
```makefile
.PHONY: help setup up down migrate test

help:
	@echo "사용 가능한 명령어:"
	@echo "  make setup      - 초기 설정"
	@echo "  make up         - 서비스 시작"
	@echo "  make down       - 서비스 종료"
	@echo "  make migrate    - DB 마이그레이션"
	@echo "  make test       - 테스트 실행"

setup:
	cp .env.example .env
	docker-compose build
	docker-compose run backend python manage.py migrate
	docker-compose run backend python manage.py createsuperuser

up:
	docker-compose up -d

down:
	docker-compose down

migrate:
	docker-compose run backend python manage.py makemigrations
	docker-compose run backend python manage.py migrate

test:
	docker-compose run backend pytest

shell:
	docker-compose run backend python manage.py shell

logs:
	docker-compose logs -f backend
```

## 4. 모범 사례

### 4.1 앱 구조 원칙
1. **단일 책임 원칙:** 각 앱은 하나의 도메인만 담당
2. **느슨한 결합:** 앱 간 의존성 최소화
3. **높은 응집도:** 관련 기능은 같은 앱에

### 4.2 파일 명명 규칙
- **모델:** `models.py` 또는 `models/`
- **뷰:** `views.py` 또는 `views/`
- **테스트:** `test_*.py`
- **서비스:** `services.py` (비즈니스 로직)

### 4.3 환경 변수 관리
- `.env.example`: 예시 환경 변수
- `.env`: 실제 환경 변수 (gitignore)
- `django-environ` 사용 권장

## 5. 확장 고려사항

### 마이크로서비스 전환 시
```
learning-platform/
├── services/
│   ├── user-service/
│   ├── course-service/
│   ├── video-service/
│   ├── payment-service/
│   └── notification-service/
├── api-gateway/
└── shared/
    ├── proto/
    └── libs/
```
```
