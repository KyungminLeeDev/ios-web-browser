# 웹 브라우저 앱
<!-- 뱃지 라인 -->
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2FKyungminLeeDev%2FiOS_Web_Browser&count_bg=%2379C83D&title_bg=%23555555&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)

## 목차

1. [개요](#1-개요)
2. [학습 내용](#2-학습-내용)
    - [Text Field](#Text-Field)
        - [텍스트 입력받고 사용하기](#텍스트-입력받고-사용하기)
        - [Keyboard Type 설정하기](#Keyboard-Type-설정하기)
    - [Alert 표시하기](#Alert-표시하기)
    - [Web View](#Web-View)
        - [입력한 주소로 이동하기](#입력한-주소로-이동하기)
        - [앞으로 가기, 뒤로 가기, 새로고침](#앞으로-가기-뒤로-가기-새로고침)
    - [Toolbar or Tab Bar?](#Toolbar-or-Tab-Bar)
    - [String을 URL 타입으로 변환하기](#String을-URL-타입으로-변환하기)
    - [정규식으로 주소에 https 포함했는지 검사](#정규식으로-주소에-https-포함됐는지-검사)
    - 주소로 이동 실패한 경우 에러 표시 (델리게이트)
3. [배운 내용](#배운-내용)
    - [메서드의 확장성 고려하기](#메서드의-확장성-고려하기)
    - [Alert은 꼭 필요할 때 사용하기](#Alert은-꼭-필요할-때-사용하기)
4. [고민한 내용](#고민한-내용)
    - [메서드의 재사용성](#메서드의-재사용성)
    - 메서드의 확장성 관련 (Step1 PR)
    - 메서드 정의 순서 (Step4 PR)
    - 웹 페이지 로딩중에 다른 주소를 입력하면 뒤에요 요청된 URL 무시 (Step4 PR)
5. [개선하고 싶은 내용](#개선하고-싶은-내용)

<br><br><br>

## 1. 개요

- Webkit으로 구현한 웹 브라우저 앱
- 팀 프로젝트: `Jacob`, [`Lina`](https://github.com/lina0322), [`팀 그라운드 룰`](./GroundRule.md), [`TWL`](./TWL.md)
- 진행 기간: 2020.11.09~15 (1주)
- 학습 Keyword: `Web View` `Toolbar` `Button` `Text Field` `Alert`
- 기능

| 입력한 주소로 이동 | 잘못된 주소는 Alert 표시 | 주소에 "https://" 붙이기 | 앞/뒤로 가기, 새로 고침 | 
| :----------------: | :----------------------: | :----------------------: | :---------------------: |
| ![](./Images/MoveToURL.gif) | ![](./Images/WrongURL.gif) | ![](./Images/AutoURL.gif) | ![](./Images/ForwardBackReload.gif) |

<br>

[👆목차로 가기](#목차)
<br><br><br>

## 2. 학습 내용

### Text Field

#### 텍스트 입력받고 사용하기

1. `ViewController`에 `@IBOutlet` 프로퍼티로 연결.
    ~~~swift
    class ViewController: UIViewController {
        @IBOutlet weak var urlTextField: UITextField!
    }
    ~~~
2. 이제 `Text Field`를 터치하면 글자를 입력할 수 있다.
3. 입력받은 글자는 `Text Field`의 `text`프로퍼티로 접근하여 가져올 수 있다. `Optional`이므로 안전하게 `Optional Unwrapping`하여 사용하자.
    ~~~swift
    if let urlText = urlTextField.text {
        // use urlText
    }
    ~~~

#### Keyboard Type 설정하기

입력받을 콘텐츠에 맞는 Keyboard Type을 설정하면 사용자가 더 편리해할 것이므로 꼭 하자.  
(핸드폰 번호를 입력하는 Text Field라면 문자는 불필요하므로 숫자패드가 보이면 더 누르기 쉬울 것이다.)
![](./Images/TextField_KeyboardType.png)
- [H.I.G - Text Fields 읽어보기](https://developer.apple.com/design/human-interface-guidelines/ios/controls/text-fields/)
- 스토리보드에서 Text Field 선택 후 Attributes Inspector -> Text Input Traits -> Keyboard Type에서 설정
- 웹 주소를 입력받으므로 `URL` Type으로 설정하니, 키보드의 `.`, `/`, `.com`등 URL 작성에 유용하게 배치됐다.

### Alert 표시하기

~~~swift
func showError(error: ErrorMessage) {
    // 1. Alert Controller 생성
    let errorAlert = UIAlertController(title: "Error!", message: error.rawValue, preferredStyle: .alert)
    // 2. Action 버튼 생성
    let ok = UIAlertAction(title: "확인", style: .default, handler: nil)
    // 3. Alert Controller에 Action 추가
    errorAlert.addAction(ok)

    // 4. Alert 띄우기
    self.present(errorAlert, animated: false)
}
~~~
- [Getting the User's Attention with Alerts and Action Sheets 읽어보기](https://developer.apple.com/documentation/uikit/windows_and_screens/getting_the_user_s_attention_with_alerts_and_action_sheets)
- UIAlertController.Style
    - actionSheet: 화면 아래에서 슬라이드 되며 올라온다. 2개 이상의 Action을 사용할 때 주로 사용
    - alert: 화면 가운데에 표시. 2개의 Action을 사용할 때 주로 사용
- UIAlertAction.Style
    - default
    - cancel: 취소 Action에 사용. default보다 글자가 굵다
    - destructive: 데이터의 수정/삭제 Action에 사용. 글자 빨간색으로 표시

### Web View

#### 입력한 주소로 이동하기

1. Web View로 웹 페이지 불러오기

    입력한 주소를 String -> URL -> URLRequest 순서로 변환하여 WKWebView `load(_:)` 메서드의 인자로 넣어 호출한다.
    ~~~swift
    func openPage(url: String) {
        // 1. URL 객체 생성
        guard let url = URL(string: url) else {
            return
        }
        // 2. URLRequest 객체 생성
        let request = URLRequest(url: url)
        // 3. Web View로 웹 페이지 불러오기
        webView.load(request)
    }
    ~~~

2. 현재 웹 페이지의 주소 표시하기

    이동이 완료 됐다면, 주소 Text Field에 현재 웹 페이지의 주소를 보여주자. 
    ~~~swift
    func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        urlTextField.text = webView.url?.absoluteString
    }
    ~~~
    - WKNavigationDelegate `webView(_:didFinish:)` 메서드는 Web View의 이동이 완료되면 호출된다.

#### 앞으로 가기, 뒤로 가기, 새로고침

1. 앞으로/뒤로 가기, 새로고침 기능 구현

    WKWebView 클래스에 goForward(), goBack(), reload() 메서드가 이미 정의돼있으므로, 버튼의 액션 메서드에서 호출해 준다.
    ~~~swift
    @IBAction func goForwardPage(_ sender: UIBarButtonItem) {
        webView.goForward()
    }

    @IBAction func goBackPage(_ sender: UIBarButtonItem) {
        webView.goBack()
    }

    @IBAction func reloadPage(_ sender: UIBarButtonItem) {
        webView.reload()
    }
    ~~~

2. 앞으로/뒤로 갈 수 있을 때만 버튼 활성화하기

    만약 열어본 웹 페이지가 하나 뿐이라면, 앞/뒤 페이지가 없으므로 해당 버튼을 눌러도 이동하지 않을 것이다.  
    이 때, 사용자는 기능에 문제가 있다고 생각할 수 있으므로 이동할 수 없을 때에는 버튼을 비활성화 해준다.  

    Web View의 이동이 완료될 때마다 WKWebView `canGoForward`/`canGoBack` 프로퍼티로 확인해서 버튼을 활성/비활성 해준다.
    ~~~swift
    func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
        goForwardButton.isEnabled = webView.canGoForward
        goBackButton.isEnabled = webView.canGoBack
    }
    ~~~

### Toolbar or Tab Bar?

화면 하단에 `Bar`를 사용한다면 `Toolbar`와`Tab Bar`의 차이를 잘 이해해야 한다.
- [H.I.G - Toolbars 읽어보기](https://developer.apple.com/design/human-interface-guidelines/ios/bars/toolbars/)
- Toolbar: 현재 화면과 관련된 기능. 즉, `도구` 개념 (Ex: 사진 앱의 공유/하트/삭제 버튼)
- Tab Bar: 앱의 다른 섹션으로 빠르게 전환해 줌 (EX: 시계 앱의 세계 시계/알람/스톱워치/타이머 탭)
- `앞으로/뒤로 가기`, `새로 고침` 버튼은 웹 브라우저의 도구 역할을 하므로 `Toolbar`를 사용했다.

### String을 URL 타입으로 변환하기

1. `URL`의 생성자 `init(string:)`으로 String 텍스트를 URL타입으로 생성한다. 
    ~~~swift
    if let url = URL(string: urlText) {
        // use url
    }
    ~~~
2. 이 생성자는 string이 유효하지 않다면, nil을 반환한다. 이것을 활용하면 입력한 주소의 유효성을 검증할 수 있다.
    > **Developer Document > URL > init(string:)**  
    > This initializer returns nil if the string doesn’t represent a valid URL. For example, an empty string or one containing characters that are illegal in a URL produces nil.

### 정규식으로 주소에 https 포함됐는지 검사

주소를 입력할 때 `https://`을 붙이지 않고 `naver.com`을 입력한다면 URL 타입 생성이 실패한다.  
입력한 주소를 미리 검사해서, 사용자가 `https://`를 입력하지 않았다면 자동으로 붙여주는 기능을 만들었다.  

~~~swift
func checkFront(of url: String?) -> Bool {
    let urlRegex = "((http|https)://)[\\S]+"
    
    return NSPredicate(format: "SELF MATCHES %@", urlRegex).evaluate(with: url)
}
~~~
> 이 기능은 팀원 `Lina`가 구현 후 설명해 주었고, 정규식을 분석하여 정리했다.
- 메타 문자: 정규식에서 일정한 의미를 가지고 쓰는 특수문자
- `http|https`: 메타 문자 `|`는 `or`을 의미하므로 `http 또는 https`를 의미
- `((http|https)://)`: 메타 문자 `()`는 서브 패턴을 지정하므로, `http:// 또는 https://`를 의미
- `[\\S]+`
    - 메타 문자 `\S`는 공백문자를 제외한 문자를 의미하고, Swift에서는 역슬래쉬 하나를 더 붙여서 `\\S`로 사용
    - 메타 문자 `+`는 앞 문자가 1개 이상을 의미하므로, `http:// 또는 https://`가 문자내에 1개 이상 있다는 것을 의미

<br>

[👆목차로 가기](#목차)
<br><br><br>



## 배운 내용

### 메서드의 확장성 고려하기

**메서드를 작성할 때는 기획의 변경 가능성과 여러 가지 상황 등에 대한 확장성을 고려해야 한다.**

즐겨 찾는 웹페이지를 띄우기 위해, 즐겨 찾는 웹페이지 URL을 `Enum`타입으로 묶고, 이를 매개변수로 받아서 해당 URL로 이동하면 되겠다는 생각으로 메서드를 정의했고, 코드리뷰에서 코멘트를 받았다.
~~~swift
    func load(favoriteWebPage: FavoriteWebPage)
~~~
> [코드리뷰 코멘트](https://github.com/yagom-academy/ios-web-browser/pull/8#discussion_r520636239)  
> 즐겨찾는 웹페이지만 불러올 수 있는 메서드라면 차후의 확장성 측면에서 괜찮을까요?  
> 기획서가 즐겨찾는 웹 페이지 뿐만 아니라 직접 입력한 웹 페이지도 불러올 수 있는 기능을 만들어 달라고 한다면 코드의 수정 없이 기능 추가/변경이 원활할 수 있을까요?  
> 정답은 없습니다. 열어두고 생각해봐도 좋을 포인트 인것 같아요  

메서드의 확장성에 대해 고민하여 수정했다.
1. 즐겨 찾는 웹 페이지만 매개변수로 받으므로, 직접 입력한 URL로 이동하는 기능을 위해서는 또 다른 메서드를 만들어야 한다.
2. 비슷한 기능의 메서드가 두 개일 필요가 없고, URL을 매개변수로 받아 이동하는 메서드만 있으면 될 것이다.
~~~swift
func openPage(url: String)
~~~



### Alert은 꼭 필요할 때 사용하기

- 배경
    앞으로/뒤로 가기 버튼을 눌렀을 때, 앞으로/뒤로 갈 수 없다면 Alert으로 에러를 띄우도록 구현했다. 
    사용자가 버튼을 눌렀는데 아무런 반응 없이 기능이 동작하지 않는다면 안 좋을 것이니, Alert으로 에러 내용을 표시하면 되겠다는 생각이었다  
- [코드리뷰 코멘트](https://github.com/yagom-academy/ios-web-browser/pull/20#discussion_r521734482)
    > 사용자에게 알림을 주는 것은 좋지만, 무분별한 얼럿 사용은 오히려 사용자 경험을 해칠 수 있습니다.
    > 적절한 피드백을 고민해봐야합니다.
    > 앞으로 갈 수 없는 상황이라면 앞으로가기 버튼을 비활성화 하는 것만으로도 충분한 피드백이 되지 않을까요?
- 개선
    코멘트의 방식이 적절하겠다고 판단하여,
    웹페이지 이동을 완료할 때마다 앞으로/뒤로 갈 수 있는지 체크하여, 갈 수 있으면 버튼을 활성화하고, 아니면 비활성화했다.
    ~~~swift 
    extension ViewController: WKNavigationDelegate {
        func webView(_ webView: WKWebView, didFinish navigation: WKNavigation!) {
            goForwardButton.isEnabled = webView.canGoForward
            goBackButton.isEnabled = webView.canGoBack
        }
    }
    ~~~
- 배운 점
    - Alert은 꼭 필요할 때만 사용해야 한다.
    - H.I.G 문서는 매우 중요하다! [Feedback - Alert관련 내용](https://developer.apple.com/design/human-interface-guidelines/ios/user-interaction/feedback/)

<br>

[👆목차로 가기](#목차)
<br><br><br>



## 고민한 내용

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

<br>

[👆목차로 가기](#목차)
<br><br><br>



## 개선하고 싶은 내용

- 뒤로 가기, 앞으로 가기, 새로 고침 기능을 Webkit 메서드를 사용하지 않고 구현해보기
- 상/하단의 Bar가 항상 보이고 있어서 웹 뷰 영역이 비좁아 보인다. 사파리 앱처럼 아래로 스크롤 시에 상/하단 Bar를 최소화하고, 위로 스크롤 시에 다시 보이게 하는 기능을 구현 해 보기
- 에러 핸들링 Error 프로토콜 사용하기, 에러 핸들링 분리하기
- 다크모드 대응

<br>

[👆목차로 가기](#목차)
<br><br><br>