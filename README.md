# 학습 관리 시스템(Learning Management System)
## 진행 방법
* 학습 관리 시스템의 수강신청 요구사항을 파악한다.
* 요구사항에 대한 구현을 완료한 후 자신의 github 아이디에 해당하는 브랜치에 Pull Request(이하 PR)를 통해 코드 리뷰 요청을 한다.
* 코드 리뷰 피드백에 대한 개선 작업을 하고 다시 PUSH한다.
* 모든 피드백을 완료하면 다음 단계를 도전하고 앞의 과정을 반복한다.

## 온라인 코드 리뷰 과정
* [텍스트와 이미지로 살펴보는 온라인 코드 리뷰 과정](https://github.com/next-step/nextstep-docs/tree/master/codereview)

# 2단계 수강신청(도메인 모델)


### 수강 신청 기능 요구사항
* 과정(Course)은 기수 단위로 여러 개의 강의(Session)를 가질 수 있다.
* 강의는 시작일과 종료일을 가진다.
* 강의는 강의 커버 이미지 정보를 가진다.
* 강의는 무료 강의와 유료 강의로 나뉜다.
* 강의 상태는 준비중, 모집중, 종료 3가지 상태를 가진다.
* 강의 수강신청은 강의 상태가 모집중일 때만 가능하다.
* 강의는 강의 최대 수강 인원을 초과할 수 없다.

### 구현 목록
* [x] 이미지 객체 생성
* [ ] 강의 객체 구현 
  * [x] 강의 개설 기능
    * 강의 개설 시에는 개설자가 별도로 상태를 넣어주지 않으면 default 준비중
    * 강의 시작일은 등록일보다 앞 설수 없음
    * 강의 종료일은 강의 시작일보다 앞일 수 없음
  * [ ] 강의 이미지 커버 수정 기능
    * 강의 개설자만 변경 가능
  * [ ] 강의 상태 변경 기능
    * 강의 개설자만 변경 가능 
    * 강의가 끝나지않았다면 원할 때에 강의 상태를 모집중으로 변경 가능
  * [ ] 강의 수강 신청 기능
    * [ ] 강의 최대 수강 인원 유효성 체크 기능
      * 강의 등록인원은 최대 수강 인원을 초과할 수 없음
  * [ ] 강의 수강 취소 기능
  * [ ] 강의 유료 무료 타입 수정 기능
  * [x] 강의 상태가 준비중일 때만 수정 가능

* [ ] 과정 객체 구현

---

# 1단계 (레거시 코드 리팩터링)

## 질문 삭제하기 요구사항

- 질문 데이터를 완전히 삭제하는 것이 아니라 데이터의 상태를 삭제 상태(deleted - boolean type)로 변경한다.
- 로그인 사용자와 질문한 사람이 같은 경우 삭제 가능하다.
- 답변이 없는 경우 삭제가 가능하다.
- 질문자와 답변 글의 모든 답변자 같은 경우 삭제가 가능하다.
- 질문을 삭제할 때 답변 또한 삭제해야 하며, 답변의 삭제 또한 삭제 상태(deleted)를 변경 한다.
- 질문자와 답변자가 다른 경우 답변을 삭제할 수 없다.
- 질문과 답변 삭제 이력에 대한 정보를 DeleteHistory를 활용해 남긴다.

## 리팩터링 요구사항

- nextstep.qna.service.QnaService의 deleteQuestion()는 앞의 질문 삭제 기능을 구현한 코드이다. 이 메소드는 단위 테스트하기 어려운 코드와 단위 테스트 가능한 코드가
  섞여 있다.
- QnaService의 deleteQuestion() 메서드에 단위 테스트 가능한 코드(핵심 비지니스 로직)를 도메인 모델 객체에 구현한다.
- QnaService의 비지니스 로직을 도메인 모델로 이동하는 리팩터링을 진행할 때 TDD로 구현한다.
- QnaService의 deleteQuestion() 메서드에 대한 단위 테스트는 src/test/java 폴더 nextstep.qna.service.QnaServiceTest이다. 도메인 모델로 로직을
  이동한 후에도 QnaServiceTest의 모든 테스트는 통과해야 한다.

  
## 구현 목록

- [x] Question 클래스에 remove 메서드를 추가
    - [x] 로그인 사용자와 질문한 사람이 다른 경우 예외를 던진다
    - [x] 질문 삭제 권한 확인 기능
    - [x] 답변이 존재할 경우 삭제 불가 , 단 답변에 질문자가 작성한 글만 있을 경우 삭제 가능 기능
    - [x] DeleteHistory 생성 후 반환 기능
- [x] Answers 클래스에 remove 메서드를 추가
    - [x] 일급 컬렉션으로 구현
    - [x] 답변을 삭제한다.
    - [x] DeleteHistory를 생성하여 반환한다.
- [x] QnAService 클래스 내 deleteQuestion 메서드 비즈니스 로직 코드 삭제
- [x] DeleteHistories 일급 컬렉션을 구현한다.