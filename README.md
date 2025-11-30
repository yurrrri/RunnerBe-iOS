# 러너비

## 목차
  * [프로젝트 소개](#프로젝트-소개)
  * [Architecture](#architecture)
  * [담당 화면 및 기능](#담당-화면-및-기능)
  * [트러블 슈팅](#트러블-슈팅)

## 프로젝트 소개

직장인 간 시간대/위치/직군/성별/나이 별로 러닝 모임을 결성하여


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
<img width="800" src="https://github.com/user-attachments/assets/90562df4-e2db-4cf5-ab48-f8e7b02cd262"/> <br/>

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
- 직장인이 타겟이므로, 성인 여부와 직군 정보를 회원가입 시 입력받습니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/431af36e-6e60-4f5d-98a7-73f3b92765dc" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/970566ee-e63b-45b1-ae45-8e305dbcbb7d" />

**2) 러닝 모임 개설 및 신청**
- 성별 / 나이대 / 위치 / 직군 별로 모임 개설 및 필터를 통한 신청이 가능합니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/11be9c33-056e-4909-bc84-d1caebb8627d" /> <img width="200" src="https://github.com/user-attachments/assets/040ed8be-7ad5-4078-b568-55718471d7f3" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/e444fdf4-0dac-442e-b2c3-9a41972fd453" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/6852b95e-68c0-4b9d-9038-cd9186ff42dc" /> <img width="200" src="https://github.com/user-attachments/assets/aacbc997-33a5-4787-9c82-e5675cdff166" />

**3) 러닝톡**
- 러닝 모임 참여자 간 실시간으로 소통할 수 있는 댓글 서비스입니다. <br/>
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/c87588f3-58f4-4410-818b-85d210a1e8c0" /> <img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/4a932f5d-810d-47c2-ad35-afc85a2d43cb" />
 
**4) 러닝 모임 관리 및 현황 확인**
- 러닝 모임 주최자는 러닝 참여자의 출석을 관리할 수 있으며, 참여자는 출석 여부를 확인할 수 있습니다. <br/>
<img width="200" src="https://github.com/user-attachments/assets/eb7f4f1c-e858-4616-bb9c-909d4551d704" />
<img width="200" src="https://github.com/user-attachments/assets/e052a4e5-fdee-448a-9b55-4ab28fed3c86" /> 
<img width="200" src="https://github.com/runner-be/RunnerBe-iOS/assets/37764504/7a2e641e-27c1-41de-a70a-9fe8ae9077b1" /> 

**5) 러닝 로그**
- 달린 후 일기와 같은 주관적, 객관적 정보를 남기는 로그를 작성하고, 모임 구성원들과 스탬프를 주고받을 수 있습니다. <br/>
<img width="200" src="https://github.com/user-attachments/assets/b9278544-f8b8-4b96-ab50-30b61baf7533" />
<img width="200" src="https://github.com/user-attachments/assets/a62b45cf-7532-438f-aae6-bca2d438c33c" />
<img width="200" src="https://github.com/user-attachments/assets/9d1cc98a-7eb1-4d89-a08f-dd5d1f87328b" />

## 트러블 슈팅
