<p>Effective C# Chapter 1(C# 언어요소) 내용 중 Item 6 ~  항목을 정리한 글입니다.
<img height="25%" src="https://velog.velcdn.com/images/kl45678/post/3e98c70c-e754-4b1f-9dd6-4302d59e2679/image.png" width="25%" /></p>
<h3 id="아이템-6--nameof-연산자를-적극-활용하라">아이템 6 : nameof() 연산자를 적극 활용하라</h3>
<p>분산 시스템의 대중화로 인해 이질적인 플랫폼과 다른 언어로 개발된 프로그램 간의 데이터 교환이 필요해졌다. 이를 해결하기 위해 문자열 식별자에 의존하는 라이브러리가 많이 사용되지만, 타입 정보 손실이라는 단점이 있다. 이를 극복하기 위해 C# 6.0에서는 타입 안전성을 보장하는 nameof() 연산자가 도입되었다.</p>
<blockquote>
<p>nameof() 키워드의 역할 : 심볼 그 자체를 해당 심볼을 포함하는 문자열로 대체</p>
</blockquote>
<p>아래는 일반적인 활용 예로 INotifyPropertyChanged 인터페이스의 구현부이다.</p>
<pre><code class="language-cs">public string Name
{
    get
    {
        return name;
    }
    set
    {
        if(value != name)
        {
            name = value;
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
        }
    }
 }
 private string name;</code></pre>
<p>위 예시에서 nameof() 연산자를 사용했기 때문에 속성의 이름을 변경할 경우 이벤트의 인자로 전달해야 하는 문자열도 쉽게 변경할 수 있다.
nameof()는 심볼의 이름을 평가하며, 타입, 변수, 인터페이스, 네임스페이스에 대해 사용할 수 있다. 다만, 제네릭 타입을 사용할 경우에는 부분적으로 제약이 있어서, 모든 타입 매개변수가 지정된 닫힌 제네릭 타입만을 사용할 수 있다.</p>
<blockquote>
<ul>
<li>열린 제네릭 타입 (ex. Dictionary&lt;Tkey, Tvalue&gt;) : 아직 결정되지 않은 타입 파라미터가 있다</li>
<li>닫힌 제네릭 타입 (ex. Dircionary &lt;string, int&gt;) : 모든 파라미터가 정해짐</li>
</ul>
</blockquote>
<h4 id="정리">정리</h4>
<ul>
<li>nameof() 연산자를 사용하면 심볼의 이름을 완전히 바꾸거나 수정할 경우에도 손쉽게 그 변경 사항을 반영할 수 있다. 또한, 정적 분석 도구를 이용하면 인자의 이름을 매개변수로 취하는 메서드를 사용할 때 저지르곤 하는 실수들을 미연에 방지할 수 있다.</li>
</ul>
<hr />
<h3 id="아이템-7--델리게이트를-이용하여-콜백을-표현하라">아이템 7 : 델리게이트를 이용하여 콜백을 표현하라</h3>
<p>콜백은서버가 비동기적으로 클라이언트에게 피드백을 주기 위해서 주로 사용하는 방법이다. 이를 위해 멀티스레딩 기술도 사용되고, 동기적으로 상태를 갱신하는 기법도 활용된다. 콜백은 C#에서 델리게이트를 이용하여 표현된다.</p>
<p>여러 클래스간 상호 통신 시 클래스 간의 결합도를 낮추고 싶다면, 인터페이스보다 델리게이트를 사용하는 것이 좋다. 델리게이트는 런타임에 통지 대상을 설정할 수 있고, 하나의 델리게이트에 여러 메서드에 대한 참조를 설정해 다수의 클라이언트에게 통지를 보낼 수도 있다.</p>
<h4 id="c-에서-자주-사용되는-델리게이트-종류">C# 에서 자주 사용되는 델리게이트 종류</h4>
<ul>
<li>Prediate &amp;ltT&amp;gt<ul>
<li>조건을 검사하여 bool 값을 반환하는 델리게이트</li>
</ul>
</li>
<li>Action &lt;&gt;<ul>
<li>여러 개의 매개변수를 받지만 반환 타입이 void인 델리게이트</li>
</ul>
</li>
<li>Func &lt;&gt;<ul>
<li>여러개의 매개변수를 받아 단일의 결괏값을 반환하는 델리게이트. Func&lt;T, bool&gt;은 Predicate &amp;ltT&gt;와 동일하다고 볼 수 있음.</li>
</ul>
</li>
</ul>
<p>LINQ는 이러한 개념을 기반으로 만들어졌다. 실제로 List&amp;ltT&gt;는 콜백을 사용하는 다양한 메서드를 가지고 있다. 아래는 예시이다.</p>
<pre><code class="language-cs">List&lt;int&gt; numbers = Enumerable.Range(1, 200).ToList();

var oddNumbers = numbers.Find(n =&gt; n % 2 == 1);
var test = numbers.TrueForAll(n =&gt; n &lt; 50);

numbers.RemoveAll(n =&gt; n % 2 == 0);

numbers.ForEach(item =&gt; Console.WriteLine(item));</code></pre>
<p>Find() 메서드는 Predicate&amp;ltint&gt; 형식의 델리게이트를 사용하여 리스트 내에 포함된 요소에 대항 테스트를 수행한다.
TrueForAll() 또한 매우 유사한 방법으로 동작하는데, 각 요소를 개별적으로 테스트하되 모든 항목이 테스트를 통과한 경우에만 true를 반환한다. RemoveAll()은 델리게이트에서 정의한 테스트를 통과한 항목들을 리스트에서 제거한다.
마지막으로 Foreach() 메서드는 리스트 내의 각 요소에 대하여 델리게이트로 지정한 동작을 수행한다. 컴파일러는 람다 표현식을 메서드로 변환한 후, 이 메서드를 참조하는 델리게이트를 생성한다.</p>
<p>모든 델리게이트는 기본적으로 멀티캐스트(2개 이상의 함수를 참조)가 가능하다. 멀티캐스트 델리게이트는 한 번만 호출하면 델리게이트 객체 추가된 모든 대상 함수가 호출된다. 하지만 이러한 구조에는 두 가지 주의해야 할 부분이 있다.</p>
<blockquote>
<h4 id="주의해야할-점">주의해야할 점</h4>
</blockquote>
<ul>
<li>예외에 안전하지 않다.</li>
<li>마지막으로 호출된 함수의 반환값이 델리게이트의 반환값으로 간주된다.</li>
</ul>
<p>멀티캐스트 델리게이트의 내부 동작 방식은 대상 함수들을 연속적으로 호출하는 형태로 구현된다. 델리게이트는 어떠한 예외도 잡지(catch) 않으며, 따라서 예외가 발생하면 함수 호출 과정이 중단된다.
사용자가 임의로 중단이 가능하도록 다음과 같이 메서드를 만들었다고 가정하자.</p>
<pre><code class="language-cs">public void LengthyOperation(Func&lt;bool&gt; pred)
{    
    foreach (ComplicatedClass cl in container)
    {
        cl.DoLengthyOperation();
        // 사용자가 임의로 중단을 요청했는지 확인
        if (false == pred())
            return;
    }
}        </code></pre>
<p>이 메서드를 유니캐스트 델리게이트 형태로 사용하면 문제가 없지만, 멀티캐스트 델리게이트 형태로 아래와 같이 사용하면 문제가 발생한다.</p>
<pre><code class="language-cs">Func&lt;bool&gt; cp = () =&gt; CheckWithUser();
cp += () =&gt; CheckWithSystem();
c.LengthyOperation(cp);</code></pre>
<p>델리게이트의 반환값은 멀티캐스트 체인에서 마지막으로 호출된 함수의 반환값이 되며, 다른 반환값은 모두 무시된다. 따라서 앞 예제의 <code>CheckWithUser()</code>의 반환값은 무시된다.
이러한 두 가지 문제를 해결하려면 델리게이트에 포함된 호출 대상 콜백 함수를 직접 다뤄야한다. 델리게이트는 추가된 메서드의 리스트를 가지므로, 이 리스트를 살펴서 직접 호출하는 방식으로 코드를 작성하는 것이다.</p>
<pre><code class="language-cs">public void LengthyOperation2(Func&lt;bool&gt; pred)
{
    bool bContinue = true;
    foreach (ComplicatedClass cl in container)
    {
        cl.DoLengthyOperation();
        foreach (Func&lt;bool&gt; pr in pred.GetInvocationList())
            bContinue &amp;= pr();

        if (!bContinue)
            return;
    }
}</code></pre>
<p>위와 같이 코드를 작성하면 델리게이트에 추가된 개별 메서드가 true를 반환한 경우에만 다음 메서드에 대한 호출을 이어간다.</p>
<h4 id="정리-1">정리</h4>
<p>델리게이트는 런타임에 콜백을 구성하는 최고의 방법이다. 델리게이트를 사용하면 콜백을 사용해야 하는 클라이언트를 더욱 단순하게 구성할 수 있을 뿐 아니라, 런타임에 콜백 함수를 구성할 수 있다. 게다가 하나의 델리게이트에 여러 개의 콜백 함수를 추가할 수도 있다. .NET환경에서 콜백이 필요한 경우에는 반드시 델리게이트를 사용하자.</p>