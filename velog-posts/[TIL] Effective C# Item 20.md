<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h2 id="icomparablet와-icomparert를-이용한-객체의-선후-관계-정의">IComparable와 IComparer를 이용한 객체의 선후 관계 정의</h2>
<p>객체의 선후 관계를 정의하는 것은 컬렉션의 정렬과 검색에 필수적이다. .NET Framework는 이를 위해 IComparable와 IComparer 두 인터페이스를 제공한다.</p>
<h3 id="icomparable-인터페이스">IComparable 인터페이스</h3>
<p>IComparable 인터페이스는 CompareTo() 메서드 하나만을 포함하며, C 라이브러리의 strcmp 함수 구현 방식을 따른다. 최신 API들은 주로 IComparable를 사용하지만, 이전 API들과의 호환성을 위해 IComparable도 함께 구현하는 것이 좋다.</p>
<pre><code class="language-csharp">public struct Customer : IComparable&lt;Customer&gt;, IComparable
{
    private readonly string name;

    public int CompareTo(Customer other) =&gt; name.CompareTo(other.name);

    int IComparable.CompareTo(object obj)
    {
        if (!(obj is Customer))
            throw new ArgumentException(&quot;Argument is not a Customer&quot;, &quot;obj&quot;);

        Customer other = (Customer)obj;
        return this.CompareTo(other);
    }
}</code></pre>
<h3 id="타입-안전성">타입 안전성</h3>
<p>IComparable는 타입 안전성을 제공한다. 다른 타입과의 비교 시도는 컴파일 오류를 발생시킨다. 반면, IComparable.CompareTo(object right)를 사용할 때는 명시적 캐스팅이 필요하다.</p>
<h3 id="관계-연산자-오버로딩">관계 연산자 오버로딩</h3>
<p>표준 관계 연산자를 오버로딩하여 객체 간 비교를 더욱 직관적으로 만들 수 있다.</p>
<pre><code class="language-csharp">public static bool operator &lt; (Customer left, Customer right) =&gt;
    left.CompareTo(right) &lt; 0;
public static bool operator &lt;= (Customer left, Customer right) =&gt;
    left.CompareTo(right) &lt;= 0;
public static bool operator &gt; (Customer left, Customer right) =&gt;
    left.CompareTo(right) &gt; 0;
public static bool operator &gt;= (Customer left, Customer right) =&gt;
    left.CompareTo(right) &gt;= 0;</code></pre>
<h3 id="comparisont-델리게이트">Comparison 델리게이트</h3>
<p>.NET Framework의 제네릭 기능 도입 이후, 많은 API가 Comparison 델리게이트를 사용하여 정렬 작업을 수행한다. 이를 통해 다양한 정렬 기준을 쉽게 구현할 수 있다.</p>
<pre><code class="language-csharp">public static Comparison&lt;Customer&gt; CompareByRevenue =&gt; 
    (left, right) =&gt; left.revenue.CompareTo(right.revenue);</code></pre>
<h3 id="icomparer-인터페이스">IComparer 인터페이스</h3>
<p>IComparer를 사용하여 추가적인 정렬 기준을 만들 수 있다. 이는 제네릭이 도입되기 전부터 사용되던 방식이다.</p>
<pre><code class="language-csharp">private class RevenueComparer : IComparer&lt;Customer&gt;
{
    int IComparer&lt;Customer&gt;.Compare(Customer left, Customer right) =&gt;
        left.revenue.CompareTo(right.revenue);
}

private static Lazy&lt;RevenueComparer&gt; revComp =
    new Lazy&lt;RevenueComparer&gt;(() =&gt; new RevenueComparer());

public static IComparer&lt;Customer&gt; RevenueCompare =&gt; revComp.Value;</code></pre>
<p>이러한 다양한 방법을 통해 객체의 선후 관계를 효과적으로 정의하고 활용할 수 있다.</p>