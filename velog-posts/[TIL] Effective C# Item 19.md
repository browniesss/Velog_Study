<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h2 id="아이템-19-런타임에-타입을-확인하여-최적의-알고리즘을-사용하라">아이템 19: 런타임에 타입을 확인하여 최적의 알고리즘을 사용하라</h2>
<p>제네릭 타입은 타입 매개변수를 통해 쉽게 재사용할 수 있지만, 이로 인해 구체적인 타입의 장점을 잃고 최적화된 알고리즘을 사용하기 어려워질 수 있습니다. C#은 이러한 점을 고려하여 설계되었으며, 특정 타입에 대해 더 효율적인 알고리즘이 있다면 해당 타입을 직접 사용하는 것이 좋습니다.</p>
<p>제네릭의 인스턴스화는 컴파일 타임의 타입만을 고려하므로, 런타임 타입 확인을 통해 최적화를 수행할 수 있습니다. 이를 위해 제약 조건을 설정하는 것이 항상 효과적인 방법은 아닙니다.</p>
<h3 id="reverseenumerable-예제">ReverseEnumerable 예제</h3>
<p>특정 타입의 시퀀스를 역순으로 순회하는 <code>ReverseEnumerable&lt;T&gt;</code> 클래스를 예로 들어보겠습니다. 이 클래스는 <code>IEnumerable&lt;T&gt;</code>를 구현하며, 내부적으로 <code>ReverseEnumerator</code>를 사용합니다.</p>
<pre><code class="language-csharp">public sealed class ReverseEnumerable&lt;T&gt; : IEnumerable&lt;T&gt;
{
    private class ReverseEnumerator : IEnumerator&lt;T&gt;
    {
        // ... (구현 생략)
    }

    IEnumerable&lt;T&gt; sourceSequence;
    IList&lt;T&gt; originalSequence;

    public ReverseEnumerable(IEnumerable&lt;T&gt; srcSequence)
    {
        sourceSequence = srcSequence;
    }

    // ... (나머지 구현 생략)
}</code></pre>
<p>이 코드는 잘 동작하지만, 대부분의 컬렉션이 랜덤 액세스를 지원하므로 비효율적일 수 있습니다. <code>IList&lt;T&gt;</code>를 지원하는 경우 복제본을 만들 필요가 없으므로, 다음과 같이 개선할 수 있습니다</p>
<pre><code class="language-csharp">public ReverseEnumerable(IEnumerable&lt;T&gt; srcSequence)
{
    sourceSequence = srcSequence;
    originalSequence = srcSequence as IList&lt;T&gt;;
}</code></pre>
<h3 id="런타임-타입-확인을-통한-최적화">런타임 타입 확인을 통한 최적화</h3>
<p>더 나아가, <code>ICollection&lt;T&gt;</code>를 구현한 컬렉션의 경우 <code>Count</code> 속성을 활용하여 저장소 공간을 미리 초기화할 수 있습니다</p>
<pre><code class="language-csharp">public IEnumerator&lt;T&gt; GetEnumerator()
{
    if (originalSequence == null)
    {
        if (sourceSequence is ICollection&lt;T&gt; source)
        {
            originalSequence = new List&lt;T&gt;(source.Count);
        }
        else
        {
            originalSequence = new List&lt;T&gt;();
        }

        foreach (T item in sourceSequence)
        {
            originalSequence.Add(item);
        }
    }

    return new ReverseEnumerator(originalSequence);
}</code></pre>
<h3 id="특수-케이스-처리-string">특수 케이스 처리: string</h3>
<p><code>string</code>은 특별한 경우로, <code>IList&lt;char&gt;</code>를 구현하지 않지만 랜덤 액세스가 가능합니다. 이를 위해 별도의 <code>ReverseStringEnumerator</code>를 구현하고, <code>GetEnumerator</code> 메서드에서 특별히 처리할 수 있습니다</p>
<pre><code class="language-csharp">public IEnumerator&lt;T&gt; GetEnumerator()
{
    if (sourceSequence is string str)
    {
        return new ReverseStringEnumerator(str) as IEnumerator&lt;T&gt;;
    }

    // ... (나머지 구현)
}</code></pre>
<p>이러한 방식으로 제네릭 클래스 내에서 타입별로 최적화된 구현을 숨겨둘 수 있습니다. 제네릭을 정의할 때는 컴파일러가 모든 것을 이해하고 있다고 가정해서는 안 되며, 런타임 타입 확인을 통해 최적의 알고리즘을 선택하는 것이 중요합니다.</p>