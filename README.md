# 사다리 게임
## 진행 방법
* 사다리 게임 게임 요구사항을 파악한다.
* 요구사항에 대한 구현을 완료한 후 자신의 github 아이디에 해당하는 브랜치에 Pull Request(이하 PR)를 통해 코드 리뷰 요청을 한다.
* 코드 리뷰 피드백에 대한 개선 작업을 하고 다시 PUSH한다.
* 모든 피드백을 완료하면 다음 단계를 도전하고 앞의 과정을 반복한다.

## 온라인 코드 리뷰 과정
* [텍스트와 이미지로 살펴보는 온라인 코드 리뷰 과정](https://github.com/nextstep-step/nextstep-docs/tree/master/codereview)

## 람다
### 람다(lambda)
람다와 클로저
람다는 익명 함수의 다른 표현이다. 즉, 함수는 함수인데 이름이 없는 경우를 의미한다.

#### Collection의 모든 값을 출력
<pre><code>// nextstep.fp.Lambda의 printAllOld method
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

for (int number : numbers) {
    System.out.println(number);
}</code></pre>

#### 람다가 없던 시절
<pre><code>List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

numbers.forEach(new Consumer<Integer>() {
    public void accept(Integer value) {
        System.out.println(value);
    }
});</code></pre>

#### 람다를 활용하면…
<pre><code>// nextstep.fp.Lambda의 printAllLambda method
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

numbers.forEach((Integer value) -> System.out.println(value));
numbers.forEach(value -> System.out.println(value)); // Type 추론이 가능해 Type 생략 가능
numbers.forEach(System.out::println); // :: 연산자 활용 가능
= numbers.forEach(x -> System.out.println(x));</code></pre>

#### 람다 문법
input arguments -> body

### 람다 실습 1 - 익명 클래스를 람다로 전환
* 다음 테스트 코드에서 MoveStrategy에 대한 익명 클래스로 구현하고 있는데 람다를 적용해 구현한다.
<pre><code>// nextstep.fp.CarTest의 이동, 정지 method
public class CarTest {
    @Test
    public void 이동() {
        Car car = new Car("pobi", 0);
        Car actual = car.move(new MoveStrategy() {
            @Override
            public boolean isMovable() {
                return true;
            }
        });
        assertEquals(new Car("pobi", 1), actual);
    }

    @Test
    public void 정지() {
        Car car = new Car("pobi", 0);
        Car actual = car.move(new MoveStrategy() {
            @Override
            public boolean isMovable() {
                return false;
            }
        });
        assertEquals(new Car("pobi", 0), actual);
    }
}</code></pre>

### 람다 실습 2 - 람다를 활용해 중복 제거
<pre><code>// nextstep.fp.Lambda의 sumAll method
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

public int sumAll(List<Integer> numbers) {
    int total = 0;
    for (int number : numbers) {
        total += number;
    }
    return total;
}
// nextstep.fp.Lambda의 sumAllEven method
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

public int sumAllEven(List<Integer> numbers) {
    int total = 0;
    for (int number : numbers) {
        if (number % 2 == 0) {
            total += number;
        }
    }
    return total;
}</code></pre>
List에 담긴 값 중 3보다 큰 수만을 더해야 한다.
이 기능을 구현하려고 보니 앞의 요구사항 1,2와 많은 중복이 발생한다. 람다를 활용해 중복을 제거한다.

// nextstep.fp.Lambda의 sumAll, sumAllEven, sumAllOverThree method 소스 코드를 확인하고 중복 제거한다.

### 힌트

* 변경되는 부분과 변경되지 않는 부분의 코드를 분리한다.
* 변경되는 부분을 인터페이스로 추출한다.
* 인터페이스에 대한 구현체를 익명 클래스(anonymous class)로 구현해 메소드의 인자로 전달한다.
* 구글에서 자바의 익명 클래스로 검색해 익명 클래스가 무엇인지 학습한다.
* 인터페이스는 다음과 같은 형태로 추출할 수 있다.
<pre><code>public interface Conditional {
    boolean test(Integer number);
}</code></pre>
* Conditional을 활용해 공통 메소드의 구조는 다음과 같다.
<pre><code>public int sumAll(List<Integer> numbers,
    Conditional c) {
    // c.test(number)를 활용해 구현할 수 있다.
}</code></pre>
* 익명 클래스를 자바 8의 람다를 활용해 구현한다.

## 스트림(stream)
### map, filter, reduce
Collection에 담긴 데이터를 처리하려면 Collection을 순회하면서 각 Element를 처리하는 것이 일반적이다. 앞으로는 순회하는 것을 잊고, Element 처리에만 집중하자.

#### filter
* 요구사항은 파일 문자 중 길이가 12보다 큰 문자의 수를 구한다.
<pre><code>
// nextstep.fp.StreamStudy countWords method

String contents = new String(Files.readAllBytes(
  Paths.get("../ war-and-peace.txt")), StandardCharsets.UTF_8);
List<String> words = Arrays.asList(contents.split("[\\P{L}]+"));

long count = 0;
for (String w : words) {
  if (w.length() > 12) count++;  
}</code></pre>
#### filter 활용해 구현
<pre><code>String contents = new String(Files.readAllBytes(
  Paths.get("../alice.txt")), StandardCharsets.UTF_8);
List<String> words = Arrays.asList(contents.split("[\\P{L}]+"));

long count = 
  words.stream().filter(w -> w.length() > 12).count();</code></pre>
  
#### map
* List에 담긴 모든 숫자 값을 2배한 결과 List를 생성한다.

<pre><code>// nextstep.fp.StreamStudy 클래스의 doubleNumbers method 참고
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);
List<Integer> dobuleNumbers =
  numbers.stream().map(x -> 2 * x).collect(Collectors.toList());</code></pre>
#### reduce
* List에 담긴 모든 숫자의 합을 구한다.

<pre><code>// nextstep.fp.StreamStudy 클래스의 sumAll method 참고

List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

public int sumAll(List<Integer> numbers) {
    return numbers.stream().reduce(0, (x, y) -> x + y);
}</code></pre>

#### 다양한 stream method 실습
> src/main/resources/fp 디렉토리 아래에 있는 war-and-peace.txt 파일을 읽어 다음 요구사항을 만족하세요.

#### map, reduce, filter 실습 1
* List에 담긴 모든 숫자 중 3보다 큰 숫자를 2배 한 후 모든 값의 합을 구한다. 지금까지 학습한 map, reduce, filter를 활용해 구현해야 한다.
* nextstep.fp.StreamStudyTest 클래스의 sumOverThreeAndDouble() 테스트를 pass해야 한다.

#### map, reduce, filter 실습 2
* nextstep.fp.StreamStudy 클래스의 printLongestWordTop100() 메서드를 구현한다. 요구사항은 다음과 같다.

* 단어의 길이가 12자를 초과하는 단어를 추출한다.
* 12자가 넘는 단어 중 길이가 긴 순서로 100개의 단어를 추출한다.
* 단어 중복을 허용하지 않는다. 즉, 서로 다른 단어 100개를 추출해야 한다.
* 추출한 100개의 단어를 출력한다. 모든 단어는 소문자로 출력해야 한다.

힌트
* 단어의 길이가 12자를 초과하는 단어를 추출한다.
* 12자가 넘는 단어 중 길이가 긴 순서로 100개의 단어를 추출한다.
    * sorted() method 활용
* 단어 중복을 허용하지 않는다. 즉, 서로 다른 단어 100개를 추출해야 한다.
    * distinct() method 활용
* 추출한 100개의 단어를 출력한다. 모든 단어는 소문자로 출력해야 한다.
    * String.toLowerCase() method 활용
## Optional
### 요구사항 1 - Optional을 활용해 조건에 따른 반환
nextstep.optional.User의 ageIsInRange1() 메소드는 30살 이상, 45살 이하에 해당하는 User가 존재하는 경우 true를 반환하는 메소드이다.

같은 기능을 Optional을 활용해 ageIsInRange2() 메소드에 구현한다. 메소드 인자로 받은 User를 Optional로 생성하면 stream의 map, filter와 같은 메소드를 사용하는 것이 가능하다.

nextstep.optional.UserTest의 테스트가 모두 pass해야 한다.

힌트
* Guide To Java 8 Optional 문서를 참고해 Optional 사용 방법을 익힌다.
* Optional.ofNullable(user)을 활용해 User을 Optional로 생성하는 것이 가능하다.
* Optional의 map(), filter() 메소드등을 활용해 필요한 데이터를 추출
* Optional의 isPresent() 메소드 활용

### 요구사항 2 - Optional에서 값을 반환
nextstep.optional.Users의 getUser() 메소드를 자바 8의 stream과 Optional을 활용해 구현한다.

자바 8의 stream과 Optional을 사용하도록 리팩토링한 후 UsersTest의 단위 테스트가 통과해야 한다.

힌트
* Guide To Java 8 Optional 문서를 참고해 Optional 사용 방법을 익힌다.
* Optional의 orElse() 메소드 활용해 구현한다.

### 요구사항 3 - Optional에서 exception 처리
nextstep.optional.ExpressionTest의 테스트가 통과하도록 Expression의 of 메소드를 구현한다.

단, of 메소드를 구현할 때 자바 8의 stream을 기반으로 구현한다.

힌트
* Guide To Java 8 Optional 문서를 참고해 Optional 사용 방법을 익힌다.
* 자바의 enum 전체 값은 values() 메소드를 통해 배열로 접근 가능하다.
* Arrays.stream()을 이용해 배열을 stream으로 생성할 수 있다.
* 일치하는 값 하나를 추출할 때 findFirst() 메소드를 활용 가능하다.
* Optional의 orElseThrow() 메소드 활용해 구현한다.

# 2단계 - 사다리(생성)
### 기능 요구사항
* 사다리 게임에 참여하는 사람에 이름을 최대5글자까지 부여할 수 있다. 사다리를 출력할 때 사람 이름도 같이 출력한다.
* 사람 이름은 쉼표(,)를 기준으로 구분한다.
* 사람 이름을 5자 기준으로 출력하기 때문에 사다리 폭도 넓어져야 한다.
* 사다리 타기가 정상적으로 동작하려면 라인이 겹치지 않도록 해야 한다.
    * |-----|-----| 모양과 같이 가로 라인이 겹치는 경우 어느 방향으로 이동할지 결정할 수 없다.
### 프로그래밍 요구사항
* 자바 8의 스트림과 람다를 적용해 프로그래밍한다.
* 규칙 6: 모든 엔티티를 작게 유지한다.

### 실행 결과
* 위 요구사항에 따라 4명의 사람을 위한 5개 높이 사다리를 만들 경우, 프로그램을 실행한 결과는 다음과 같다.

<pre><code>
참여할 사람 이름을 입력하세요. (이름은 쉼표(,)로 구분하세요)
pobi,honux,crong,jk

최대 사다리 높이는 몇 개인가요?
5

실행결과

pobi  honux crong   jk
    |-----|     |-----|
    |     |-----|     |
    |-----|     |     |
    |     |-----|     |
    |-----|     |-----|
</code></pre>

### Step2 리뷰사항
* [x] [fix#01][Ladder.java] 객체지향 생활체조 7번, 8번 원칙을 지킬 것
    * 규칙 7 : 3개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다
    * 규칙 8 : 일급 콜렉션을 쓴다
* [x] [fix#02][Ladder.java] Ladder에서 하나의 Row를 의미하는 Row까지 생성하고 있음. 이 부분을 해당 책임을 가진 객체에게 위임하고 결과를 반환 받도록 수정
* [x] [fix#03][Ladder.java] 핵심 로직을 가지는 도메인 객체와 화면과 관련된 View를 변경할 것 (현재 기준 View에 대한 요구사항이 변경 되었을 때 도메인이 변경되어야 함)
* [x] [fix#04][Point.java] if문의 조건을 한 줄로 표현 할 것 
* [x] [fix#05][Point.java] Primitive type(기본 타입)은 Default 값을 가짐. 즉 항상 값이 있는 상태이므로 null이 될 수 없음 (boolean은 기본 값으로 false를 가짐) 
* [x] [fix#06][Row.java] 존재하지 않는 값을 표현히가 위해 null을 사용하기보다, 존재할 지 안 할지 모르는 값을 표현할 수 있는 Optional을 사용할 것
* [x] [fix#07][Ladder.java] RANDOM 을 클래스 변수로 선언한 이유가 있을 지 
* [x] [fix#08][Ladder.java] Ladder 객체를 생성하는 순간, 사다리 판이 만들어질 수 있도록 수정 (불필요한 make 호출을 없앨 것)
* [x] [fix#09][JoinersConvertorTest.java] @MethodSource에 대해서 학습할 것
    * https://www.baeldung.com/parameterized-tests-junit-5
* [ ] [fix#10][LadderGame.java] height는 꼭 필요한 변수인가
* [ ] [fix#11][Ladder.java] 파라미터로 String 타입의 input을 받아 내부에서 Joiners객체를 생성하는 것과 파라미터로 Joiners 객체를 바로 받는 것을 비교 해보시고 의견을 공유 할 것
* [ ] [fix#12][Ladder.java] Rows를 통해 사다리 높이를 알 수 있을 것 같은데, height 변수가 꼭 필요한가 
* [ ] [fix#13][Joiners.java] 참여자의 수를 조회하는 메소드에 맞게 메소드 명을 변경할 것 
* [ ] [fix#14][Joiners.java] 개인적인 궁금함, 현재 게임 참여자를 만드는 책임이 JoinersConvertor에 있는데, Joiners에서 책임을 가지게 할 수는 없었을까, 현재 Joiners 객체가 빈약한 도메인 모델임
    * 빈약한 모델 : 비즈니스적인 내용을 갖고 있지 않은 소위 말하는 getter/setter 만 있는 객체 (Anti-patterns)  
* [ ] [fix#15][Ladder.java] make() 메소드명 변경 (무엇을 만드는지도 알려주면 좋을 것) 
* [ ] [fix#16][Ladder.java] randomRows, randomCols 여러 변수를 같은 라인에 선언하는 경우, SonarLint에서는 코드 읽기가 어렵다며 별도의 라인에 선언하라고 경고해 줌 
* [ ] [fix#17][Ladder.java] for문이 돌면서 count 메소드를 매번 호출하고 있음 
* [ ] [fix#18][Ladder.java] 재 사용 되기 쉬운 Random 객체 같은 경우에는 메소드가 호출될 때 마다 객체를 생성하기보다, 한 개의 Random 객체를 만들어두고 재사용 하게 할  
* [ ] [fix#19][Ladder.java] rows.get().draw()에서 객체의 상태값을 묻지 말고(get), 어떤 행위를 하도록 메세지를 보낼  
* [ ] [fix#20][Row.java] 하나의 Row를 의미하는 Row 클래스에서 rowNumber가 필요한지 궁금, 그리고 columnSize도 Point의 개수로 알 수 있을 것 같은데 제거할 것 
* [ ] [fix#21][Rows.java] count() 기능에 맞게 메소드 명을 변경 할 것 
* [ ] [fix#22][Row.java] .filter(p -> p.match(x,y)) 와 같이 변경할 수 있음  

