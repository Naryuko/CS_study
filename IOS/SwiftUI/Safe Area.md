## 1. SwiftUI의 safe area
- SwiftUI view는 기본적으로 safe area 안쪽에 생성된다.

<img width="1104" alt="image" src="https://user-images.githubusercontent.com/55742167/160227426-2c64dc38-fdb1-45fa-9204-cde4b652b601.png">

## 2. ignoreSafeArea
- ignoreSafeArea를 사용하면 safe area를 무시하고 view를 그릴 수 있다.

<img width="1120" alt="image" src="https://user-images.githubusercontent.com/55742167/160227455-50648748-56a1-4e28-8fb8-700c90d4d961.png">

```swift
func ignoresSafeArea(_ regions: SafeAreaRegions = .all, edges: Edge.Set = .all) -> some View
```

- regions: 무시되어야 하는 safe area
  - container: device와 container에 의해 정해지는 safe area, 우리가 보통 생각하는 safe area다
  - keyboard: software keyboard에 의해 가려지는 부분을 알려주는 safe area
  - all: 위 두 가지를 모두 포함
- edges: 무시되어야 할 safe area 방향을 알려줌, 
  - all, bottom, horizontal, leading, top[, trailing, vertical이 존재
  - 기본값은 all

## 3, safeAreaInset
- view의 원래 safe area 영역을 기준으로 다른 view를 배치할 수 있도록 해 주는 view modifier

```swift
func safeAreaInset<V>(edge: VerticalEdge, alignment: HorizontalAlignment = .center, spacing: CGFloat? = nil, content: () -> V) -> some View where V : View

func safeAreaInset<V>(edge: HorizontalEdge, alignment: VerticalAlignment = .center, spacing: CGFloat? = nil, content: () -> V) -> some View where V : View
```

- edge: 해당 view의 safe area 중 어떤 곳을 기준으로 view를 추가할 것인지 알려줌
- alignment: 추가할 view의 정렬 방식, bottom / top / leading / trailing / center 등이 존재
- spacing: 추가할 view와 원래 view의 거리, 기본적으로 0이며 값을 추가하면 원래의 view가 줄어드는 방향으로 두 view가 멀어지게 된다.
- content: 추가할 view

<img width="1109" alt="image" src="https://user-images.githubusercontent.com/55742167/160228099-982f36ce-36f2-40c2-9f50-b9ddbf7048ef.png">

<img width="1117" alt="image" src="https://user-images.githubusercontent.com/55742167/160228106-5acd48e4-a553-4281-af89-a5f7b7440dcf.png">

## 참고
- [Managing safe area in SwiftUI](https://swiftwithmajid.com/2021/11/03/managing-safe-area-in-swiftui/)
