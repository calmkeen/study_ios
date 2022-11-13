# 전달인자 레이블 / guard / Optional Binding

### 전달인자 레이블

```swift
func exampleMethod(from labelA: string, to labelB: string) {
//여기서 from과 to가 전달인자 레이블인데
//사용이유는 함수 외부에서 매개변수의 역활을 정확히 정의해줄수 있기 때문에 사용한다.
// 그런이유로 내생각엔 사실 대부분의 경우는 메서드명과 메서드를 설명하는 주석으로 해결이 되서 생략하는것 같다.
return labelB + labelA
}

// 가변매개변수
func exampleMethod(from labelA: string, to labelB: string...) {
//가변매개변수란 labelB의 타입옆에 ... 이 가변메게 변수인데 이렇게 되면 나중에
return labelB + labelA
}
//이렇게 사용가능하다. 배열로 넘거받음
exampleMethod(labelA:"test",labelB: "test1","test2","test3")

```

### guard문

```swift
옵셔널 바인딩에서 쓸수 있는데
//아래방식으로 하면 if문 밖에서는 사용할 수 없다.
let a: Int? = 3
if let b  = a {
	print(b)
else {}

//개선안
//조건이 투루일때만 통과하고 아니면 else 실행
func test {
let a: Int? = 3
guard let b = a else {return}

```

### 옵셔널 바인딩을 해제하는 방법

```swift
//실제 값과 비교하면 된다.
//이러면 컴파일러가 자동으로 해제시켜줌
let a: Int? = 6
if a == 6 {return} else {return}
//묵시적해제
//아래의 경우 자동적으로 해제
var stringToInt: Int! = Int(String)
stringToInt + 1
```

### Protocol

```swift
class에서 프로토콜을 준수할시에는
class test : 상속받는 클래스, protocol1, protocol2 {}
이 순번을 지켜야하고
만약 protocol이
protocol testProtocol {
init()
}
이런식이라면 무조건
class testclasss: testProtocol { 
require init(){} }
이런식의 필수 이니셜라이저가 필요하다
```
