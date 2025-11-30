# 러너비

## 목차
  * [프로젝트 소개](#프로젝트-소개)
  * [Architecture](#architecture)
  * [담당 화면 및 기능](#담당-화면-및-기능)
  * [트러블 슈팅](#트러블-슈팅)

## 프로젝트 소개
직장인 간 시간대/위치/직군/성별/나이 별로 러닝 모임을 결성하여 러닝을 뛸 수 있도록 돕는 서비스 <br>
   🔗 [앱 다운로드 링크](https://apps.apple.com/kr/app/%EB%9F%AC%EB%84%88%EB%B9%84/id1612604358)

- 팀 구성
   - iOS 개발자 2명, 서버 개발자 1명, 안드로이드 개발자 1명, PM 1명, 디자이너 2명
- 진행 기간
    - 개발 : 2022.05. ~ 2022.10
    - 유지보수 : 2022 ~ 2024.06
- 기술 스택
  -   언어 : Swift
  -   UI: UIKit, 코드 방식(SnapKit)
  -   비동기 처리 : RxSwift, DispatchQueue
  -   네트워킹  : Moya
  -   기타: Firebase Analytics/Crashlytics (유저 및 버그 리포트)
  -   협업: Slack, Figma, Zeplin
    
## Architecture
### MVVM-C 
<img width="700" src="https://github.com/user-attachments/assets/90562df4-e2db-4cf5-ab48-f8e7b02cd262"/> <br/>

- **Common**: 앱 공통적으로 쓰이는 모달과 같은 컴포넌트, 모델, CommonViews / 유용한 도구 모음 Util / Extension, Localization 등
  - 해당 그룹의 컴포넌트나 Common View, Extension, Util은 앱 공통적으로 사용하거나 자주 사용하는 코드 및 UI를 모듈화함으로써 코드 중복을 줄이고 재사용성을 높임 
- **Base**: 앱의 ViewController, ViewModel, Coordinator와 APIService에 공통적으로 사용되는 속성을 정의하며, Feature의 각 요소는 해당 Base 클래스를 상속함으로써 코드 재사용성을 높임
- **Feature**: 회원가입 시 초기 진입하는 Onboarding 화면 / 탭바 기준으로 Home, BookMark, Message, MyPage로 분리
  - 각 Feature는 ViewController, ViewModel, Component, Coordinator를 가짐
  - 기존 MVVM 패턴에 Clean Architecture를 도입하여 Layer를 분리하고, ViewModel을 Solid의 SRP(단일 책임 원칙)에 맞게 역할을 분리함으로써 유지보수를 용이하게 함
 
  **[Presentation Layer]**
  - **Component**: 화면 별로 필요한 ViewController, ViewModel을 소유하며 화면 전환 가능성이 있는 Component를 생성 및 전달
  - **Coordinator**: ViewController의 화면 전환 및 전환에 따른 추가 작업 로직 분리, 화면 계층 관리
  - **ViewModel**: Moya 기반의 APIService에서 통신한 내용과 UserDefaults의 localDB에 의존하여 데이터를 획득하며, View는 ViewModel을 바인딩함 (RxSwift, RxCocoa)
    - **Input/Output Modeling**: ViewModel의 Nested Type으로 Input 및 Output 구조체를 추가하여 View에서 ViewModel로 입력이 들어오는 부분은 Input, ViewModel에서 View로 출력되는 부분은 Output으로 분리하여 코드 가독성을 높임

  **[Data Layer]**
  - **APIService, KeyChainService**: 직접적으로 Entity를 사용하여 네트워크 통신, KeyChain 값 얻어오기 등을 통해 데이터를 UseCase에 제공하는 역할 수행

## 담당 화면 및 기능
**1) 회원가입**
- 직장인이 타겟이므로, 성인 여부와 직군 정보 등을 회원가입 시 입력받습니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/431af36e-6e60-4f5d-98a7-73f3b92765dc" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/844b0a14-b067-4497-ac74-0b95d87d4f2d" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/a686dc8d-63e5-4050-83b5-852b024576b1" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/970566ee-e63b-45b1-ae45-8e305dbcbb7d" />

**2) 러닝톡**
- 러닝 모임 참여자 간 실시간으로 소통할 수 있는 댓글 서비스입니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/c87588f3-58f4-4410-818b-85d210a1e8c0" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/4a932f5d-810d-47c2-ad35-afc85a2d43cb" />
 
**3) 마이페이지, 러닝 모임 관리 및 현황 확인**
- 러닝 모임 주최자는 러닝 참여자의 출석을 관리할 수 있으며, 참여자는 출석 여부를 확인할 수 있습니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/e6f6f73b-1a6e-48c1-9a58-b17cf467c263" />
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/7a2e641e-27c1-41de-a70a-9fe8ae9077b1" /><img src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/0d1cc772-5726-44ad-98bd-b0da21084bc4" width="200" />

## 트러블 슈팅
**1. MVVM 구조에서 ViewModel 책임 분산 및 Clean Architecture 적용**
- **문제** <br/>
[리팩토링 전 아키텍처] <br/>
<img width="700" src="https://github.com/user-attachments/assets/90562df4-e2db-4cf5-ab48-f8e7b02cd262"/> <br/>
ViewModel이 네트워크 요청, Keychain 접근, 비즈니스 로직, UI 바인딩 등 모든 처리를 담당해
코드가 복잡하고, 유지보수가 어려운 문제가 있었습니다.

- **해결 과정** <br/>
[리팩토링 후 아키텍처] <br/>
<img width="700"  alt="image" src="https://github.com/user-attachments/assets/221fb418-35f3-4c06-ab65-2ff80d50f9d2" /> <br/>
  - **목표:** ViewModel의 책임은 오직 UI 상태 관리에 집중시키고, 
비즈니스 로직과 데이터 접근 로직을 분리하여 유지보수성을 높이는 것이 목표였습니다.
    - 선택 이유 <br/>
기존 MVVM 구조를 크게 변경하지 않으면서도 의존성 분리를 적용하고 싶었기 때문에, 구조 변경 부담이 적은 Clean Architecture를 선택하여 개선했습니다.
  - **구현 과정:**
    1. 계층 분리: Presentation Layer(View/ViewModel), Domain Layer(UseCase, Entity), Data Layer(Repository, Service)로 계층을 명확히 분리했습니다.
    2. UseCase 도입: ViewModel이 직접 데이터에 접근하는 대신, UseCase를 통해 비즈니스 로직을 처리하도록 구조를 개선했습니다.
    3. 의존성 역전 적용: UseCase는 구체적인 Repository 대신 추상화된 Repository Protocol에 의존하도록 설계했습니다. 이를 통해 실제 데이터 제공자(API, Firebase, Keychain 등)가 변경되더라도 UseCase의 로직을 수정할 필요가 없게 되어 의존성을 분리했습니다.
    4. ViewModel 리팩토링: ViewModel 내부에서도 extension을 활용하여 UseCase 호출과 UI 바인딩 로직을 분리함으로써, ViewModel이 UI 상태 관리에만 집중할 수 있도록 했습니다.

- **결과** <br/>
리팩토링 이후 ViewModel의 책임이 분리됨으로써, 전체적인 코드 가독성과 유지보수성이 향상되었습니다.

**2. RxSwift 중복 이벤트 발생 문제**
- **문제** <br/>
UICollectionView cell의 버튼을 누르면 간헐적으로 화면 전환이 여러번 발생하는 문제가 있었습니다.

- **해결 과정** <br/>
  - 버튼 클릭 이벤트는 RxSwift를 통해 바인딩되어 있었고, 구독 해제 시점을 ViewController의 DisposeBag에 의존하고 있었습니다.
  - 하지만 UICollectionView 셀이 재사용되는 특성상, 셀이 사라져도 이전 구독이 유지되어 이전 셀의 버튼 이벤트가 남아있는 상태에서 중복 이벤트가 발생하는 것이 원인이었습니다. <br/>
<img width="700" alt="image" src="https://github.com/user-attachments/assets/5f2852bd-3432-4f5b-9c65-1cb3d19c2278" /> <br/>
  - 셀 내부에 DisposeBag을 생성하고, prepareForReuse()에서 해당 DisposeBag을 초기화하여 재사용 이전에 구독이 해제되도록 수정했습니다.
  - 추가로, 버튼 클릭 이벤트의 구독 역시 ViewController가 아닌, 셀 내부 DisposeBag에 바인딩되도록 변경하여 중복 이벤트 문제를 해결했습니다.

 **3. UIScrollView에 View, UICollectionView를 넣었을 때 리스트가 끝까지 스크롤되지 않는 문제**
- **문제** <br/>
UIScrollView에 View, UICollectionView를 넣었을 때 UICollectionView가 탭바 밑으로 보이지 않고, 스크롤뷰 전체가 하단으로 스크롤되지 않는 이슈가 있었습니다.

- **원인** <br/>
  - https://developer.apple.com/documentation/uikit/uiscrollview
  - 공식문서에 따르면, 스크롤뷰는 컨텐츠뷰의 크기에 맞춰서 스크롤되기 때문에 컨텐츠사이즈가 명확해야한다고 나와있습니다.
  - 이를 토대로 원인을 살펴보면
    - ‘작성한 글’과 ‘참여 러닝’ 컬렉션뷰는 API와 통신 성공함에 따라, 그리고 API 통신한 내용에 따라 contentSize가 유동적이므로,
    - UIScrollView가 스크롤하는 contentSize가 명확히 정해져있지 않아 스크롤이 되지 않았던 것으로 짐작되었습니다.
   
- **해결 과정** <br/>
  - 처음 마이페이지를 클릭했을 때 ‘작성한 글’을 불러오므로, ‘작성한 글’을 API에서 불러오는 시점에
  UICollectionView의 높이를 ‘작성한 글’의 UICollectionView의 contentSize의 임의의 근사값 높이로 세팅한 다음, <br/>
<img width="372" height="234" alt="image" src="https://github.com/user-attachments/assets/462c0ea7-961d-4b4f-8d44-5485601ddfd1" /> <br/>
  - ‘작성한 글’과 ‘참여 러닝’ 버튼을 클릭하여 API로부터 내용이 load 될 때마다 각 컬렉션뷰의 높이를 자기자신의 contentSize로 업데이트하여 해결했습니다. <br/>
  <img width="515" height="156" alt="image" src="https://github.com/user-attachments/assets/f48a0876-253b-4f09-b74b-ddee24b74acc" />

