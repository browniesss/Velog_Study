<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="아이템-23-타입-매개변수에-대해-메서드-제약-조건을-설정하려면-델리게이트를-활용하라">아이템 23: 타입 매개변수에 대해 메서드 제약 조건을 설정하려면 델리게이트를 활용하라</h3>
<p>제네릭 클래스를 설계할 때, 특정 메서드(예를 들어, <code>Add()</code>처럼 두 값을 더하는 메서드)를 반드시 지원하는 타입만을 사용하고 싶을 수 있습니다. 전통적인 방법은 해당 메서드를 포함하는 인터페이스(<code>IAdd&lt;T&gt;</code> 등)를 정의하고, 이를 구현한 클래스만을 허용하는 것이지만, 이 방식은 사용자에게 추가적인 클래스를 구현하도록 강요하여 불편함을 초래합니다.</p>
<p>이 문제를 해결하기 위한 한 가지 접근법은 <strong>델리게이트(delegate)</strong> 를 활용하는 것입니다. 즉, 제약 조건 대신에 원하는 메서드의 시그니처(원형)에 부합하는 델리게이트를 매개변수로 받아서, 실제 연산을 수행하도록 하는 방식입니다. 이렇게 하면 사용자는 인터페이스를 구현할 필요 없이 람다식이나 메서드 참조를 통해 원하는 기능을 전달할 수 있습니다.</p>
<p>예를 들어, 두 값을 더하는 <code>Add()</code> 메서드를 가진 제네릭 메서드를 작성할 때, .NET의 <code>System.Func&lt;T1, T2, TOutput&gt;</code> 델리게이트를 활용할 수 있습니다.</p>
<pre><code class="language-csharp">public static class Example
{
    public static T Add&lt;T&gt;(T left, T right, Func&lt;T, T, T&gt; AddFunc) =&gt;
        AddFunc(left, right);
}</code></pre>
<p>위 코드에서 <code>Example.Add</code>는 두 값과 이들을 더하는 방법을 정의하는 <code>AddFunc</code> 델리게이트를 인자로 받습니다. 사용자는 아래와 같이 간단한 람다식을 전달하여 덧셈 로직을 지정할 수 있습니다.</p>
<pre><code class="language-csharp">int a = 6;
int b = 7;
int sum = Example.Add(a, b, (x, y) =&gt; x + y);</code></pre>
<p>이 방법의 장점은 <strong>유연성</strong>에 있습니다. 타입에 대한 구체적인 제약 조건 없이, 원하는 동작을 델리게이트로 전달할 수 있으므로 코드의 재사용성과 확장성이 높아집니다.</p>
<hr />
<p>또 다른 예로, 서로 다른 시퀀스의 데이터를 결합하여 새로운 객체 시퀀스를 생성하는 경우를 생각해봅니다. 예를 들어, 두 개의 <code>double</code> 배열에서 각각 X, Y 좌표 값을 읽어 <code>Point</code> 객체를 생성하는 경우입니다.</p>
<p>먼저, 불변(immutable) 타입인 <code>Point</code> 클래스를 정의합니다.</p>
<pre><code class="language-csharp">public class Point
{
    public double X { get; }
    public double Y { get; }

    public Point(double x, double y)
    {
        this.X = x;
        this.Y = y;
    }
}</code></pre>
<p>만약 두 시퀀스를 순차적으로 읽어 (X, Y) 쌍으로 묶으려 한다면, 생성자를 통해 <code>Point</code> 객체를 만들어야 합니다. 이때도 매개변수를 가진 생성자를 제약 조건으로 강제할 방법은 없으므로, 두 값을 받아 <code>Point</code> 객체를 반환하는 델리게이트를 사용하면 효과적입니다.</p>
<p>이를 위해 아래와 같이 <code>Zip</code> 메서드를 작성할 수 있습니다.</p>
<pre><code class="language-csharp">public static IEnumerable&lt;TOutput&gt; Zip&lt;T1, T2, TOutput&gt;(
    IEnumerable&lt;T1&gt; left, IEnumerable&lt;T2&gt; right,
    Func&lt;T1, T2, TOutput&gt; generator)
{
    using IEnumerator&lt;T1&gt; leftSequence = left.GetEnumerator();
    using IEnumerator&lt;T2&gt; rightSequence = right.GetEnumerator();

    while (leftSequence.MoveNext() &amp;&amp; rightSequence.MoveNext())
    {
        yield return generator(leftSequence.Current, rightSequence.Current);
    }
}</code></pre>
<p><code>Zip</code> 메서드는 두 시퀀스의 요소를 동시에 순회하면서, 전달받은 <code>generator</code> 델리게이트를 호출하여 두 값을 하나의 결과(<code>TOutput</code>)로 변환합니다. 사용 예는 다음과 같습니다.</p>
<pre><code class="language-csharp">double[] xValues = { 0, 1, 2 };
double[] yValues = { 0, 1, 2 };

List&lt;Point&gt; values = new List&lt;Point&gt;(
    Zip(xValues, yValues, (x, y) =&gt; new Point(x, y)));</code></pre>
<p>이와 같이 델리게이트를 활용하면, 타입 매개변수에 대해 보다 세밀한 동작을 외부에서 정의할 수 있으며, 제약 조건의 한계를 우회하면서도 원하는 기능을 구현할 수 있습니다. 델리게이트를 통한 설계는 코드의 <strong>유연성</strong>과 <strong>재사용성</strong>을 높이는 좋은 기법 중 하나입니다.</p>
<hr />
<p>요약하자면, 제네릭 타입에 특정 메서드의 구현을 강제하고 싶을 때, 인터페이스 제약 조건 대신 델리게이트를 매개변수로 사용하여 메서드의 시그니처에 맞는 기능을 외부에서 제공하도록 할 수 있습니다. 이는 사용자가 간단하게 람다식이나 메서드 참조를 통해 원하는 동작을 전달할 수 있도록 하여, 코드의 단순함과 확장성을 동시에 확보하는 방법입니다.</p>