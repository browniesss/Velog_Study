<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h4 id="아이템-21-타입-매개변수가-idisposable을-구현한-경우를-대비하여-제네릭-클래스를-작성하라">아이템 21: 타입 매개변수가 IDisposable을 구현한 경우를 대비하여 제네릭 클래스를 작성하라</h4>
<p>제약 조건은 런타임 오류를 컴파일 타임 오류로 대체하고, 타입 매개변수로 사용할 수 있는 타입을 명확히 규정한다. 하지만 제약 조건은 타입 매개변수가 무엇을 해야 하는지만 규정할 수 있고, 무엇을 해서는 안 되는지는 정의할 수 없다. 대부분의 경우 이는 문제가 되지 않지만, 타입 매개변수로 지정하는 타입이 IDisposable을 구현하고 있다면 특별한 추가 작업이 필요하다.</p>
<p>예를 들어, 다음과 같은 제네릭 클래스가 있다고 가정해보자:</p>
<pre><code class="language-csharp">public class EngineDriverOne&lt;T&gt; where T : IEngine, new()
{
    public void GetThingsDone()
    {
        T driver = new T();
        driver.DoWork();
    }
}</code></pre>
<p>이 경우, T가 IDisposable을 구현한 타입이라면 리소스 누수가 발생할 수 있다. 이를 방지하기 위해 다음과 같이 코드를 수정할 수 있다:</p>
<pre><code class="language-csharp">public void GetThingsDone()
{
    T driver = new T();
    using (driver as IDisposable)
    {
        driver.DoWork();
    }
}</code></pre>
<p>이 방식을 사용하면 T가 IDisposable을 구현했을 경우에만 Dispose() 메서드가 호출된다.</p>
<p>또 다른 방법으로는 IDisposable 인터페이스를 직접 구현하는 것이다:</p>
<pre><code class="language-csharp">public sealed class EngineDriver2&lt;T&gt; : IDisposable where T : IEngine, new()
{
    private Lazy&lt;T&gt; driver = new Lazy&lt;T&gt;(() =&gt; new T());

    public void GetThingsDone() =&gt; driver.Value.DoWork();

    public void Dispose()
    {
        if (driver.IsValueCreated)
        {
            var resource = driver.Value as IDisposable;
            resource?.Dispose();
        }
    }
}</code></pre>
<p>이 예시에서는 Lazy를 사용하여 초기화를 지연시키고, sealed 클래스로 구현하여 표준 Dispose 패턴 전체를 구현하지 않도록 했다.</p>
<p>마지막으로, 객체의 소유권을 외부 클래스로 전담하는 방법도 있다:</p>
<pre><code class="language-csharp">public sealed class EngineDriver&lt;T&gt; where T : IEngine
{
    private T driver;

    public EngineDriver(T driver)
    {
        this.driver = driver;
    }

    public void GetThingsDone()
    {
        driver.DoWork();
    }
}</code></pre>
<p>이 방식을 사용하면 EngineDriver 클래스는 driver 객체의 생명주기를 관리할 필요가 없다.</p>
<p>결론적으로, 제네릭 클래스를 작성할 때는 항상 방어적으로 타입 매개변수가 IDisposable을 구현하고 있는지 확인하고 적절히 처리하는 것이 중요하다. 이를 통해 리소스 누수를 방지하고 안전한 코드를 작성할 수 있다.</p>