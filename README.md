[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2FKyungminLeeDev%2FiOS_Web_Browser&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

# 웹 브라우저
- Webkit으로 구현한 웹 브라우저 앱
- 팀 프로젝트: `Jacob`, [`Lina`](https://github.com/lina0322), [`팀 그라운드 룰`](./GroundRule.md)
- 진행 기간: 2020.11.09~15 (1주)
- 학습 Keyword: `WebKit View` `Toolbar` `Button` `Text Field` `Alert`

## Index
- [기능](#기능)
- [구현 내용](#구현-내용)
- [배운 내용](#배운-내용)
- [고민한 내용](#고민한-내용)
- [개선하고 싶은 내용](#개선하고-싶은-내용)

## 기능

| 입력한 주소로 이동 | 잘못된 주소는 Alert 표시 | 주소에 "https://" 붙이기 | 앞/뒤로 가기, 새로 고침 | 
| - | - | - | - |
| ![](./Images/MoveToURL.gif) | ![](./Images/WrongURL.gif) | ![](./Images/AutoURL.gif) | ![](./Images/ForwardBackReload.gif) |


## 구현 내용

- 화면 구성
    - 주소 입력 필드
    - 웹 뷰
    - 주소입력 필드
    - 툴 바
        - 바 버튼 아이템
- 앞으로 가기, 뒤로 가기 버튼
- Alert 표시
- 정규식으로 주소에 https 포함했는지 검사
- 주소 입력 필드에 현재 URL 표시 (델리게이트)
- 주소로 이동 실패한 경우 에러 표시 (델리게이트)


## 배운 내용

- 메서드의 확장성 고려하기
- [무분별한 얼럿 사용은 사용자 경험을 해칠 수 있다 Step2 PR](https://github.com/yagom-academy/ios-web-browser/pull/20#discussion_r521734482)

## 고민한 내용

- [메서드의 재사용성](#메서드의-재사용성)
- 메서드의 확장성 관련 (Step1 PR)
- 메서드 정의 순서 (Step4 PR)
- 웹 페이지 로딩중에 다른 주소를 입력하면 뒤에요 요청된 URL 무시 (Step4 PR)

### 메서드의 재사용성

`입력한 URL로 Web View를 이동하는 메서드`를 `ViewController`에 정의했다. 이 메서드를 만약 다른 `ViewController`에서 사용하려면 메서드를 복붙해야 하는데 더 나은 방법은 없을까?
- `UIViewController`를 extension 해서 메서드를 정의하면 모든 `ViewController`에서 사용할 수 있겠네?
    - 하지만 모든 `ViewController`가 `Web View`와 `입력한 URL로 Web View를 이동하는 메서드`가 필요한 건 아니다
- `WKWebView`를 extesnsion 해서 메서드를 정의하면 `WKWebView`를 사용하는 곳에서 모두 이 기능을 사용할 수 있겠네?
    - `WKWebView`를 사용하는 모든 곳에서 이 기능이 필요할까? 그렇다면 애플에서 이미 구현 해놨을 것이다
- `입력한 URL로 Web View를 이동하는 메서드`와 `WKWebView.load(_:)`의 차이는 URL이 무효일 경우 에러 표시를 해주는 것뿐이다
- 결론
    - 프로젝트의 `ViewController`에서만 사용할 것이므로 이곳에 정의
    - 이 경우 말고, 기능을 재사용할 방법은 extension뿐 아니라, 전용 객체를 만들어 사용하면 될 것이다.



## 개선하고 싶은 내용

- 뒤로 가기, 앞으로 가기, 새로 고침 기능을 Webkit 메서드를 사용하지 않고 구현해보기
- 상/하단의 Bar가 항상 보이고 있어서 웹 뷰 영역이 비좁아 보인다. 사파리 앱처럼 아래로 스크롤 시에 상/하단 Bar를 최소화하고, 위로 스크롤 시에 다시 보이게 하는 기능을 구현 해 보기
- 에러 핸들링 Error 프로토콜 사용하기, 에러 핸들링 분리하기