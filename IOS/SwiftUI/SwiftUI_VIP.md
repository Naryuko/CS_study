## 1. UIKit에서의 VIP

<img width="695" alt="vip" src="https://user-images.githubusercontent.com/55742167/158515980-fb03bd96-427b-4847-b703-6d695c9dd60e.png">


# 2. UIKit과 거의 비슷한 VIP

<img width="1046" alt="스크린샷 2022-03-16 오후 1 27 52" src="https://user-images.githubusercontent.com/55742167/158516861-48f531ff-1fee-4170-8a4c-993d1df63212.png">

- UIKit에서 사용하던 것과 거의 비슷한 VIP 패턴
  - DisplayLogic, BusinessLogic, PresentLogic 프로토콜을 생성
  - view 생성시 interactor, presenter 등을 configure
  - 터치나 제스쳐, onAppear 등을 통해 interactor의 함수 호출
- ViewController가 사라짐
- DataStore가 ObservedObject가 되며 view에 존재

### example

- view 생성시 view, interactor, presenter setup

```Swift
@main
struct VIP_1App: App {
    var body: some Scene {
        WindowGroup {
            SomeView()
                .configureView()
        }
    }
}

extension SomeView {
    func configureView() -> some View {
        var view = self
        let interactor = SomeInteractor()
        let presenter = SomePresenter()
        view.interactor = interactor
        interactor.presenter = presenter
        presenter.view = view

        return view
    }
}
```

- 버튼을 눌렀을 때 VIP cycle을 돌며 로직 처리

```swift
// view
struct SomeView: View {
    var interactor: SomeInteractor?
    @ObservedObject var dataStore = SomeDataStore()

    var body: some View {
        VStack {
            Spacer()
            Text("\(dataStore.someInt)").font(.largeTitle)
            Spacer()
            Button("add 1") {
                interactor?.doSomething(request: .init())
            }
            Spacer()
        }
    }
}

// interactor
class SomeInteractor: SomeBusinessLogic {
    var presenter: SomePresentLogic?

    func doSomething(request: Something.something.Request) {
        presenter?.presentDoSomething(response: .init())
    }
}

// presenter
class SomePresenter: SomePresentLogic {
    var view: SomeDisplayLogic?

    func presentDoSomething(response: Something.something.Response) {
        view?.displayDoSomething(viewModel: .init())
    }
}

// view
extension SomeView: SomeDisplayLogic {
    func displayDoSomething(viewModel: Something.something.ViewModel) {
        dataStore.someInt += 1
    }
}
```

### 참고
- [raywenderlich Getting Started With the VIP Clean Architecture Pattern](https://www.raywenderlich.com/29416318-getting-started-with-the-vip-clean-architecture-pattern#toc-anchor-008)


## 3. 색다른 VIP

<img width="790" alt="스크린샷 2022-03-16 오후 1 37 52" src="https://user-images.githubusercontent.com/55742167/158517680-1f35512f-aeef-4ed7-a832-e27c065b38fc.png">


### AppState
- presenter가 없어진 대신 ObservableObject인 AppState 존재
- 유일한 object(class)
- 앱의 state를 유지하며 다른 layer에 대해 일체 아는 것이 없고 어떠한 비즈니스 로직도 포함하지 않음
 
### View
- 상태를 가지지 않거나 @State를 가질 수 있음
- 다른 layer들은 view를 알 수 없으므로 프로토콜을 이용해 view를 숨길 필요가 없다.

### Interactor
- 비즈니스 로직 담당
- state를 가지지 않으며 오직 AppState object만 참조
- 데이터를 가져오거나 계산 등을 한 결과를 직접 반환하지 않는다. 대신 AppState에 전달하거나 View에서 전달받은 Binding에 전달한다.
  - Binding은 작업 결과가 하나의 View에서만 내부적으로 쓰이고 전체적 AppState에 속하지 않는 경우 사용한다. 앱 내에서 유지할 필요가 없거나 다른 화면과 공유할 필요가 없을 때 등..

### example

- AppState는 view나 interactor 등을 전혀 알지 못한다.

```swift
class AppState: ObservableObject {
    @Published var someInt = 1
}
```

- 아래와 같이 Environment를 사용해 interactor를 configure할 수도 있다.

```swift
// interactor
struct SomeInteractor: EnvironmentKey {
    var appState: AppState?

    static var defaultValue: SomeInteractor = SomeInteractor()

    func doSomething() {
        appState?.someInt += 1
    }
}

extension EnvironmentValues {
    var interactor: SomeInteractor {
        get {
            self[SomeInteractor.self]
        }

        set {
            self[SomeInteractor.self] = newValue
        }
    }
}

// main
@main
struct VIP_2App: App {
    var body: some Scene {
        let appState = AppState()

        WindowGroup {
            SomeView()
                .environment(\.interactor, SomeInteractor(appState: appState))
                .environmentObject(appState)
        }
    }
}
```


- 버튼을 눌렀을 때 VIP 사이클을 돌며 로직 처리

```swift
// view
struct SomeView: View {
    @EnvironmentObject var appState: AppState
    @Environment(\.interactor) var interactor: SomeInteractor

    var body: some View {
        VStack {
            Spacer()
            Text("\(appState.someInt)").font(.largeTitle)
            Spacer()
            Button("add 1") {
                interactor.doSomething()
            }
            Spacer()
        }
    }
}

// interactor
struct SomeInteractor: EnvironmentKey {
    var appState: AppState?

    static var defaultValue: SomeInteractor = SomeInteractor()

    func doSomething() {
        appState?.someInt += 1
    }
}
```

- 이와 같은 경우 View가 AppState을 구독하고 있기 때문에 PresentLogic, DisplayLogic이 필요없다.
