# CHAPTER.01

프로그램이 잘 동작하는데 코드가 지저분하다는 이유만으로 불평하는 것은 너무 미적인 부분만 보는 것은 아닌가?

코드를 수정하는 것은 결국 사람이 하는 일이다.

코드가 지저분하면 수정해야할 부분을 찾고, 기존 코드와 잘 맞물려 동작하게 할 방법을 강구하기 어렵다.

→ 프로그램이 새로운 기능을 추가하기에 편한 구조가 아니라면, 먼저 기능을 추가하기 쉬운 형태로 리팩터링하고 나서 원하는 기능을 추가하자.

리팩터링이 필요한 일은 요구사항의 변경때문이다. 코드를 절대 변경할 일이 없다면 아무리 지저분해도 그냥 놔둬도 된다. 그러나 다른 사람이 읽고 이해해야하는 순간들이 오고, 로직을 파악하기가 어렵다면 대책을 마련해야한다.

## 리팩터링의 첫 단계

리팩터링의 첫단계는 항상 똑같다. 리팩터링할 코드 영역을 꼼꼼하게 검사해줄 테스트 코드들부터 마련해야한다. 리팩터링 과정에서 실수를 할 수 있기 때문에 회귀버그 방지를 위한 지속적인 검증을 해줄 수 있는 테스트 코드를 만들고, 이에 의지해야한다.

## 함수 쪼개기

긴 함수를 리팩터링 할 때는 먼저 전체 동작을 각각의 부분으로 나눌 수 있는 지점을 찾는다. 그리고 그 부분을 함수로 쪼개고 적절한 함수명을 지어준다. 이러면 코드 전부를 읽지 않고, 좀 더 높은 레벨에서 코드를 이해할 수 있게 된다. 또 이 과정에서 테스트 코드를 주기적으로 실행시켜줘야한다.

함수 쪼개기 과정에서

- 함수 추출하기
- 임시 변수를 질의함수로 바꾸기
- 변수 인라인하기
- 함수 선언 바꾸기
- 반복문 쪼개기
- 문장 슬라이드하기

등의 기법이 나오는데 이는 추후 다룰 장에서 더 자세히 알아보도록 하자.

리팩터링을 하나보면 성능이 이전보다 안좋아질 여지가 생기는 코드로 변경될 수 있는데, 대부분은 리팩터링한다해도 크게 영향이 없다. 다만 성능의 영향이 있는 경우도 있다. 이럴때도 리팩터링을 우선적으로 하는게 좋다. 왜냐면 깔끔한 코드로 바꾸고 나면 성능 최적화도 더 쉬워지는 경우가 많기 때문이다. 우선 리팩터링을 마무리하고 성능을 생각해보도록 하자.

함수로 잘 쪼개고 나면 최상단 함수는 여러 작은 함수의 흐름으로 나뉘어지게되고, 이러면 가독성이 좋아진다.(함수명을 잘 지었을 경우)

- 이 과정은 프로그램의 논리적인 요소를 파악하기 쉽도록 코드 구조를 보강하는데 주안점을 둔 것이다.
- 또한 리팩토링 초기 단계에서 흔히 수행하는 일이다. (골격 개선 정도로 이해하자)

이러고나면 이제 한 함수가 여러 역할을 하는 것을 확인할 수도 있고, 다형성을 적용하여 OCP를 지키고 싶어지는 부분도 생길 수 있다. 이런 부분이 더 눈에 띌 수 있게 된 이유는 작은 함수로 분리하여 코드 흐름 파악이 더 쉬워졌기 때문이다.

리팩터링은 대부분 코드가 하는 일을 파악하는 데서 시작한다. 그래서 코드를 읽고, 개선점을 찾고, 리팩터링 작업을 통해 개선점을 코드에 반영하는 식으로 진행한다. 그 결과 코드가 명확해지고 이해하기 더 쉬워진다. 그러면 또 다른 개선점이 떠오르면서 선순환이 형성된다.

좋은 코드를 가늠하는 확실한 방법은 ‘`얼마나 수정하기 쉬운가`’ 이다.

또한 리팩터링을 하는 과정에서 단계는 매우 잘게 나눠야한다. 그래야 더 빠르게 처리할 수 있고, 코드가 깨지지 않는다. 작은 단계들이 모여서 큰 변화를 만드는 방식으로 진행해야한다.