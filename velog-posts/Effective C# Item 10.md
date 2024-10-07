<p>Effective C# Chapter 1. Item 10 항목의 내용을 정리한 글입니다.
<img height="25%" src="https://velog.velcdn.com/images/kl45678/post/3e98c70c-e754-4b1f-9dd6-4302d59e2679/image.png" width="25%" /></p>
<h3 id="아이템-10--베이스-클래스가-업그레이드된-경우에만-new-한정자를-사용하라">아이템 10 : 베이스 클래스가 업그레이드된 경우에만 new 한정자를 사용하라</h3>
<p>베이스 클래스에서 virtual로 선언되지 않은 메서드를 하위 클래스에서 new 한정자로 재정의할 수 있지만, 이는 메서드의 동작 방식을 모호하게 만들 수 있다. 대부분의 개발자는 베이스 클래스와 하위 클래스의 메서드가 동일한 작업을 수행할 것으로 기대하기 때문에, 이러한 재정의는 혼란을 초래할 수 있다.</p>
<pre><code class="language-cs">object c = MakeObject();

// MyClass 타입의 참조를 이용하여 메서드를 호출한다.
MyClass cl = c as MyClass;
cl.MagicMethod();

// MyOtherClass 타입의 참조를 이용하여 메서드를 호출한다.
MyOtherClass cl2 = c as MyOtherClass;
cl2.MagicMethod();</code></pre>
<p>다음과 같이 MyOtherClass에서 new 한정자를 이용하여 MagicMethod()를 재정의했다면 두 메서드의 호출 결과가 달라진다.</p>
<pre><code class="language-cs">public class MyClass
{
    public void MagicMethod()
    {
        Console.WriteLine(&quot;MyClass&quot;);
    }
}

public class MyOtherClass : MyClass
{
    // MagicMethod를 재정의
    public new void MagicMethod()
    {
        Console.WriteLine(&quot;MyOtherClass&quot;);
    }
}</code></pre>
<p>위 처럼 코드를 작성하면, 개발자들은 혼란스러울 수 밖에 없다.
사실 new 한정자는 비가상 메서드를 가상 메서드로 만드는 것이 아니라 클래스의 명명 범위(naming scope) 내 새로운 메서드를 추가하는 역할을 수행한다.</p>
<p>비가상 메서드는 정적으로 바인딩되므로 MyClass.MagicMethod()를 호출하는 코드는 정확히 이 메서드를 호출한다. 런타임에 파생 클래스에서 새롭게 정의하고 있는 메서드가 있는지 찾지 않는다는 것이다. 반면, 가상 메서드는 동적으로 바인딩되므로 런타임에 객체의 타입이 무엇이냐에 따라 그에 부합하는 메서드를 호출한다.</p>
<p>new 한정자를 활용해도 좋은 경우는 베이스 클래스에서 이미 사용하고 있는 메서드를 재정의하여 완전히 새로운 베이스 클래스를 만들어야 하는 경우 정도이다. 아래 예시를 통해 확인해보자.</p>
<pre><code class="language-cs">public class MyWidget : BaseWidget
{
    public void NormalizeValues()
    {

    }
}</code></pre>
<p>많은 고객이 MyWidget 클래스를 사용 중인 상황에서 BaseWidget의 개발사가 새로운 버전을 출시했다고 해보자. MyWidget을 빌드했지만, BaseWidget에 NormalizeValues()라는 새로운 메서드가 추가된 바람에 빌드가 실패하는 경우를 생각해보자.</p>
<pre><code class="language-cs">public class BaseWidget
{
    public void NormalizeValues()
    {
    }
}</code></pre>
<p>문제의 원인은 베이스 클래스에 동일 이름의 메서드가 추가됐기 때문이다. 이 문제를 해결하는데에는 2가지 방법이 있다.</p>
<ul>
<li>MyWidget 클래스에서 정의한 NormalizeValues() 메서드의 이름을 변경하는 것</li>
<li>메서드의 이름을 변경하는 대신 new 한정자를 사용하는 방법</li>
</ul>
<p>MyWidget 클래스를 사용하는 모든 코드를 수정할 수 있다면, 메서드 이름을 바꾸는 것이 장기적으로 좋다. 하지만 이 클래스를 이미 널리 공개해 누가 사용하는지 알 수 없다면, 기존 메서드명을 유지하면서 new 한정자를 사용하는 것이 나을 수 있다. new 한정자는 베이스 클래스가 업데이트되어 파생 클래스와 메서드 이름이 충돌할 때 유용하게 문제를 해결할 수 있다.</p>
<p>그러나 시간이 지나면서 BaseWidget의 NormalizeValues() 메서드를 사용하는 사용자가 늘어나면, 이름은 같지만 동작이 다른 메서드들이 생길 수 있다. 따라서 new 한정자를 사용할 때는 신중히 고민해야 한다.</p>
<p>new 한정자는 특별한 상황에서만 사용해야 한다. 무분별하게 사용하면 메서드 호출 시 혼란을 초래할 수 있다. 베이스 클래스가 업그레이드되어 이름 충돌이 발생할 때는 new 한정자를 고려할 수 있지만, 그 외에는 절대로 사용하지 않는 것이 좋다.</p>