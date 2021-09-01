# TableView 만들기
- cmd+shift+L 로 table view, cell을 만든다.
- table view -> attributes inspector에서 content를 dynamic propotypes으로 한다면 cell을 재사용, static cells로 한다면 고정된 개수의 cell을 사용한다.
  - tableView.dequeReusableCell(withIdentifier:) 함수에서 재사용할 cell을 꺼낼 수 있다.
- table view cell의 identifier를 설정하면 table view의 cell을 식별할 수 있다

# Data Source 연결하기
- table view에 들어갈 데이터를 요청
- section의 개수, cell의 개수, cell에서 어떤 정보를 보여줄지 알려준다.
- data source는 주로 뷰 컨트롤러로 지정한다. (View controller가 UITableViewDataSource를 상속받아 구현한다)

```swift
class ViewController: UIViewController, UITableViewDataSource {
    @IBOutlet weak var tableView: UITableView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        tableView.dataSource = self
        // Do any additional setup after loading the view.
    }

    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return 3
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell1", for: indexPath)
        cell.textLabel?.text = "text"
        return cell
    }


}
```

- IndexPath는 cell의 위치를 나타내는 변수로, indexPath.section과 indexPath.row를 통해 cell의 위치를 알려준다.
       
# Delegate 연결하기
- cell의 높이나 눌렀을 때 동작, section header와 footer의 모양 등을 설정
- delegate는 주로 뷰 컨트롤러로 지정한다. (View controller가 UITableViewDelegate를 상속받아 구현한다.)
- override viewDidLoad에서 마찬가지로 tableView.delegate = self를 통해 delegate를 연결시켜 주어야 한다.

```swift
extension ViewController: UITableViewDelegate {
    // table view cell이 선택되었을때 할 행동을 정함
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        navigationController?.pushViewController(ViewController, animated: true)
    }
    
    // section의 제목과 형태를 결정
    func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
        return "(\(section)번 섹션"
    }
    
    func tableView(_ tableView: UITableView, viewForHeaderInSection section: Int) -> UIView? {
        <#code#>
    }
    
    // cell 높이 설정
    // 셀마다 호출되기 때문에 성능상 좋지 않다.
    func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
        <#code#>
    }
    
    func tableView(_ tableView: UITableView, estimatedHeightForRowAt indexPath: IndexPath) -> CGFloat {
        <#code#>
    }
}
```

- tableView.rowHeight의 값을 조정하는 것으로도 cell의 높이를 바꿀 수 있다. 위의 함수를 통해 설정하는 것보다 성능상 이점이 있다.

# custom cell 만들기
- 원하는 cell class를 만들고 storyboard에서 cell의 identity inspector에서 class를 커스텀 셀의 클래스로 변경해 준다.
- label, image view 등 cell 안의 요소들을 커스텀 셀 클래스와 연결해 준다.
- 해당 셀을 재사용할 때에는 아래와 같이 커스텀 셀로 타입캐스팅을 해준 후 사용하면 된다.

```swift
let cell = tableView.dequeueReusableCell(withIdentifier: "cell") as? CustomCell
```


