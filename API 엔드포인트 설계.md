# 기능 구현 프롬프트

## 프로젝트 정보
- 프로젝트명: Django 동영상 스트리밍 플랫폼
- 프레임워크: Django + Django REST Framework

## 요청 템플릿

```
Django 온라인 강의 플랫폼의 [기능명]을 구현해주세요.

[기능 설명]:
[요구사항]:
[제약사항]:

답변 형식:
# [기능명] 구현

## 1. 구현 개요
- **기능 설명:** [간단한 설명]
- **관련 모듈:** [앱 이름]
- **의존성:** [필요한 패키지]

## 2. 데이터 모델

### models.py
```python
from django.db import models
from core.models import TimeStampedModel

class [ModelName](TimeStampedModel):
    """
    [모델 설명]
    """
    # 필드 정의
    name = models.CharField(max_length=200, verbose_name="이름")
    description = models.TextField(blank=True, verbose_name="설명")
    is_active = models.BooleanField(default=True, verbose_name="활성화")

    # 관계
    user = models.ForeignKey(
        'users.User',
        on_delete=models.CASCADE,
        related_name='related_items',
        verbose_name="사용자"
    )

    class Meta:
        db_table = 'table_name'
        verbose_name = "모델명"
        verbose_name_plural = "모델명 목록"
        ordering = ['-created_at']
        indexes = [
            models.Index(fields=['user', '-created_at']),
        ]

    def __str__(self):
        return self.name

    def custom_method(self):
        """커스텀 메서드"""
        pass
```

## 3. 시리얼라이저

### serializers.py
```python
from rest_framework import serializers
from .models import [ModelName]

class [ModelName]ListSerializer(serializers.ModelSerializer):
    """목록 조회용 Serializer"""
    user_name = serializers.CharField(source='user.username', read_only=True)

    class Meta:
        model = [ModelName]
        fields = [
            'id',
            'name',
            'description',
            'user_name',
            'is_active',
            'created_at',
        ]
        read_only_fields = ['id', 'created_at']

class [ModelName]DetailSerializer(serializers.ModelSerializer):
    """상세 조회용 Serializer"""
    user = UserSerializer(read_only=True)

    class Meta:
        model = [ModelName]
        fields = '__all__'
        read_only_fields = ['id', 'created_at', 'updated_at']

class [ModelName]CreateSerializer(serializers.ModelSerializer):
    """생성용 Serializer"""

    class Meta:
        model = [ModelName]
        fields = [
            'name',
            'description',
        ]

    def validate_name(self, value):
        """이름 검증"""
        if len(value) < 3:
            raise serializers.ValidationError("이름은 최소 3자 이상이어야 합니다.")
        return value

    def create(self, validated_data):
        """커스텀 생성 로직"""
        user = self.context['request'].user
        validated_data['user'] = user
        return super().create(validated_data)

class [ModelName]UpdateSerializer(serializers.ModelSerializer):
    """수정용 Serializer"""

    class Meta:
        model = [ModelName]
        fields = [
            'name',
            'description',
            'is_active',
        ]
```

## 4. 비즈니스 로직

### services.py
```python
from django.db import transaction
from .models import [ModelName]

class [ModelName]Service:
    """[기능명] 비즈니스 로직"""

    @staticmethod
    def create_item(user, data):
        """아이템 생성"""
        with transaction.atomic():
            item = [ModelName].objects.create(
                user=user,
                **data
            )
            # 추가 비즈니스 로직
            return item

    @staticmethod
    def update_item(item, data):
        """아이템 수정"""
        with transaction.atomic():
            for key, value in data.items():
                setattr(item, key, value)
            item.save()
            # 추가 비즈니스 로직
            return item

    @staticmethod
    def delete_item(item):
        """아이템 삭제"""
        with transaction.atomic():
            # soft delete 또는 hard delete
            item.is_active = False
            item.save()
            # 또는 item.delete()

    @staticmethod
    def process_complex_logic(item):
        """복잡한 비즈니스 로직"""
        # 여러 단계의 처리
        pass
```

## 5. API 뷰

### views.py
```python
from rest_framework import viewsets, status
from rest_framework.decorators import action
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated
from django_filters.rest_framework import DjangoFilterBackend

from .models import [ModelName]
from .serializers import (
    [ModelName]ListSerializer,
    [ModelName]DetailSerializer,
    [ModelName]CreateSerializer,
    [ModelName]UpdateSerializer,
)
from .services import [ModelName]Service
from .filters import [ModelName]Filter
from .permissions import IsOwnerOrReadOnly

class [ModelName]ViewSet(viewsets.ModelViewSet):
    """
    [기능명] API ViewSet

    list: 목록 조회
    retrieve: 상세 조회
    create: 생성
    update: 전체 수정
    partial_update: 부분 수정
    destroy: 삭제
    """
    queryset = [ModelName].objects.select_related('user').filter(is_active=True)
    permission_classes = [IsAuthenticated, IsOwnerOrReadOnly]
    filter_backends = [DjangoFilterBackend]
    filterset_class = [ModelName]Filter

    def get_serializer_class(self):
        """액션에 따른 Serializer 선택"""
        if self.action == 'list':
            return [ModelName]ListSerializer
        elif self.action == 'create':
            return [ModelName]CreateSerializer
        elif self.action in ['update', 'partial_update']:
            return [ModelName]UpdateSerializer
        return [ModelName]DetailSerializer

    def get_queryset(self):
        """사용자별 쿼리셋 필터링"""
        queryset = super().get_queryset()

        # 관리자가 아닌 경우 본인 데이터만
        if not self.request.user.is_staff:
            queryset = queryset.filter(user=self.request.user)

        return queryset

    def perform_create(self, serializer):
        """생성 시 추가 로직"""
        item = [ModelName]Service.create_item(
            user=self.request.user,
            data=serializer.validated_data
        )
        serializer.instance = item

    def perform_update(self, serializer):
        """수정 시 추가 로직"""
        item = [ModelName]Service.update_item(
            item=self.get_object(),
            data=serializer.validated_data
        )
        serializer.instance = item

    def perform_destroy(self, instance):
        """삭제 시 추가 로직"""
        [ModelName]Service.delete_item(instance)

    @action(detail=True, methods=['post'])
    def custom_action(self, request, pk=None):
        """커스텀 액션"""
        item = self.get_object()

        # 비즈니스 로직 처리
        result = [ModelName]Service.process_complex_logic(item)

        return Response({
            'success': True,
            'data': result,
            'message': '처리가 완료되었습니다.'
        })

    @action(detail=False, methods=['get'])
    def my_items(self, request):
        """내 아이템 목록"""
        queryset = self.get_queryset().filter(user=request.user)

        page = self.paginate_queryset(queryset)
        if page is not None:
            serializer = self.get_serializer(page, many=True)
            return self.get_paginated_response(serializer.data)

        serializer = self.get_serializer(queryset, many=True)
        return Response(serializer.data)
```

## 6. 권한 관리

### permissions.py
```python
from rest_framework import permissions

class IsOwnerOrReadOnly(permissions.BasePermission):
    """
    소유자만 수정/삭제 가능, 읽기는 모두 가능
    """

    def has_object_permission(self, request, view, obj):
        # 읽기 권한은 모두 허용
        if request.method in permissions.SAFE_METHODS:
            return True

        # 쓰기 권한은 소유자만
        return obj.user == request.user

class IsInstructor(permissions.BasePermission):
    """강사 권한"""

    def has_permission(self, request, view):
        return request.user.is_authenticated and request.user.role == 'instructor'
```

## 7. 필터링

### filters.py
```python
from django_filters import rest_framework as filters
from .models import [ModelName]

class [ModelName]Filter(filters.FilterSet):
    """[기능명] 필터"""

    name = filters.CharFilter(lookup_expr='icontains')
    created_after = filters.DateTimeFilter(field_name='created_at', lookup_expr='gte')
    created_before = filters.DateTimeFilter(field_name='created_at', lookup_expr='lte')

    class Meta:
        model = [ModelName]
        fields = ['name', 'is_active', 'user']
```

## 8. URL 라우팅

### urls.py
```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import [ModelName]ViewSet

router = DefaultRouter()
router.register(r'items', [ModelName]ViewSet, basename='item')

app_name = 'app_name'

urlpatterns = [
    path('', include(router.urls)),
]
```

## 9. Admin 설정

### admin.py
```python
from django.contrib import admin
from .models import [ModelName]

@admin.register([ModelName])
class [ModelName]Admin(admin.ModelAdmin):
    list_display = ['id', 'name', 'user', 'is_active', 'created_at']
    list_filter = ['is_active', 'created_at']
    search_fields = ['name', 'user__username']
    readonly_fields = ['created_at', 'updated_at']

    fieldsets = (
        ('기본 정보', {
            'fields': ('name', 'description', 'user')
        }),
        ('설정', {
            'fields': ('is_active',)
        }),
        ('메타 정보', {
            'fields': ('created_at', 'updated_at'),
            'classes': ('collapse',)
        }),
    )
```

## 10. 백그라운드 작업 (Celery)

### tasks.py
```python
from celery import shared_task
from .models import [ModelName]
from .services import [ModelName]Service

@shared_task
def process_item_async(item_id):
    """비동기 아이템 처리"""
    try:
        item = [ModelName].objects.get(id=item_id)
        [ModelName]Service.process_complex_logic(item)
        return {'status': 'success', 'item_id': item_id}
    except [ModelName].DoesNotExist:
        return {'status': 'error', 'message': '아이템을 찾을 수 없습니다.'}

@shared_task
def cleanup_old_items():
    """오래된 아이템 정리"""
    from django.utils import timezone
    from datetime import timedelta

    threshold_date = timezone.now() - timedelta(days=30)
    count = [ModelName].objects.filter(
        is_active=False,
        updated_at__lt=threshold_date
    ).delete()

    return {'deleted_count': count[0]}
```

## 11. 시그널

### signals.py
```python
from django.db.models.signals import post_save, pre_delete
from django.dispatch import receiver
from .models import [ModelName]

@receiver(post_save, sender=[ModelName])
def item_created(sender, instance, created, **kwargs):
    """아이템 생성 후"""
    if created:
        # 생성 후 처리 (알림, 로그 등)
        pass

@receiver(pre_delete, sender=[ModelName])
def item_deleting(sender, instance, **kwargs):
    """아이템 삭제 전"""
    # 삭제 전 정리 작업
    pass
```

## 12. 사용 예시

### API 호출 예시

#### 생성
```bash
curl -X POST http://localhost:8000/api/v1/items/ \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "테스트 아이템",
    "description": "설명"
  }'
```

#### 목록 조회
```bash
curl -X GET "http://localhost:8000/api/v1/items/?is_active=true&name=test" \
  -H "Authorization: Bearer {token}"
```

#### 상세 조회
```bash
curl -X GET http://localhost:8000/api/v1/items/1/ \
  -H "Authorization: Bearer {token}"
```

#### 수정
```bash
curl -X PATCH http://localhost:8000/api/v1/items/1/ \
  -H "Authorization: Bearer {token}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "수정된 이름"
  }'
```

#### 삭제
```bash
curl -X DELETE http://localhost:8000/api/v1/items/1/ \
  -H "Authorization: Bearer {token}"
```

## 13. 테스트 코드 작성 가이드

### 필수 테스트 항목
- [ ] 모델 생성 및 저장
- [ ] 필드 검증
- [ ] API 엔드포인트 (CRUD)
- [ ] 권한 검사
- [ ] 비즈니스 로직
- [ ] 엣지 케이스

## 14. 체크리스트

### 구현 전
- [ ] 요구사항 명확히 파악
- [ ] 데이터 모델 설계
- [ ] API 스펙 정의

### 구현 중
- [ ] 모델 작성
- [ ] 시리얼라이저 작성
- [ ] 뷰 작성
- [ ] 권한 설정
- [ ] URL 라우팅

### 구현 후
- [ ] 테스트 작성 및 실행
- [ ] 코드 리뷰
- [ ] 문서화
- [ ] 마이그레이션 파일 확인
```
