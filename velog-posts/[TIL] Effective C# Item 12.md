<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h4 id="아이템-12-할당-구문보다-멤버-초기화-구문이-좋다">아이템 12: 할당 구문보다 멤버 초기화 구문이 좋다</h4>
<p>클래스를 만들다 보면 종종 여러 개의 생성자를 작성해야 하는 경우가 있는데, 생성자 내부에서 멤버 변수들의 값을 초기화하도록 처리하다보면 휴먼 미스로 인해서 코드가 누락되는 경우가 많다.</p>
<p>그렇기에 생성자에서 멤버 변수의 값을 초기화하는게 아닌, 멤버 초기화 구문을 사용하는게 좋다.</p>
<pre><code>public class MyClass
{
    // 컬렉션 선언 동시에 초기화
    private List&lt;string&gt; labels = new List&lt;string&gt;();
}</code></pre><p>위 예시처럼 멤버 초기화 구문을 넣으면 생성자 구문 앞쪽에 자동으로 초기화 구문이 포함된다.</p>
<p>다만, 멤버 초기화 구문을 사용한다고 초기화되지 않은 멤버 변수를 사용하는 문제로부터 벗어날수는 있지만 완벽한 것은 아니다.</p>
<p>아래 3가지 경우에는 멤버 초기화 구문을 사용하지 않는게 낫다.</p>
<ul>
<li><p>객체를 0이나 null로 초기화 하는 경우</p>
<ul>
<li>기본 시스템 초기화 루틴은 모든 값을 코드 실행 전 0으로 설정하기에, 불필요한 일을 추가적으로 하는 꼴이 된다.<pre><code>public struct MyType
{
MyType myVal1; // 0으로 초기화
MyType myVal2 = new MyType(); // 반복해서 0으로 초기화
}</code></pre></li>
</ul>
</li>
<li><p>동일한 객체를 반복해서 초기화 하는 경우</p>
<ul>
<li><p>아래 코드처럼 List 객체를 생성하는 방식이 다양하게 혼재할 경우 멤버 초기화를 사용하지 않는 것이 좋다.</p>
<ul>
<li><p>아래 경우에서는 실제로 2개의 List&lt;&gt; 객체가 생성되고 멤버 초기화 구문은 생성자의 본문보다 앞서 수행되므로, 생성자에서 생성한 객체만 살아남고, 멤버 초기화 구문의 객체는 바로 가비지가 된다.</p>
<pre><code>public class MyClass
{
private List&lt;string&gt; labels = new List&lt;string&gt;();

MyClass()
{
}

MyClass(int size)
{
    labels = new List&lt;string&gt;(size);
}
}</code></pre></li>
</ul>
</li>
</ul>
</li>
<li><p>예외 처리가 반드시 필요한 경우</p>
<ul>
<li>멤버 초기화 구문은 try-catch 등으로 감쌀 수 없기 때문에, 초기화 과정에서 예외가 발생 시 예외가 외부로 전파되기에 클래스 내부에서 복구를 시도할 수 없다.
반드시 예외가 필요한 경우 멤버 초기화 구문 대신 생성자 내부로 옮겨야 한다.</li>
</ul>
</li>
</ul>