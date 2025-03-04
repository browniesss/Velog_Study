<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="아이템-25-타입-매개변수로-인스턴스-필드를-만들-필요가-없다면-제네릭-메서드를-정의하라">아이템 25: 타입 매개변수로 인스턴스 필드를 만들 필요가 없다면 제네릭 메서드를 정의하라</h3>
<p>타입 매개변수로 인스턴스 필드를 만들어야 하는 경우에는 제네릭 클래스를 작성하고, 그렇지 않은 경우에는 일반 클래스 내 제네릭 메서드를 작성해야한다.</p>
<pre><code>public static class Utils&lt;T&gt;
{
    public static T Max(T left, T right) =&gt; 
        Comparer&lt;T&gt;.Default.Compare(left, right) &lt; 0 ? right : left;

    public static T Min(T left, T right) =&gt;
        Comparer&lt;T&gt;.Default.Compare(left, right) &lt; 0 ? left : right;
}</code></pre><p>위와 같이 제네릭 클래스로 유틸 클래스를 정의할 때는 실제 사용할 때 아래처럼 직접 타입 매개변수를 지정해야하는 번거로움이 있을 뿐더러, .NET Framework에 포함된 많은 Min, Max함수들도 사용하지 못하게 된다.
<code>string sMax = Utils&lt;string&gt;.Max(foo, bar);</code></p>
<p>그렇기에 아래처럼 일반 클래스 내 제네릭 메서드를 구현하면 좀 더 편하다.</p>
<pre><code>public static class Utils
{
    public static T Max&lt;T&gt;(T left, T right) =&gt; ~~~

    public static double Max(double left, double right) =&gt; Math.Max(left, right);
}</code></pre><h4 id="결론">결론</h4>
<p>일반 클래스 내 제네릭 메서드를 만들어서 타입별로 메서드가 특화되도록 코드를 작성하는게 좋다.
무조건 제네릭 메서드를 사용하는 게 장점만 있는건 아니고, 다음 2가지 경우에는 반드시 제네릭 클래스를 만들어야 한다.</p>
<ol>
<li>클래스 내 타입 매개변수로 주어진 타입으로 내부 상태를 유지해야 하는 경우.</li>
<li>제네릭 인터페이스를 구현하는 클래스를 만들어야 할 경우.</li>
</ol>
<p>위 경우가 아니라면 일반 클래스 내 제네릭 메서드를 구현하는 편이 향후 유지보수에도 편리하고 세밀하게 내용을 수정할 때도 도움이 된다.</p>