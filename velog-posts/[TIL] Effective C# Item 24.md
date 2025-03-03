<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="아이템-24-베이스-클래스나-인터페이스에-대해서-제네릭을-특화하지-말라">아이템 24: 베이스 클래스나 인터페이스에 대해서 제네릭을 특화하지 말라</h3>
<p>제네릭 클래스나 제네릭 메서드를 설계할 때는 <strong>컴파일러의 오버로드 선택 규칙을 명확히 이해하고, 베이스 클래스나 인터페이스에 대한 제네릭 특화를 피해야 한다.</strong>  </p>
<h4 id="오버로드된-메서드-선택-과정"><strong>오버로드된 메서드 선택 과정</strong></h4>
<p>다음 코드에서 <code>WriteMessage(d)</code> 호출 시, <code>d</code>는 <code>MyDerived</code> 타입이다.  </p>
<pre><code class="language-csharp">public class MyBase { }

public interface IMessageWriter
{
    void WriteMessage();
}

public class MyDerived : MyBase, IMessageWriter
{
    void IMessageWriter.WriteMessage() =&gt;
        Console.WriteLine(&quot;Inside MyDerived.WriteMessage&quot;);
}

static void WriteMessage(MyBase b)
{
    Console.WriteLine(&quot;Inside WriteMessage(MyBase)&quot;);
}

static void WriteMessage&lt;T&gt;(T obj)
{
    Console.Write(&quot;Inside WriteMessage&lt;T&gt;(T): &quot;);
    Console.WriteLine(obj.ToString());
}

MyDerived d = new MyDerived();
Console.WriteLine(&quot;Calling Program.WriteMessage&quot;);
WriteMessage(d);</code></pre>
<h4 id="출력-결과"><strong>출력 결과</strong></h4>
<pre><code>Calling Program.WriteMessage
Inside WriteMessage&lt;T&gt;(T): MyDerived</code></pre><h4 id="왜-제네릭-메서드가-선택되었을까"><strong>왜 제네릭 메서드가 선택되었을까?</strong></h4>
<p>컴파일러는 <strong>가장 구체적인(더 정확히 일치하는) 시그니처</strong>를 선택하는데, 여기서 두 후보가 있다.  </p>
<ol>
<li><code>WriteMessage(MyBase b)</code>: <code>d</code>가 <code>MyBase</code>로 <strong>암시적 형변환</strong>이 필요함.  </li>
<li><code>WriteMessage&lt;T&gt;(T obj)</code>: <code>T</code>를 <code>MyDerived</code>로 <strong>정확히 치환</strong>할 수 있음.  </li>
</ol>
<p>→ <strong>제네릭 메서드가 더 정확히 일치</strong>하기 때문에 선택된다.  </p>
<hr />
<h2 id="❌-베이스-클래스나-인터페이스에-대한-제네릭-특화는-왜-피해야-할까"><strong>❌ 베이스 클래스나 인터페이스에 대한 제네릭 특화는 왜 피해야 할까?</strong></h2>
<ol>
<li><p><strong>예상과 다르게 동작할 수 있음</strong>  </p>
<ul>
<li>개발자는 <code>WriteMessage(MyBase)</code>가 호출될 것이라 예상할 수 있지만, 실제로는 <code>WriteMessage&lt;T&gt;(T)</code>가 선택됨.  </li>
<li>이로 인해 <strong>제네릭 메서드가 특정 타입을 의도치 않게 가로채는 문제</strong>가 발생할 수 있다.  </li>
</ul>
</li>
<li><p><strong>유지보수성이 떨어짐</strong>  </p>
<ul>
<li>새로운 타입이 추가될 때마다, 제네릭 특화가 예기치 않은 동작을 유발할 가능성이 있음.  </li>
<li>인터페이스를 구현하는 모든 타입이 특화 대상이 되면, 의도치 않은 코드 흐름이 발생할 수 있다.  </li>
</ul>
</li>
<li><p><strong>제네릭이 아니라 다형성을 활용해야 할 문제일 수 있음</strong>  </p>
<ul>
<li>원래 <code>WriteMessage(MyBase)</code> 같은 <strong>다형성을 활용하는 구조가 더 자연스러울 수 있음.</strong>  </li>
<li>불필요한 제네릭 특화는 가독성과 유지보수성을 해칠 수 있다.  </li>
</ul>
</li>
</ol>
<hr />
<h3 id="✅-결론-제네릭-특화보다-다형성을-고려하라"><strong>✅ 결론: &quot;제네릭 특화보다 다형성을 고려하라&quot;</strong></h3>
<ul>
<li><strong>제네릭 메서드는 가장 구체적인 타입을 우선 선택한다.</strong>  </li>
<li><strong>베이스 클래스나 인터페이스에 대해 제네릭 특화를 남발하면, 오버로드 동작이 예측하기 어려워진다.</strong>  </li>
<li><strong>다형성을 활용하는 설계가 더 적절할 수도 있다.</strong>  </li>
</ul>
<p>➡ <strong>제네릭 특화보다는 다형성을 우선 고려하라!</strong></p>