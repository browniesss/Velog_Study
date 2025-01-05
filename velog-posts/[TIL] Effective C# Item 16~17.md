<blockquote>
<p>Effective C#을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h4 id="아이템-16-생성자-내에서는-절대로-가상-함수를-호출하지-말라">아이템 16: 생성자 내에서는 절대로 가상 함수를 호출하지 말라.</h4>
<p>어떤 타입이든, 생성자가 수행을 완료하기 전까지는 객체가 완전히 생성되었다고 보기 어렵기에, 생성자 내 가상 함수를 호출하면 예상처럼 동작하지 않는다.</p>
<pre><code>class B
{
    protected B()
    {
        VFunc();
    }

    protected virtual void VFunc()
    {
        Console.WriteLine(&quot;VFunc in B&quot;);
    }
}

class Derived : B
{
    private readonly string msg = &quot;Set by initializer&quot;;

    public Derived(string msg)
    {
        this.msg = msg;
    }

    protected override void VFunc()
    {
        Console.WriteLine(msg);
    }

    public static void Main()
    {
        var d = new Derived(&quot;Constructed in main&quot;);
    }
}</code></pre><p>C#에서 베이스 클래스의 생성자 내에서 가상 함수를 호출하면, 파생 클래스에서 재정의한 함수가 실행되는 이유는 객체가 런타임에 파생 클래스 타입으로 간주되기 때문입니다. 생성자의 본문에 진입하는 순간, 객체는 이미 초기화가 완료된 상태로 간주되지만, 파생 클래스의 생성자 본문은 아직 실행되지 않았습니다. 따라서, 베이스 클래스의 생성자에서 호출되는 가상 함수는 파생 클래스의 함수가 호출되며, 이는 객체가 생성될 때 파생 클래스의 멤버 변수가 초기화되기 전에 발생할 수 있습니다.</p>
<h4 id="아이템-17-표준-dispose-패턴을-구현하라템-17-표준-dispose-패턴을-구현하라">아이템 17: 표준 Dispose 패턴을 구현하라템 17: 표준 Dispose 패턴을 구현하라</h4>
<p>비관리 리소스를 포함하는 객체는 리소스를 명시적으로 정리하는 작업이 중요하다. .NET Framework에서는 비관리 리소스를 정리하는 표준화된 패턴을 사용하고 있으므로, 새로운 타입을 만들 때도 동일한 패턴을 따르는 것이 좋다. 이 패턴은 Dispose 패턴으로 알려져 있다. 이 패턴을 사용하면 개발자들이 IDisposable 인터페이스를 통해 리소스를 안정적으로 정리할 수 있으며, 명시적으로 정리해야 한다는 사실을 잊어버리거나 인지하지 못한 경우에도 finalizer를 통해 올바르게 리소스가 정리될 수 있다. Dispose 패턴은 가비지 컬렉터와 연계되어 동작하며, 불가피한 경우에만 finalizer를 호출하여 성능에 미치는 부정적인 영향을 최소화한다.</p>
<p>상속 계통상 비관리 리소스의 최상위 베이스 클래스는 다음과 같은 작업을 수행해야 한다.</p>
<ul>
<li>리소스를 정리하기 위해서 IDisposable 인터페이스를 구현해야 한다.</li>
<li>멤버 필드로 비관리 리소스를 포함하는 경우에 한해 방어적으로 동작할 수 있도록 finalizer를 추가해야 한다.</li>
<li>Dispose와 finalizer(존재하는 경우)는 실제 리소스 정리 작업을 수행하는 다른 가상 메서드에 작업을 위임하도록 작성돼야 한다. 파생 클래스가 고유의 리소스 정리 작업이 필요한 경우 이 가상 메서드를 재정의 할 수 있도록 하기 위함이다.</li>
</ul>
<p>파생 클래스는 다음 작업을 수행해야 한다.</p>
<ul>
<li>파생 클래스가 고유의 리소스 정리 작업을 수행해야 한다면 베이스 클래스에서 정의한 가상 메서드를 재정의 한다.</li>
<li>멤버 필드로 비관리 리소스를 포함하는 경우에만 finalizer를 추가해야 한다.</li>
<li>베이스 클래스에서 정의하고 있는 가상 함수를 반드시 재호출해야 한다.</li>
</ul>
<p>가장 먼저 알아둬야 하는 것은 비관리 리소스를 포함하는 클래스는 반드시 finalizer를 구현해야 한다는 것이다.</p>
<p>IDisposable 인터페이스는 단 하나의 메서드만을 가진다.</p>
<pre><code>public interface IDisposable
{
    void Dispose();
}</code></pre><p>IDisposable.Dispose() 메서드는 다음 네 가지 작업을 반드시 수행해야 한다.</p>
<ul>
<li>모든 비관리 리소스를 정리한다.</li>
<li>모든 관리 리소스를 정리한다.</li>
<li>객체가 이미 정리되었음을 나타내기 위한 상태 플래그 설정. 앞서 이미 정리된 객체에 대하여 추가로 정리 작업이 요청될 경우 이 플래그를 확인하여 ObjectDisposed 예외를 발생시킨다.</li>
<li>finalizer 호출 회피. 이를 위해 GC.SuppressFinalize(this)를 호출한다.</li>
</ul>
<p>위 부분에서도 개선해야 할 부분들이 있는데, 첫 번째로는 클래스를 상속해서 파생 클래스를 정의하는 경우다. 파생 클래스가 자신이 포함하고 있는 리소스를 정리하는 것은 그렇다 치더라도 베이스 클래스가 포함하고 있는 리소스는 어떻게 정리해야 할까 ? 이러한 문제점을 해결하려면 파생 클래스가 finalizer나 자신만의 IDisposable을 구현할 때 반드시 베이스 클래스에서 구현한 함수를 호출하도록 코드를 작성해야 한다. 또 다른 개선 사항으로는 finalize와 Dispose() 메서드는 일반적으로 동일한 역할을 수행하므로 중복된 코드가 여러 번 나타날 수 있다는 점이다.</p>
<p>이러한 문제를 해결하기 위해서 번거롭더라도 추가적인 작업을 할 수밖에 없다. 표준 Dispose 패턴에서 정의하고 있는 세번째 메서드는 protected로 선언된 가상 헬퍼 함수다. 이 함수의 역할은 리소스 정리를 위한 공통 작업을 수행하고 파생 클래스에게 리소스를 정리할 기회를 주는 것이다. 베이스 클래스를 통해 핵심 구조를 제공하는 것이다. 또한 이 함수는 파생 클래스에서 Dispose()나 finalize를 구현할 때도 도움이 된다. 이 함수의 원형은 다음과 같다.</p>
<pre><code>protected virtual void Dispose(bool isDisposing);</code></pre><p>이 가상 함수를 구현해두면 finalizer와 Dispose 양쪽에서 사용할 수 있다. 게다가 가상 함수이므로 파생 클래스에서 이 메서드를 재정의하여 자신이 소유한 리소스를 정리하는 코드를 작성할 수 있다. 코드의 마지막 부분에서는 반드시 베이스 클래스에서 정의하고 있는 Dispose(bool) 함수를 호출해야한다. Dispose(bool)을 호출할때는 반드시 관리 리소스와 비관리 리소스 모두를 제거할 때는 isDisposing으로 true를 전달하고 비관리 리소스만 정리하려면 false를 전달해야 한다는 것이다.</p>
<p>이 패턴을 구현할 때 참고할 수 있도록 .NET Framework의 코드 일부를 가져와서 짧게 수정해봤다. MyResourceHog 클래스는 IDisposable 인터페이스의 구현 방법과 가상 Dispose(bool) 메서드를 구현하는 방법을 보여준다.</p>
<pre><code>public class MyResourceHog : IDisposable
{
   // 이미 dispose 되었을지를 나타내는 플래그
   private bool alreadyDisposed = false;

   // IDisposable을 구현
   // 가상 Dispose 매서드를 호출하고
   // finalize를 회피하도록 한다.
   public void Dispose()
   {
      Dispose(true);
      GC.SuppressFinalize(this);
   }

   // 가상 Dispose 메서드
   protected virtual void Dispose(bool isDisposing)
   {
      // Dispose 는 한번만 수행되도록 한다.
      if (alreadyDisposed) return;

      if (isDisposing)
      {
         // 여기서 관리 리소스를 정리한다.
      }

      // 여기서 비관리 리소스를 정리한다.

      // disposed 플래그 설정
      alreadyDisposed = true;
   }

   public void ExampleMethod()
   {
      if (alreadyDisposed)
      {
         throw new ObjectDisposedException(&quot;MyResourceHog&quot;,
         &quot;Called Example Method on Disposed Object&quot;);
      }
   }
}</code></pre><p>파생 클래스의 구현 방법에 대해서도 알아보자. 파생 클래스가 추가적인 정리 작업을 수행해야하는 경우 다음과 같이 Dispose(bool) 메서드를 재정의해야 한다.</p>
<pre><code>public class DerivedResourceHog : MyResourceHog
{
   // 자신만의 Disposed 플래그
   private bool disposed = false;

   protected override void Disposed(bool isDisposing)
   {
      // Dispose는 한번만 수행되도록 한다.
      if (disposed) return;

      if (isDisposing)
      {
         // 여기서 관리 리소르를 정리한다.
      }

      // 여기서 비관리 리소스를 정리한다.

      // 베이스 클래스가 자신의 리소스를 정리할 수 있도록 해주어야 한다.
      // 베이스 클래스는 GC.SuppressFinalize()를 호출해야 한다.
      base.Dispose(isDisposing);

      // 파생 클래스의 리소스가 정리되었음을 표시
      disposed = true;
   }
}</code></pre><p>베이스 클래스와 파생 클래스가 각자 자신의 dispose 여부를 나타내기 위해 고유의 플래그를 가졌음을 유심히 살펴봐야 한다. 이는 순전히 방어적으로 코드를 작성하기 위함인데, 플래그를 이중으로 배치하여 베이스 클래스 혹은 파생 클래스의 일부만이 정리된 경우에 혹시 발생할지도 모를 문제를 피하기 위해서다.</p>
<p>앞 코드에서 보면 finalize가 추가되지 않았는데, 비관리 리소스가 포함되는 경우에만 finalizer를 구현해야 한다. 무조건 한다면 성능상의 손해를 감수해야 하기 때문이다.</p>
<p>또 하나 중요한점은 제거 혹은 정리 작업에 있어서 가장 핵심적이고도 중요한 지침 중 하나는 Dispose 메서드 내에서는 리소스 정리 작업만을 수행해야 한다는 것이다.
아래는 잘못된 예시이다.</p>
<pre><code>public class BadClass
{
   // 전역 객체에 대한 참조를 저장한다.
   private static readonly List&lt;BadClass&gt; finalizedList = new List&lt;BadClass&gt;();

   private string msg;

   public BadClass(string msg)
   {
      // 참조를 캐싱한다.
      msg = (string)msg.clone();
   }

   ~BadClass()
   {
      // 이 객체를 다시 리스트에 추가한다.
      // 객체는 다시 도달 가능 상태가 되었으며
      // 더 이상 가비지가 아니다. 살아났다!
      finalizedList.Add(this);
   }
}</code></pre><p>BadClass 객체는 finalizer를 실행할 때 전역 목록에 자신을 추가한다. 이는 결국 자신을 다시 도달 가능한 객체(가비지가 아님)로 변경한 꼴이다. 이렇게 하면 다만 여러가지 문제가 발생한다. 첫째로 가비지 컬렉터는 이 객체에 대해 이미 finalizer를 호출했으므로 더이상 finalizer를 호출할 필요가 없다고 간주한다. 때문에, 정말 다시 삭제하려 해도 finalizer를 호출할 방법이 없다. 둘째로 객체가 살아난 것처럼 보이겠지만 이 객체에 포함된 여타 필드들은 사용할 수가 없다. 가비지 컬렉터는 finalizer큐에 삽입된 객체에서 도달 가능한 다른 객체들 또한 삭제하지 않는다. 하지만 이미 finalize 과정이 완료된 이후라면 이야기가 다르다. 이 경우에는 도달 가능 객체라 하더라도 명백히 가비지로 간주된다. 따라서 BadClass가 가진 필드들은 설사 지금 당장은 메모리를 점유하고 있더라도 언젠가 정리될 것이다.</p>