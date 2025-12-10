# 러너비

## 목차
  * [프로젝트 소개](#프로젝트-소개)
  * [Architecture](#architecture)
  * [Experience](#experience)
  * [Trouble Shooting](#trouble-shooting)
  * [주요 화면 설명](#주요-화면-설명)

## 프로젝트 소개
<img width="700" alt="ios6 7" src="https://github.com/user-attachments/assets/6348cf97-d555-4feb-8ed5-5ba3aca03c43" />

직장인 간 시간대/위치/직군/성별/나이 별로 러닝 모임을 결성하여 러닝을 뛸 수 있도록 돕는 커뮤니티 서비스 <br>
   🔗 [앱 다운로드 링크](https://apps.apple.com/kr/app/%EB%9F%AC%EB%84%88%EB%B9%84/id1612604358)

- 팀 구성
   - iOS 개발자 2명, 서버 개발자 1명, 안드로이드 개발자 1명, PM 1명, 디자이너 2명
- 진행 기간
    - 개발 : 2022.05. ~ 2022.10
    - 추가개발 및 유지보수 : 2022.11 ~ 2024.06
- 기술 스택
  -   언어 : Swift
  -   UI: UIKit, 코드 방식(SnapKit)
  -   비동기 처리 : RxSwift, RxCocoa
  -   네트워킹  : Moya
  -   이미지 처리: Kingfisher
  -   푸시 알림 : Firebase
  -   협업: Slack, Figma, Zeplin
    
## Architecture
### MVVM-C 
<img width="700" src="https://github.com/user-attachments/assets/90562df4-e2db-4cf5-ab48-f8e7b02cd262"/> <br/>

- **Common**: 앱 공통/전역으로 쓰이는 Common View, Model, Service 등
- **Base**: 앱의 ViewController, ViewModel, Coordinator와 APIService에 공통적으로 사용되는 속성을 정의하며, Feature의 각 요소는 해당 Base 클래스를 상속함으로써 코드 재사용성 향상
 
 - **Presentation Layer**
   - **Component**: 화면 별로 필요한 ViewController, ViewModel을 소유하며 화면 전환 가능성이 있는 Component를 생성 및 전달
   - **Coordinator**: ViewController의 화면 전환 및 전환에 따른 추가 작업 로직 분리, 화면 계층 관리
   - **ViewModel**: Moya 기반의 APIService에서 통신한 내용과 UserDefaults의 localDB에 의존하여 데이터를 획득하며, View는 ViewModel을 바인딩함 (RxSwift, RxCocoa)
   - **Input/Output Modeling**: ViewModel의 Nested Type으로 Input 및 Output 구조체를 추가하여 View에서 ViewModel로 입력이 들어오는 부분은 Input, ViewModel에서 View로 출력되는 부분은 Output으로 분리하여 코드 가독성 향상

 - **Data Layer**
   - **APIService, KeyChainService**: 네트워크 통신, KeyChain을 통해 Presentation Layer에 데이터 제공

## Experience
**1. Clean Architecture를 적용하여 MVVM 아키텍처 개선**
- **리팩토링 전 아키텍처**
<img width="700" src="https://github.com/user-attachments/assets/90562df4-e2db-4cf5-ab48-f8e7b02cd262"/> <br/>
ViewModel이 네트워크 요청, Keychain 접근, 비즈니스 로직, UI 바인딩 등 모든 처리를 담당해
코드가 복잡하고, 유지보수가 어려운 문제가 있었습니다.

- **Clean Architecture를 선택한 이유**

1) MVI는 상태 관리 중심, VIPER는 화면 레벨 책임 분리에 초점이 있어 ViewModel의 로직을 분리하는 의도에 적합하지 않다고 판단했습니다.
2) 반면 Clean Architecture는 도메인 로직을 Domain, Data, Presentaiton Layer로 분리해 ViewModel이 화면 상태 관리 역할에만 집중하도록 만들 수 있어, Massive ViewModel 문제를 해결하고자 하는 의도에 적합한 구조라고 판단했습니다.

- **리팩토링 후 아키텍처**
<img width="700"  alt="image" src="https://github.com/user-attachments/assets/221fb418-35f3-4c06-ab65-2ff80d50f9d2" /> <br/>
  - **목표:** ViewModel의 책임은 오직 UI 상태 관리에 집중시키고, 
비즈니스 로직과 데이터 접근 로직을 분리하여 유지보수성을 높이는 것이 목표였습니다.
  - **구현 과정:**
    1. **계층 분리**: Presentation Layer(View/ViewModel), Domain Layer(UseCase, Entity), Data Layer(Repository, Service)로 계층을 명확히 분리했습니다.
    2. **UseCase 도입**: ViewModel이 직접 데이터에 접근하는 대신, UseCase를 통해 비즈니스 로직을 처리하도록 구조를 개선했습니다.
    3. **의존성 역전 적용**: UseCase는 구체적인 Repository 대신 추상화된 Repository Protocol에 의존하도록 설계했습니다. 이를 통해 실제 데이터 제공자(API, Firebase, Keychain 등)가 변경되더라도 UseCase에 영향이 없도록 개선했습니다.
    4. **ViewModel 리팩토링**: ViewModel 내부에서서 UseCase 호출과 UI 바인딩 로직을 분리함으로써, ViewModel이 UI 상태 관리에만 집중할 수 있도록 강화했습니다.

**2. 피드 UI에서, 수많은 게시물들을 효율적으로 로드하기 위해 스크롤마다 일정량의 게시물을 로드하는 로직 구현 경험 (무한 스크롤) <br/>**
**3. 유저 피드백에 기반한 개선사항을 반영, 배포하여 유지보수 경험** <br/>
**4. 코드 기반 UI 개발, 커스텀 collectionview, 푸시 알림, MVVM + RxSwift, RxCocoa, RxDataSource 를 활용한 비동기, 데이터 바인딩 등 개발 경험**


## Trouble Shooting

**1. RxSwift 중복 이벤트 발생 문제 해소**
- **문제** <br/>
UICollectionView cell의 버튼을 누르면 간헐적으로 화면 전환이 여러번 발생하는 문제가 있었습니다.

- **해결 과정** <br/>
  - 버튼 클릭 이벤트는 RxSwift를 통해 바인딩되어 있었고, 구독 해제 시점을 ViewController의 DisposeBag에 의존하고 있었습니다.
  - 하지만 UICollectionView 셀이 재사용되는 특성상, 셀이 사라져도 이전 구독이 유지되어 이전 셀의 버튼 이벤트가 남아있는 상태에서 중복 이벤트가 발생하는 것이 원인이었습니다. <br/>
<img width="700" alt="image" src="https://github.com/user-attachments/assets/5f2852bd-3432-4f5b-9c65-1cb3d19c2278" /> <br/>
  - 셀 내부에 DisposeBag을 생성하고, prepareForReuse()에서 해당 DisposeBag을 초기화하여 재사용 이전에 구독이 해제되도록 수정했습니다.
  - 추가로, 버튼 클릭 이벤트의 구독 역시 ViewController가 아닌, 셀 내부 DisposeBag에 바인딩되도록 변경하여 중복 이벤트 문제를 해결했습니다.

 **2. UIScrollView에 View, UICollectionView를 넣었을 때 리스트가 끝까지 스크롤되지 않는 문제 해소**
- **문제** <br/>
UIScrollView에 View, UICollectionView를 넣었을 때 UICollectionView가 탭바 밑으로 보이지 않고, 스크롤뷰 전체가 하단으로 스크롤되지 않는 이슈가 있었습니다.
   
- **해결 과정** <br/>
  - https://developer.apple.com/documentation/uikit/uiscrollview
  - 공식문서에 따르면, 스크롤뷰는 컨텐츠뷰의 크기에 맞춰서 스크롤되기 때문에 컨텐츠사이즈가 명확해야한다고 나와있습니다.
  - 이를 토대로 원인을 살펴보면
    - ‘작성한 글’과 ‘참여 러닝’ 컬렉션뷰는 API와 통신 성공함에 따라, 그리고 API 통신한 내용에 따라 contentSize가 유동적이므로,
    - UIScrollView가 스크롤하는 contentSize가 명확히 정해져있지 않아 스크롤이 되지 않았던 것으로 짐작되었습니다.
  - 처음 마이페이지를 클릭했을 때 ‘작성한 글’을 불러오므로, ‘작성한 글’을 API에서 불러오는 시점에
  UICollectionView의 높이를 ‘작성한 글’의 UICollectionView의 contentSize의 임의의 근사값 높이로 세팅한 다음, <br/>
  <img width="372" height="234" alt="image" src="https://github.com/user-attachments/assets/462c0ea7-961d-4b4f-8d44-5485601ddfd1" /> <br/>
  - ‘작성한 글’과 ‘참여 러닝’ 버튼을 클릭하여 API로부터 내용이 load 될 때마다 각 컬렉션뷰의 높이를 자기자신의 contentSize로 업데이트하여 해결했습니다. <br/>
  <img width="515" height="156" alt="image" src="https://github.com/user-attachments/assets/f48a0876-253b-4f09-b74b-ddee24b74acc" /> <br/>

## 주요 화면 설명
**1) 회원가입**
- 직장인이 타겟이므로, 성인 여부와 직군 정보를 회원가입 시 입력받습니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/431af36e-6e60-4f5d-98a7-73f3b92765dc" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/844b0a14-b067-4497-ac74-0b95d87d4f2d" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/a686dc8d-63e5-4050-83b5-852b024576b1" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/970566ee-e63b-45b1-ae45-8e305dbcbb7d" />

**2) 러닝 모임 개설 및 신청**
- 성별 / 나이대 / 위치 / 직군 별로 모임 개설 및 필터를 통한 신청이 가능합니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/11be9c33-056e-4909-bc84-d1caebb8627d" /> <img width="200" src="https://github.com/user-attachments/assets/040ed8be-7ad5-4078-b568-55718471d7f3" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/e444fdf4-0dac-442e-b2c3-9a41972fd453" /> <img width="200" src="https://github.com/user-attachments/assets/aacbc997-33a5-4787-9c82-e5675cdff166" />

**3) 러닝톡**
- 러닝 모임 참여자 간 실시간으로 소통할 수 있는 댓글 서비스입니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/c87588f3-58f4-4410-818b-85d210a1e8c0" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/4a932f5d-810d-47c2-ad35-afc85a2d43cb" />
 
**4) 러닝 모임 관리 및 현황 확인**
- 러닝 모임 주최자는 러닝 참여자의 출석을 관리할 수 있으며, 참여자는 출석 여부를 확인할 수 있습니다. <br/>
<img width="200" src="https://github.com/user-attachments/assets/eb7f4f1c-e858-4616-bb9c-909d4551d704" />
<img width="200" src="https://github.com/user-attachments/assets/e052a4e5-fdee-448a-9b55-4ab28fed3c86" /> 
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/7a2e641e-27c1-41de-a70a-9fe8ae9077b1" /> 

**5) 러닝 로그**
- 달린 후 일기와 같은 주관적, 객관적 정보를 남기는 로그를 작성하고, 모임 구성원들과 스탬프를 주고받을 수 있습니다. <br/>
<img width="200" src="https://github.com/user-attachments/assets/b9278544-f8b8-4b96-ab50-30b61baf7533" />
<img width="200" src="https://github.com/user-attachments/assets/a62b45cf-7532-438f-aae6-bca2d438c33c" />
<img width="200" src="https://github.com/user-attachments/assets/9d1cc98a-7eb1-4d89-a08f-dd5d1f87328b" />
