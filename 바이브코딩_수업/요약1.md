# 바이브 코딩은 무엇이고 왜 배워야 하는가?

* 바이브 코딩: 생성형 AI를 활용한 SW개발 방법론, 이 단어는 cursor와 같은 도구가 있었을 때 나왔던 개념이지만 지금 Claude Code와 같은 생성형 AI 도구들이 나오면서 더 주목받고 있으며 그 가능성 또한 크다라고 생각하고 있습니다. '바이브 코딩'이라는 단어의 어감으로는 '가볍다'라고 생각하실 수 있지만 견고한 코드를 작성하는데에도 현재는 바이브 코딩이 활용되고 있습니다.
* 왜 배워야 하는가: 아래 있는 기본 명령어 등 클로드 코드를 잘 사용할 수 있는 방법을 배우는 것이 바이브 코딩에 도움이 됩니다. 그리고 이러한 '지식'이 아니라 기존에 해왔던 '경험' 측면에 '요구사항 명세'를 잘 쓰는 방법은 배우지 않고서는 단편적인 명령만 하게 됩니다. 
* 실습: 바이브 코딩 랜딩 페이지를 만들고 일부를 수정하게 한 다음 'CSS'도 수정이 되어있나 보세요. 대부분 살아있습니다. 그러니까 요청한 요청사항만 수행하고 그에 수반되는 행위를 하지 않습니다. 그렇기 때문에 '요청' + '요청' + '요청'으로 이어지는 것은 비효율적이며, 우리가 만들고 싶은 프로덕트를 만들지도 못할 확률이 높아집니다. 또한 질문이 누적됨에 따라 유지보수가 급속도로 어려워집니다.
* 바이브 코딩에서 사람의 역할은 디렉터 또는 PM이라고 생각합니다. 여러분이 기획, 디자인, 개발 등을 모두 지시야 하기 때문에 대략적인 정보를 가지고 있어야 합니다.
* 이렇게 되었을 때 5년 후, 10년 후에는 어떨까요? '평범의 종말'이 있을 수도 있다고 생각합니다. AI가 인간의 일자리를 대체하는 것이 아니라 인간이 평범한 일을 하는 것이 사라진다는 내용입니다.

# 기본 명령어

* https://docs.claude.com/ko/docs/claude-code/slash-commands

# 팁
* 개발을 이어서 한다면 새로운 세션을 시작하거나 대화를 이어서 할지 결정해야 하는데 대화를 이어서하면 답변의 정확도가 떨어지기 때문에 대부분 새로운 세션을 시작하는 것이 좋습니다. 그럴 때 이전에 있었던 대화를 /compact 명령어로 요약해서 제공하면 도움이 됩니다.

```
══════════════════════════════════════════ Conversation compacted · ctrl+o for history ═════════════════════════════════════════

> This session is being continued from a previous conversation that ran out of context. The conversation is summarized 
below:
Analysis:
Let me chronologically analyze the conversation:

1. **Initial Request**: User asked me to analyze the codebase and create a CLAUDE.md file. However, the directory was 
initially empty.

2. **Second Request (Korean)**: User said "바이브 코딩 랜딩 페이지를 만들어줘" (Create a Vibe Coding landing page). This was   the actual primary request.

... 생략 ...
```
* rewind를 사용하면 대화를 되돌릴 수 있습니다. 선택지는 3개입니다.

```
 Rewind

 Confirm you want to restore to the point before you sent this message:

 │ index.html에서 '인기 강의'는 필요 없어. 지워줘. 단일 강의야!
 │ (25m ago)

 The conversation will be forked.
 The code will be restored +43 -23 in index.html and 2 other files.

 ❯ 1. Restore code and conversation
   2. Restore conversation
   3. Restore code

 ⚠ Rewinding does not affect files edited manually or via bash.


 Enter to continue · Esc to exit
 ```

# 효율적인 작업 방법

1. 프로젝트 폴더를 만들고 VS Code로 열기
2. 터미널에서 `claude` 실행
3. `/plan`모드로 프로젝트 기획
4. `/plan`모드로 나온 모든 절차를 각각 `.md`파일로 저장
5. 각각에 절차를 claude를 통해 템플릿으로 고도화 해달라고 요청(프롬프트 라이브러리 구축)
6. 프로젝트 초기 아키텍처는 가능하면 직접 설정을 하시기를 권해드립니다. install, db 반영, 기본 폴더 구조 등
7. 개인이나 회사에 가지고 있는 자산(아이콘, header와 footer 등의 디자인 정보, 이미지 등)을 claude가 참고할 수 있게 정리(프롬프트 디자인 시스템 등을 구축)
8. 단계별로 개발 시작
9. `/init`으로 프로젝트 초기화
10. 필요할 때마다 `/review`로 코드 검토
11. 대화가 길어지면 `/clear` 또는 `/compact` 정리
12. 사용량이 궁금하면 `/context`또는 `/cost`로 확인

# 종류별 요구사항 템플릿

## 세팅 요구사항 템플릿

```
# [제목] 서비스 MVP 요구사항 명세서

## 1. 서비스 개요
**목적**: [서비스 개발에 구체적인 개발 목적]
**타겟 사용자**: [나이, 성별 등 상세한 타겟팅, 구체적 사용자 페르소나]
**핵심 가치**: [핵심적으로 전달하려고 하는 가치]
**비즈니스 모델**: [수익 모델]
**목표**: [사용자가 이 서비스를 통해 얻는 구체적 경험, 성공지표 등 KPI]
**제약사항**: [시간, 예산, 기술적 한계]

## 2. 주요 기능
### 2.1 [로그인, 주문 등] 기능
- [기능의 상세 내용]

## 3. 화면 구성
### 3.1 [메인, 상품 목록 등] 페이지
- [페이지 상세 정보, 기능과 구성 모두 상세하게 제시]

## 4. 사용자 시나리오
1. [사용자가 메인 페이지에 들어옴 등의 구체적 시나리오 단계별 제시]

## 5. 디자인 요청사항
[5장에서 작성된 구체적 디자인 요구사항 design-system.md 또는 design-system.JSON 파일 경로]를 기반으로 개발

## 6. 시스템 아키텍처
**Frontend**: [React, Tailwind CSS 등 기술과 버전 명시]
**Backend**: [Django, Spring 등 기술과 버전 명시]
**Database**: [개발과 프로덕션 기술 명시]
**Payment**: [결제 스택 명시]
**Deployment**: [배포 스택 명시]

## 7. ERD
[머메이드 형태의 ERD를 기입합니다. 만약 이 부분이 없을 경우 문서 작성을 요구합니다.]

## 8. URL 구조

### 8.1 게시글 API
[App, Method, URL, ViewClass, Note, Auth 등 테이블 형테로 제시합니다. 만약 이 부분이 없을 경우 문서 작성을 요구합니다.]

## 9. 폴더 구조
[폴더와 폴더 안 파일이 있어야 하는 내용 등을 명시합니다. 그렇지 않을 경우 원하지 않는 구조를 잡아 프로젝트를 시작할 수 있으니 꼼꼼하게 작성하길 권합니다.]

## 10. 개발 일정(WBS)과 우선순위
[머메이드 형태의 개발 일정과 일정별 우선순위 기입]
```