[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2FKyungminLeeDev%2FiOS_Web_Browser&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

# 웹 브라우저
- Webkit으로 구현한 웹 브라우저 앱
- 팀 프로젝트: `Jacob`, [`Lina`](https://github.com/lina0322), [`팀 그라운드 룰`](./GroundRule.md)
- 진행 기간: 2020.11.09~15 (1주)
- 학습 Keyword: `WebKit View` `Toolbar` `Button` `Text Field` `Alert`

## Index
- [기능](#기능)
- [구현 내용](#구현-내용)
- [미구현 내용](#미구현-내용)
- [학습 내용](#배운-내용)

## 기능

| 입력한 주소로 이동 | 잘못된 주소는 Alert 표시 | 주소에 "https://" 붙이기 | 앞/뒤로 가기, 새로고침 | 
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

## 미구현 내용

- 뒤로가기, 앞으로가기, 새로고침 기능을 Webkit 메서드를 사용하지 않고 구현해보기

## 배운 내용

## 정리할 내용

- [무분별한 얼럿 사용은 사용자 경험을 해칠 수 있다](https://github.com/yagom-academy/ios-web-browser/pull/20#discussion_r521734482)
- 앞으로 가기, 뒤로 가기 버튼 관련

