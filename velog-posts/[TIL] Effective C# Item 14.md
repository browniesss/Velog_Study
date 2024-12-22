<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="아이템-14-초기화-코드가-중복되는-것을-최소화하라">아이템 14: 초기화 코드가 중복되는 것을 최소화하라</h3>
<p>기존 C#에서 생성자를 작성할 때 멤버 변수의 값을 초기화 하기위해 여러개의 생성자를 작성하곤 하였는데, 그렇게 작성하지 말고 공용 생성자를 작성해두는게 좋다.</p>
<pre><code>public class MyClass
{
    // 데이터
    private List ~~~

    // 변수
    private string ~~

    public MyClass() : this(0, &quot;&quot;)
    {
    }

    public MyClass(int initialCount) : this(initialCount, string.Empty)
    {
    }

    public MyClass(int initialCount, string name)
    {
        coll = ~~;

        this.name = name;
    }</code></pre><p>위 예제처럼 생성자를 이용해 멤버 변수의 값을 초기화하는 경우라면 다른 생성자를 호출하여 초기화 과정의 일부를 위임할 수 있다. 또 C# 4.0에 추가된 기본 매개변수 기능을 활용하여 아래처럼 작성할 경우 중복 코드를 더 줄일 수 있다.</p>
<pre><code>public class MyClass
{
    // 데이터
    private List ~~~

    // 변수
    private string ~~

    public MyClass() : this(0, string.Empty)
    {
    }

    public MyClass(int initialCount = 0, string name = &quot;&quot;)
    {
        coll = ~~;

        this.name = name;
    }</code></pre><p>생성자의 모든 매개변수에 대해 기본값을 정의한 경우에 new MyClass()라고만 작성해도 유효한 코드가 되지만, 어떤 경우에도 제한 없이 이런 구조를 사용하기 위해서는 앞 예제코드처럼 매개변수가 없는 생성자를 명시적으로 작성해야 한다.</p>
<p>대부분 기본값을 가지는 매개변수를 사용하는 생성자를 사용할 것이므로 문제가 없겠지만, 제네릭 클래스를 사용(new () 제약조건이 명시되어 있음)할 경우에는 무조건 매개변수가 없는 생성자를 구현해야 한다.</p>
<p>앞 코드 생성자 중 name 매개변수의 기본값을 string.Empty가 아닌 &quot;&quot;로 지정한게 보이는데, string.Empty는 컴파일 타임 상수가 아닌 string 클래스에서 정의하는 정적 속성이기 때문이다. 매개변수 기본값은 컴파일타임 상수만이 가능하다.</p>
<p>반면, 여러개 생성자를 만드는 대신 기본값을 갖는 생성자를 작성하면 코드의 결합도가 높아지는 단점도 있다. 기본값을 갖는 매개변수를 사용하면 형식 매개변수의 이름과 매개변수의 기본값이 모두 공개 인터페이스의 일부가 되며, 매개변수의 이름을 변경하면 이 타입을 사용하는 모든 코드를 다시 컴파일 해야 한다.
그렇기에 머지않아 코드를 변경해야할 수 있다면 기본값을 갖는 매개변수를 사용하기보다, 기존 방식처럼 여러 생성자를 오버로딩하는 편이 나을 수 있다.</p>
<h4 id="생성자-체인-기법">생성자 체인 기법</h4>
<ul>
<li>생성자 체인 기법이란 임의의 생성자가 동일 클래스 내에 정의된 다른 생성자를 호출하는 방식</li>
</ul>
<pre><code>public class MyClass
{
    private List coll;
    private string name;

    public MyClass()
    {
        commonConstructor(0, &quot;&quot;);
    }

    public MyClass(int initialCount)
    {
        commonConstructor(initialCount, &quot;&quot;);
    }

    public MyClass(int initialCount, string Name)
    {
        commonConstructor(initialCount, Name);
    }

    private void commonConstructor(int count, string name)
    {
        coll = ~~;

        this.name = name;
    }
}</code></pre><p>위 코드는 첫번째 예제와 비슷한듯 보이지만, 훨씬 비효율적인 코드를 생성한다. 실제로 컴파일러는 이 코드를 컴파일할 때 사용자가 작성하지 않은 코드를 추가한다. 먼저, 모든 인스턴스 변수에 대한 초기화 코드가 추가되고 베이스 클래스의 생성자를 호출하는 코드가 추가되고, 마지막으로는 사용자가 작성한 공용 유틸리티 함수를 호출하는 코드가 추가된다.</p>
<p>생성자를 작성할 때 기본값을 가지는 매개변수를 사용하는 방법과 다수의 생성자를 오버로딩하는 방법은 각기 적합한 용도가 있다. 일반적으로는 기본값을 가지는 매개변수를 사용해 생성자를 작성하는게 좋다.</p>
<p>특정 타입의 인스턴스가 생성되는 전체 순서를 정리해보자면 다음과 같다.</p>
<ol>
<li>정적 변수의 저장 공간을 0으로 초기화</li>
<li>정적 변수에 대한 초기화 구문 수행</li>
<li>베이스 클래스의 정적 생성자 수행</li>
<li>정적 생성자 수행</li>
<li>인스턴스 변수의 저장 공간을 0으로 초기화</li>
<li>인스턴스 변수에 대한 초기화 구문 수행</li>
<li>적절한 베이스 클래스의 인스턴스 생성자 수행</li>
<li>인스턴스 생성자 수행</li>
</ol>