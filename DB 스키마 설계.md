# 버그 수정 프롬프트

## 프로젝트 정보
- 프로젝트명: Django 동영상 스트리밍 플랫폼

## 요청 템플릿

```
다음 버그를 분석하고 수정 방법을 제시해주세요.

[버그 설명]:
[재현 단계]:
1.
2.
3.

[예상 동작]:
[실제 동작]:
[에러 메시지]:
```
[에러 로그나 스택 트레이스 붙여넣기]
```

[관련 코드]:
```python
# 버그가 발생하는 코드
```

답변 형식:
# 버그 수정 가이드

## 1. 버그 정보

### 1.1 기본 정보
- **버그 ID:** BUG-001
- **심각도:** 🔴 Critical / 🟡 Major / 🟢 Minor
- **우선순위:** High / Medium / Low
- **발견일:** 2024-01-20
- **보고자:** [이름]
- **담당자:** [이름]

### 1.2 버그 요약
**제목:** [간단한 버그 설명]

**설명:**
[상세한 버그 설명]

### 1.3 영향 범위
- **영향받는 기능:** [기능명]
- **영향받는 사용자:** [사용자 유형]
- **재현 빈도:** 항상 / 가끔 / 드물게

## 2. 버그 재현

### 2.1 재현 환경
- **OS:** Windows / macOS / Linux
- **브라우저:** Chrome / Firefox / Safari
- **Django 버전:** 4.2
- **Python 버전:** 3.11
- **데이터베이스:** PostgreSQL 15

### 2.2 재현 단계
1. [단계 1]
2. [단계 2]
3. [단계 3]

### 2.3 예상 vs 실제 동작
| 항목 | 내용 |
|------|------|
| 예상 동작 | [기대하는 정상 동작] |
| 실제 동작 | [현재 발생하는 문제] |

## 3. 에러 분석

### 3.1 에러 메시지
```
[에러 메시지 전체]
```

### 3.2 스택 트레이스
```python
Traceback (most recent call last):
  File "...", line X, in function_name
    [코드]
Exception: [에러 내용]
```

### 3.3 로그 분석
```
[관련 로그]
```

## 4. 근본 원인 분석 (Root Cause Analysis)

### 4.1 문제 발생 위치
**파일:** `apps/module/views.py`
**라인:** 45-50

```python
# 문제가 있는 코드
def problematic_function(param):
    # 버그가 있는 로직
    result = some_operation(param)  # 여기서 에러 발생
    return result
```

### 4.2 원인 분석
**주요 원인:**
- [근본 원인 1: 구체적인 설명]
- [근본 원인 2: 구체적인 설명]

**왜 발생했는가:**
1. [원인 1에 대한 상세 분석]
2. [원인 2에 대한 상세 분석]

**예시 시나리오:**
- [구체적인 발생 상황 예시]

### 4.3 관련 코드 분석

#### 문제 코드 #1
**위치:** `apps/courses/models.py:45`

```python
# 현재 코드
class Course(models.Model):
    price = models.DecimalField(max_digits=10, decimal_places=2)

    def calculate_discount(self, percentage):
        # 문제: percentage 검증 없음
        return self.price * (1 - percentage / 100)
```

**문제점:**
- percentage가 음수이거나 100보다 큰 경우 처리 안 됨
- Decimal 타입 혼용으로 인한 정밀도 문제

#### 문제 코드 #2
**위치:** `apps/enrollments/views.py:78`

```python
# 현재 코드
def enroll_course(request, course_id):
    course = Course.objects.get(id=course_id)  # 문제: DoesNotExist 처리 안 됨
    enrollment = Enrollment.objects.create(
        user=request.user,
        course=course
    )
    return Response({'status': 'success'})
```

**문제점:**
- Course가 없을 때 500 에러 발생
- 중복 등록 체크 없음
- 트랜잭션 처리 없음

## 5. 수정 방안

### 5.1 수정 전략
- **접근 방법:** [수정 방식 설명]
- **영향도 평가:** [변경 영향 범위]
- **롤백 계획:** [문제 발생 시 대응]

### 5.2 코드 수정

#### 수정 #1: 검증 로직 추가
**파일:** `apps/courses/models.py`

**Before:**
```python
def calculate_discount(self, percentage):
    return self.price * (1 - percentage / 100)
```

**After:**
```python
from decimal import Decimal

def calculate_discount(self, percentage):
    """
    할인가 계산

    Args:
        percentage: 할인율 (0-100)

    Returns:
        Decimal: 할인된 가격

    Raises:
        ValueError: 유효하지 않은 할인율
    """
    if not 0 <= percentage <= 100:
        raise ValueError(f"할인율은 0-100 사이여야 합니다: {percentage}")

    discount_rate = Decimal(str(percentage)) / Decimal('100')
    discounted_price = self.price * (Decimal('1') - discount_rate)

    return discounted_price.quantize(Decimal('0.01'))
```

**변경 사항:**
- ✅ 할인율 범위 검증 추가 (0-100)
- ✅ Decimal 타입으로 통일하여 정밀도 문제 해결
- ✅ Docstring 추가
- ✅ 반환값 소수점 2자리로 정규화

---

#### 수정 #2: 예외 처리 및 검증
**파일:** `apps/enrollments/views.py`

**Before:**
```python
def enroll_course(request, course_id):
    course = Course.objects.get(id=course_id)
    enrollment = Enrollment.objects.create(
        user=request.user,
        course=course
    )
    return Response({'status': 'success'})
```

**After:**
```python
from django.db import transaction
from rest_framework import status
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated
from rest_framework.response import Response
from django.shortcuts import get_object_or_404

@api_view(['POST'])
@permission_classes([IsAuthenticated])
def enroll_course(request, course_id):
    """
    강의 수강 등록

    Args:
        course_id: 강의 ID

    Returns:
        Response: 등록 결과
    """
    # Course 존재 여부 확인
    course = get_object_or_404(Course, id=course_id, status='published')

    # 이미 등록했는지 확인
    if Enrollment.objects.filter(user=request.user, course=course).exists():
        return Response(
            {
                'success': False,
                'error': {
                    'code': 'ALREADY_ENROLLED',
                    'message': '이미 수강 중인 강의입니다.'
                }
            },
            status=status.HTTP_400_BAD_REQUEST
        )

    # 트랜잭션으로 안전하게 처리
    try:
        with transaction.atomic():
            enrollment = Enrollment.objects.create(
                user=request.user,
                course=course
            )

            # 강의 수강생 수 증가
            course.total_enrollments = models.F('total_enrollments') + 1
            course.save(update_fields=['total_enrollments'])

            # 환영 이메일 발송 (비동기)
            from apps.notifications.tasks import send_enrollment_email
            send_enrollment_email.delay(enrollment.id)

        return Response(
            {
                'success': True,
                'data': {
                    'enrollment_id': enrollment.id,
                    'course_title': course.title,
                    'enrolled_at': enrollment.enrollment_date
                },
                'message': '강의 수강 등록이 완료되었습니다.'
            },
            status=status.HTTP_201_CREATED
        )

    except Exception as e:
        # 로깅
        import logging
        logger = logging.getLogger(__name__)
        logger.error(f"수강 등록 실패: user_id={request.user.id}, course_id={course_id}, error={str(e)}")

        return Response(
            {
                'success': False,
                'error': {
                    'code': 'ENROLLMENT_FAILED',
                    'message': '수강 등록 중 오류가 발생했습니다. 잠시 후 다시 시도해주세요.'
                }
            },
            status=status.HTTP_500_INTERNAL_SERVER_ERROR
        )
```

**변경 사항:**
- ✅ `get_object_or_404`로 404 에러 처리
- ✅ 발행된 강의만 등록 가능하도록 필터링
- ✅ 중복 등록 체크 추가
- ✅ 트랜잭션 처리로 데이터 일관성 보장
- ✅ F() 표현식으로 race condition 방지
- ✅ 예외 처리 및 로깅 추가
- ✅ 명확한 에러 응답

---

#### 수정 #3: 테스트 코드 추가
**파일:** `apps/courses/tests/test_models.py`

```python
from decimal import Decimal
from django.test import TestCase
from apps.courses.models import Course

class CourseModelTest(TestCase):
    def setUp(self):
        self.course = Course.objects.create(
            title="Test Course",
            price=Decimal('100.00')
        )

    def test_calculate_discount_valid(self):
        """정상적인 할인율로 계산"""
        result = self.course.calculate_discount(20)
        self.assertEqual(result, Decimal('80.00'))

    def test_calculate_discount_zero(self):
        """0% 할인"""
        result = self.course.calculate_discount(0)
        self.assertEqual(result, Decimal('100.00'))

    def test_calculate_discount_full(self):
        """100% 할인"""
        result = self.course.calculate_discount(100)
        self.assertEqual(result, Decimal('0.00'))

    def test_calculate_discount_negative(self):
        """음수 할인율 - 에러 발생"""
        with self.assertRaises(ValueError) as context:
            self.course.calculate_discount(-10)
        self.assertIn("0-100 사이", str(context.exception))

    def test_calculate_discount_over_100(self):
        """100 초과 할인율 - 에러 발생"""
        with self.assertRaises(ValueError) as context:
            self.course.calculate_discount(150)
        self.assertIn("0-100 사이", str(context.exception))

    def test_calculate_discount_decimal_precision(self):
        """소수점 정밀도 테스트"""
        result = self.course.calculate_discount(33.33)
        # 소수점 2자리까지만
        self.assertEqual(result.as_tuple().exponent, -2)
```

## 6. 검증 계획

### 6.1 단위 테스트
```bash
# 특정 테스트 실행
pytest apps/courses/tests/test_models.py::CourseModelTest::test_calculate_discount_valid

# 전체 테스트 실행
pytest apps/courses/tests/

# 커버리지 확인
pytest --cov=apps/courses apps/courses/tests/
```

### 6.2 통합 테스트
- [ ] API 엔드포인트 테스트
- [ ] 데이터베이스 트랜잭션 테스트
- [ ] 에러 케이스 테스트

### 6.3 수동 테스트
1. [ ] 정상 시나리오 테스트
2. [ ] 엣지 케이스 테스트
3. [ ] 에러 케이스 테스트
4. [ ] 성능 테스트 (필요시)

## 7. 배포 계획

### 7.1 배포 전 체크리스트
- [ ] 코드 리뷰 완료
- [ ] 테스트 통과 확인
- [ ] 마이그레이션 필요 여부 확인
- [ ] 설정 변경 사항 확인
- [ ] 의존성 변경 사항 확인

### 7.2 배포 단계
1. **개발 환경 테스트**
   - 로컬에서 수정 사항 테스트

2. **스테이징 배포**
   - 스테이징 환경에 배포
   - QA 테스트 수행

3. **프로덕션 배포**
   - 배포 시간: [업무 시간 외]
   - 모니터링 강화
   - 롤백 준비

### 7.3 롤백 계획
```bash
# Git 롤백
git revert <commit-hash>
git push origin main

# 마이그레이션 롤백 (필요시)
python manage.py migrate app_name <previous_migration>
```

## 8. 모니터링

### 8.1 모니터링 지표
- [ ] 에러율 확인
- [ ] 응답 시간 확인
- [ ] 데이터베이스 부하 확인
- [ ] 사용자 피드백 모니터링

### 8.2 알림 설정
- 에러 발생 시 즉시 알림
- 성능 저하 시 알림

## 9. 사후 조치

### 9.1 문서 업데이트
- [ ] API 문서 업데이트
- [ ] 변경 로그 작성
- [ ] 장애 보고서 작성 (Critical 버그인 경우)

### 9.2 재발 방지
- [ ] 유사 버그 패턴 검색
- [ ] 코드 리뷰 체크리스트 업데이트
- [ ] 린터 규칙 추가 (가능한 경우)
- [ ] 팀 공유 및 교육

### 9.3 회고
**배운 점:**
- [이번 버그를 통해 배운 내용]

**개선점:**
- [앞으로 개선할 사항]

## 10. 참고 자료
- [관련 이슈 링크]
- [Stack Overflow 링크]
- [공식 문서 링크]

## 버그 수정 체크리스트

### 분석
- [ ] 버그 재현 완료
- [ ] 근본 원인 파악
- [ ] 영향 범위 확인

### 수정
- [ ] 수정 코드 작성
- [ ] 테스트 코드 작성
- [ ] 코드 리뷰 요청

### 검증
- [ ] 단위 테스트 통과
- [ ] 통합 테스트 통과
- [ ] 수동 테스트 완료

### 배포
- [ ] 스테이징 배포 및 테스트
- [ ] 프로덕션 배포
- [ ] 모니터링 확인

### 마무리
- [ ] 문서 업데이트
- [ ] 이슈 종료
- [ ] 재발 방지 조치
```
