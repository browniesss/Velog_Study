<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="아이템-22-공변성과-반공변성을-지원하라">아이템 22: 공변성과 반공변성을 지원하라</h3>
<p>타입의 가변성인 공변성과 반공변성은 특정 타입의 객체를 다른 타입으로 변환할 수 있는 특성을 말합니다. 이를 지원하려면 제네릭 인터페이스나 델리게이트의 정의에서 공변/반공변을 나타내는 데코레이터를 추가해야 합니다. 공변성 및 반공변성을 지원하면 API를 보다 유연하고 안전하게 사용할 수 있습니다.</p>
<h4 id="공변성">공변성</h4>
<ul>
<li>X를 Y로 변환할 수 있다면, <code>C&lt;X&gt;</code>를 <code>C&lt;Y&gt;</code>로 바꿔서 사용할 수 있습니다. 이때 <code>C&lt;T&gt;</code>는 공변입니다.</li>
</ul>
<h4 id="반공변성">반공변성</h4>
<ul>
<li>Y를 X로 변환할 수 있다면, <code>C&lt;X&gt;</code>를 <code>C&lt;Y&gt;</code>로 바꿔서 사용할 수 있습니다. 이때 <code>C&lt;T&gt;</code>는 반공변입니다.</li>
</ul>
<p>예를 들어, <code>List&lt;Planet&gt;</code>(CelestialBody를 상속받음) 타입의 객체를 메서드에 전달할 수 있습니다:</p>
<pre><code class="language-csharp">public static void CovariantGeneric(IEnumerable&lt;CelestialBody&gt; baseItems)
{
    foreach (var thing in baseItems)
        Console.WriteLine(&quot;~~~&quot;);
}</code></pre>
<p>이게 가능한 이유는 <code>IEnumerable&lt;T&gt;</code>를 정의할 때 <code>T</code>를 <code>out</code>으로 선언했기 때문입니다:</p>
<pre><code class="language-csharp">public interface IEnumerable&lt;out T&gt; : IEnumerable
{
    new IEnumerator&lt;T&gt; GetEnumerator();
}

public interface IEnumerator&lt;out T&gt; : IDisposable, IEnumerator
{
    new T Current { get; }
}</code></pre>
<p>여기서 <code>out</code> 데코레이터는 타입 매개변수 <code>T</code>를 출력 위치에서만 사용하겠다는 의미입니다. 출력 위치는 함수의 반환값, 속성의 <code>get</code> 접근자, 델리게이트의 일부 위치 등을 포함합니다.</p>
<p>반공변성은 <code>in</code> 데코레이터를 사용하여 구현합니다. <code>in</code>은 타입 매개변수를 입력 위치에서만 사용한다는 것을 컴파일러에 알려줍니다. 예시로는 <code>.NET Framework</code>의 <code>IComparable&lt;T&gt;</code> 인터페이스가 있습니다:</p>
<pre><code class="language-csharp">public interface IComparable&lt;in T&gt;
{
    int CompareTo(T other);
}</code></pre>
<h3 id="델리게이트의-매개변수에-대한-공변반공변">델리게이트의 매개변수에 대한 공변/반공변</h3>
<p>델리게이트 매개변수에서도 공변/반공변을 사용할 수 있습니다:</p>
<pre><code class="language-csharp">public delegate TResult Func&lt;out TResult&gt;();
public delegate TResult Func&lt;in T, out TResult&gt;(T arg);
public delegate TResult Func&lt;in T1, T2, out TResult&gt;(T1 arg1, T2 arg2);</code></pre>
<p>공변 인터페이스에서 불변 인터페이스를 반환할 수는 없으며, 한 타입에 공변과 반공변을 동시에 지정할 수 없습니다.</p>
<h3 id="결론">결론</h3>
<p>공변성과 반공변성을 정확히 이해하는 것은 쉽지 않지만, 제네릭 인터페이스와 델리게이트에서 <code>in</code>과 <code>out</code> 데코레이터를 사용하면 가변성 관련 오류를 컴파일러가 사전에 체크할 수 있습니다. 따라서 제네릭을 정의할 때는 이러한 데코레이터를 사용하는 것이 좋습니다. 컴파일러는 정의 과정뿐만 아니라 실제 사용 시에도 오류를 확인할 수 있습니다.</p>