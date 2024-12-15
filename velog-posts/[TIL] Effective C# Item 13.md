<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h4 id="아이템-13-정적-클래스-멤버를-올바르게-초기화하라">아이템 13: 정적 클래스 멤버를 올바르게 초기화하라</h4>
<p>정적 멤버 변수를 포함하는 타입이 있다면, 인스턴스 생성 전 반드시  정적 멤버 변수를 초기화 해야한다. C#에서는 정적 멤버 초기화 구문과, 정적 생성자를 제공하는데, 정적 생성자는 타입 내 정의된 모든 메서드, 변수, 속성에 최초로 접근하기 전 자동으로 호출되는 특이한 메서드이다.</p>
<p>인스턴스 멤버 초기화와 마찬가지로, 정적 멤버를 간단히 초기화 하는 경우라면 아래 예제처럼 정적 생성자보다는 멤버 초기화 구문을 사용하는 것이 좋다.</p>
<pre><code>public class MySingleTon
{
    private static readonly MySingleTon theOneAndOnly = new MySingleTon();

    public static MySingleTon TheOnly
    {
        get
        {
            return theOneAndOnly;
        }
    }

    private MySingleTon()
    {
    }
}</code></pre><p>초기화 과정이 복잡한 경우는 아래 예제처럼 정적 생성자에서 초기화 해줘도 된다.</p>
<pre><code>public class MySingleTon2
{
    private static readonly MySingleTon2 theOneAndOnly;

    static MySingleTon2()
    {
        theOneAndOnly = new MySingleTon2();
    }

    public static MySingleTon2 TheOnly
    {
        get
        {
            return theOneAndOnly;
        }
    }

    private MySingleTon2()
    {
    }
}</code></pre><p>인스턴스 멤버 초기화 구문과 마찬가지로, 정적 멤버 초기화 구문 또한 정적 생성자가 호출되기 이전에 실행되며, 베이스 클래스 정적 생성자보다도 먼저 호출된다.</p>
<p>앱도메인 내 CLR이 특정 타입에 접근해야 하는 경우 정적 생성자를 우선적으로 호출하며, 모든 타입은 정적 생성자를 하나만 가질 수 있고 어떠한 인자도 넘길 수 없다.</p>
<p>정적 생성자는 CLR에 의해서 호출되기 때문에 예외가 발생할 가능성이 있는 경우라면 조심스럽게 다뤄야한다.
만약 호출하는 쪽에서 예외(TypeInitializationException)를 잡아버리면 앱 도메인을 언로드하지 않는 한 객체를 생성하지 못한다.</p>
<p>이러한 타입 초기화 과정은 재시도 되지 않는다.</p>
<p>인스턴스 멤버 초기화와 마찬가지로, 예외가 발생할 가능성이 있는 경우에는 멤버 초기화 구문 대신 정적 생성자를 사용해야 한다.</p>