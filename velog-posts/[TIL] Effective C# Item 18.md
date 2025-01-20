<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h4 id="아이템-18-반드시-필요한-제약-조건만-설정하라">아이템 18: 반드시 필요한 제약 조건만 설정하라</h4>
<p>제네릭 타입 매개변수에 대한 제약 조건은 타입의 유형을 제한하는 방법으로, 최소한으로 설정해야 한다. 과도한 제약 조건은 사용자에게 불필요한 추가 작업을 요구할 수 있다.</p>
<p>제약 조건을 설정하면 컴파일러는 System.Object의 public 메서드 이상을 타입 매개변수에 기대할 수 있게 된다. 제약 조건이 없으면 컴파일러는 타입 매개변수를 System.Object의 최소 기능만 제공하는 타입으로 가정한다.</p>
<p>제약 조건의 주요 용도:</p>
<ol>
<li>컴파일러와 다른 개발자에게 제네릭 타입에 대한 가정을 알려준다.</li>
<li>컴파일러가 제네릭 타입 작성 시 타입 매개변수의 기능을 가정할 수 있게 한다.</li>
<li>사용자가 올바른 타입을 지정했는지 컴파일 타임에 확인할 수 있게 한다.</li>
</ol>
<p>제약 조건 대신 런타임 테스트를 사용할 수 있지만, 제약 조건을 설정하면 코드가 간결해지고 컴파일 타임에 오류를 확인할 수 있다.</p>
<pre><code class="language-csharp">// 제약 조건 없이 런타임 테스트 사용
public static bool AreEqual&lt;T&gt;(T left, T right)
{
    if (left == null)
        return right == null;

    if (left is IComparable&lt;T&gt;)
    {
        IComparable&lt;T&gt; lval = left as IComparable&lt;T&gt;;
        if (right is IComparable&lt;T&gt;)
            return lval.CompareTo(right) == 0;
        else
            throw new ArgumentException(&quot;Type does not implement IComparable&lt;T&gt;&quot;, nameof(right));
    }
    else
    {
        throw new ArgumentException(&quot;Type does not implement IComparable&lt;T&gt;&quot;, nameof(left));
    }
}

// 제약 조건 사용
public static bool AreEqaul2&lt;T&gt;(T left, T right)
    where T : IComparable&lt;T&gt; =&gt; left.CompareTo(right) == 0;</code></pre>
<p>제약 조건을 최소화하는 방법:</p>
<ol>
<li>제네릭 타입 내에서 반드시 필요한 기능만 제약 조건으로 설정한다.</li>
<li><code>new()</code> 대신 <code>default()</code>를 사용하여 제약 조건을 줄일 수 있다.</li>
</ol>
<pre><code class="language-csharp">// default() 사용 예시
public static T FirstOrDefault&lt;T&gt;(this IEnumerable&lt;T&gt; sequence, Predicate&lt;T&gt; test)
{
    foreach (T value in sequence)
        if (test(value))
            return value;
    return default(T);
}

// new() 제약 조건 사용 예시
public delegate T FactoryFunc&lt;T&gt;();

public static T Factory&lt;T&gt;(FactoryFunc&lt;T&gt; makeANewT) where T : new()
{
    T rVal = makeANewT();
    if (rVal == null)
        return new T();
    else
        return rVal;
}</code></pre>
<p><code>default()</code>는 값 타입에 대해 0을, 참조 타입에 대해 null을 반환한다. <code>new()</code>와 달리 <code>default()</code>는 실제 객체를 생성하지 않으므로 주의가 필요하다.</p>
<p>제약 조건을 적절히 사용하면 코드의 안정성과 성능을 향상시킬 수 있지만, 과도한 제약 조건은 제네릭 타입의 사용성을 떨어뜨릴 수 있다. 따라서 필요한 최소한의 제약 조건만을 설정하는 것이 중요하다.</p>