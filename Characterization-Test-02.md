# Characterization Test - 02

2018-11-14

Characterization Test를 도입해서 통계 데이터를 생성하는 서비스 메서드(Method Under Test,  MUT라고 하자)를 여러 케이스를 커버할 수 있도록 일반화시켜 확장하고 있다.

로컬 환경에서는 데이터 조회가 물리적으로 불가능해서 로컬에서 테스트하려면 테스트 데이터를 반드시 만들어야 한다.

트리구조를 이루고 있는 데이터를 통해 조회하는 부분이 있어서 테스트 데이터를 만드는데 꽤 오랜 시간이 걸렸다.

테스트 데이터를 만들때는 모든 필드에 대한 값을 넣을 필요 없이 MUT의 기존 로직을 에러 없이 통과할 수 있을 정도로만 구성하면 된다.

그 후에는 MUT의 기존 로직 중에서 데이터 조회에 사용되는 Repository 인터페이스를 모두 Mocking하고, 호출되는 메서드를 모두 Stubbing 했다.

글로 쓰면 짧고 테스트 데이터 생성과 Mocking, Stubbing에 사용된 코드는 약 150 라인 정도로 그리 많은 분량은 아니지만 꽤나 지겹고 번거로운 과정이다.

어쨌든 긴 노가다 과정을 마치고 커버리지와 함께 테스트를 실행해보니 MUT 로직 중에서 지나가야 할 구간을 에러 없이 모두 통과했다.

아직 assert에 사용할 expected 값조차 모르는 상태이므로 테스트 자체는 당연히 실패다. 하지만 애초부터 테스트 통과는 목적이 아니었다. 목적은 바로 MUT가 반환할 객체의 JSON 문자열이고 나는 그걸 얻었다.

assertThat(actual).isEqualTo(expected); 에서 expected 부분을 방금 전에 얻은 귀중한 JSON 문자열로 덮어써버리면 테스트는 통과한다. MUT에 대한 방어막이 완성된 것이다.

이제 안심하고 마음껏 MUT를 리팩터링할 수 있게 되었다.

MUT의 코드는 처음에는 여러 케이스에서 동작하도록 일반화 시키기 불가능해보였는데, 내부를 변수화, 인라인화, 파라미터화, 메서드 추출 등을 이용해서 요리조리 바꿔보니 헐.. 처음 생각과 달리 일반화가 가능했다.

하지만 나를 아니 내가 짠 코드를 믿을 순 없다. 아니 믿어선 안 된다.

아까 만들어 둔 테스트를 실행한다. 오오 ㅅㅂ 초록불이 들어왔다.
존나 짜릿한 거다..

사실 코드 복사하고 몇 군데 수정해서 만들면 어쩌면 이 방식보다 더 빨리 만들었을 수도 있을 것이다.

하지만 테스트 데이터가 없으니 반드시 서버 테스트를 거쳐야 했을 것이고, 개발도 존나 못하는 놈이 한 번에 뙇 성공시켰을리 만무하다. 결국 MUT의 복잡도에 따라 여러번 시행 착오를 겪어야 했을 것이다.

정말 처참한 상황은 그런 과정을 거치고 나서 남는 건 달랑 새로 만든 메서드 하나뿐이라는 사실이다. 앞으로 기능 확장이 더 필요하면 위 과정을 똑같이 반복해야 한다는 걸 생각하면 테스트를 만들면서 개발하는 방식이 정신적으로나 효율적으로나 훨씬 행복감을 가져다 준다.

며칠 전의 교육이 없었다면 앞으로도 계속 위와 같은 머저리짓을 반복했을텐데.. 이제라도 알게 되어 진짜 다행이다.