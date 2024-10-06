<p>Effective C# Chapter 1. Item 8 ~ 9 항목의 내용을 정리한 글입니다.
<img height="25%" src="https://velog.velcdn.com/images/kl45678/post/3e98c70c-e754-4b1f-9dd6-4302d59e2679/image.png" width="25%" /></p>
<h3 id="아이템-8--이벤트-호출-시에는-null-조건-연산자를-사용하라">아이템 8 : 이벤트 호출 시에는 null 조건 연산자를 사용하라</h3>
<p>이벤트를 발생시키는 작업은 언뜻 보면 어려운 부분이 없어 보이나, 이벤트에 결합된 핸들러가 없다면 문제가 발생할 수 있다. 해당 부분을 해결하기 위해 이벤트 핸들러가 결합되어 있는지를 확인하는 코드를 추가한다면 이벤트 핸들러가 결합되어 있는지를 확인하는 코드와 이벤트를 발생시키는 코드 사이에 경쟁 조건이 발생할 가능성이 있기에, C# 6.0에 새롭게 추가된 null 조건 연산자를 사용하면 간단하게 해결할 수 있다.</p>
<blockquote>
<p>경쟁 조건(Race Condition)  : 둘 이상의 입력 또는 조작의 타이밍이나 순서 등이 결과값에 영향을 줄 수 있는 상태</p>
</blockquote>
<pre><code class="language-cs">public class EventSource
{
    private EventHandler&lt;int&gt; Updated;

    public void RaiseUpdates()
    {
        counter++;
        Updated(this, counter);
    }

    private int counter;
}</code></pre>
<p>위 코드는 문제가 있는 코드이다. Update 이벤트에 이벤트 핸들러가 결합되어 있지 않다면 NullReferenceException 예외가 발생한다. 이벤트 핸들러가 결합되지 않은 이벤트는 null 값을 갖기 때문이다.</p>
<pre><code class="language-cs">public void RaiseUpdates()
{
    counter++;
    if (Updated != null)
        Updated(this, counter);
}</code></pre>
<p>위 코드 처럼 유효한 이벤트 핸들러가 결합되어 있는지 확인 후 실행한다면, 대부분 동작하지만 여전히 숨어있는 버그가 있다. if문을 호출하여 Updated 이벤트가 null이 아님을 확인한 뒤, 다른 스레드에서 이벤트 핸들러의 등록을 취소했다고 생각해보면, 이전 코드처럼 NullReferenceException 예외가 발생한다.</p>
<pre><code class="language-cs">public void RaiseUpdates()
{
    counter++;
    var handler = Updated;
    if (handler != null)
        handler(this, counter);
}</code></pre>
<p>위 예제가 바로 .NET과 C#을 이용하여 안전하게 이벤트를 발생시키는 권장코드다. 이 코드는 멀티스레드 환경에서도 안전하게 동작하지만, 코드 가독성 측면에서 문제가 있다.
위 코드의 가독성문제를 null 조건 연산자를 사용하면 간단하게 해결이 가능하다.</p>
<pre><code class="language-cs">public void RaiseUpdates()
{
    counter++;
    Updated?.Invoke(this, counter);
}</code></pre>
<p>'?.' 연산자의 동작 방식은 연산자의 왼쪽을 평가하여, 이 값이 null이 아닌 경우에만 연산자 오른쪽의 표현식을 실행한다. 위 코드는 멀티스레드 환경에서도 안전할 뿐 아니라, 이전 코드보다 더욱 간결하다.</p>
<hr />
<h3 id="아이템-9--박싱과-언박싱을-최소화하라">아이템 9 : 박싱과 언박싱을 최소화하라</h3>
<p>값 타입은 주로 값을 저장할 때 쓰는 저장소이며 다형적이지 못하다. 반면 .NET Framework는 모든 타입의 최상위 타입을 참조 타입인 System.Object로 정의하고 있다. 이 두가지는 서로 양립할 수 없는 것처럼 보이지만, .NET Framework는 방식과 언박싱이라는 방법을 통해 이 두 가지 서로 다른 타입을 이어준다.</p>
<blockquote>
<ul>
<li>박싱 : 값 타입의 객체를 참조 타입으로 변환</li>
</ul>
</blockquote>
<ul>
<li>언박싱 : 참조 타입의 객체를 값 타입으로 변환</li>
</ul>
<p>박싱과 언박싱은 성능에 좋지 않은 영향을 미친다. 때로는 박싱과 언박싱을 수행하는 과정에서 임시 객체가 생성되기도 하여, 이로 인해 예상치 못한 버그가 발생하기도 한다. 따라서 박싱과 언박싱은 가능한 피하는 것이 좋다.</p>
<p>대부분의 경우 .NET 2.0에 추가된 제네릭 클래스와 제네릭 메서드를 사용하면 박싱과 언박싱을 피할 수 있다. 제네릭 관련 기능을 활용하면 값 타입 객체에 대한 불필요한 박싱 작업이 수행되지 않도록 코드를 작성할 수 있다는 것이다. 하지만 여전히 System.Object 타입의 객체를 요구하는 경우가 있어 박싱과 언박싱이 자동으로 수행되어지고 있다.
아래 코드는 박싱의 예시이다.</p>
<pre><code class="language-cs">Console.WriteLine($&quot;A few numbers:{firstNumber}, {secondNumber}, {thirdNumber}&quot;);</code></pre>
<p>보간 문자열을 만드는 위 작업은 실제로 System.Object 객체에 대한 배열을 사용한다. 정숫값은 값 타입이므로 값을 문자열로 변환하기 위한 함수를 호출하려면 반드시 박싱을 수행해야 한다. 3개의 정수를 System.Object 타입으로 변환하는 유일한 방법이 박싱이기 때문이다. 이제 박싱된 객체의 ToString() 메서드를 호출하는데, 동작 방식은 다음과 유사하다.</p>
<pre><code class="language-cs">int i = 25;
object o = i; // 박싱
Console.WriteLine(o.ToString());</code></pre>
<p>보간 문자열 생성의 예에서 박싱과 언박싱 작업은 개별 객체 단위로 반복적으로 이뤄진다. 메서드 내에서 인자로 주어진 객체를 이용하여 문자열을 생성하는 작업은 다음 코드와 유사하다.</p>
<pre><code class="language-cs">object firstParm = 5;
object o = firstParm;
int i = (int)o; // 언박싱
string output = i.ToString();</code></pre>
<p>이 같은 코드를 직접 작성하지는 않을것이다. 이는 컴파일러에서 값 타입의 객체를 System.Object로 변경하기 위해 자동으로 생성하는 코드일 뿐이며, 컴파일러는 값 타입의 객체를 System.Object 타입의 객체로 변환해야 하는 경우 항상 박싱과 언박싱 코드를 자동으로 생성해준다. 하지만 이로인해 발생하는 성능상 취약점을 개선하고 싶다면 WriteLine 메서드에 값 타입의 객체를 직접 전달하지 말고 문자열 인스턴스를 전달하는 것이 좋다.</p>
<pre><code class="language-cs">Console.WriteLine($@&quot;A few Numbers: {firstNumbers.ToString()} ~~~ );</code></pre>
<p>이와 같이 코드를 작성하면 값 타입인 정수 타입 객체를 System.Object 타입으로 변경하는 것을 피할 수 있다.
박싱과 언박싱을 피할 수 있는 비교적 손쉬운 다른 규칙은 가능한 한 .NET 1.x의 컬렉션을 사용하는 것을 피하고 대신 .NET 2.0에 추가된 제네릭 컬렉션을 사용하라는 것이다.
.NET 1.x의 컬렉션은 System.Object 타입의 객체에 대한 참조를 저장하도록 구현되어 있다.
따라서 컬렉션에 값 타입의 객체를 추가하려는 경우 박싱을 피할 수 없다. 컬렉션으로부터 객체를 가져오는 경우에도 박싱된 객체의 복사본을 가져온다. 매번 객체에 대한 복사가 일어난다는 것이다.</p>
<pre><code class="language-cs">// 컬렉션 내에서 Person 타입을 사용한다.
var attendees = new List&lt;Person&gt;();
Person p = new Person { Name = &quot;Old Name&quot; };
attendees.Add(p);

// Name을 변경하려 했다.
// Person이 참조 타입이라면 정상 동작한다.
Person p2 = attendees[0];
p2.Name = &quot;New Name&quot;;

// &quot;Old Name&quot;을 출력한다.
Console.WriteLine(attendees[0].ToString());

int i = 25;
object o = i; // 박싱
Console.WriteLine(o.ToString());</code></pre>
<p>Person은 값 타입이다. JIT 컴파일러는 List&amp;ltPerson&gt;과 같이 닫힌 제네릭 타입을 생성하여, attendees 컬렉션에 Person 객체를 저장할 때 박싱이 일어나지 않도록 했다. 하지만 Name 속성을 변경하기 위해서 컬렉션으로부터 Person 객체를 가져오는 순간 새로운 복사본이 생성된다. 이후 속성 변경 코드는 모두 복사본을 대상으로 이뤄진다. 이러한 문제점 뿐 아니라 다른 여러 가지 이유에서라도 변경 불가능한 값 타입을 만드는 것이 여러모로 좋다.</p>
<h4 id="정리">정리</h4>
<p>결론적으로 값 타입은 System.Object 타입이나, 여타의 인터페이스 타입으로 변경할 수 있다. 이러한 변환 작업은 암시적으로 이뤄지기에 실제로 어떤 부분에서 이러한 변환 작업이 행해지는지도 찾아내기 어렵다. 수행 환경과 개발 언어별로 다양한 규칙이 존재하기 때문이다. 박싱과 언박싱 자업은 부지불식간에 객체에 대한 복사본을 생성하곤 하기에 버그가 발생할 수 있고, 값 타입을 다형적으로 처리하는 과정에서 성능을 느리게 만든다. 제네릭이 아닌 컬렉션 내에 값 타입의 객체를 저장하거나 System.Object 내 정의된 메서드를 호출하기 위해서 System.Object 타입으로 형변환을 수행하는 것과 같이 값 타입을 System.Object 타입이나 인터페이스 타입으로 변경하는 코드는 가능한 한 작성하지 말아야 한다.</p>