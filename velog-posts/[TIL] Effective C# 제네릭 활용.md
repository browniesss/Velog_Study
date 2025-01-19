<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="chapter-3-제네릭-활용">Chapter 3. 제네릭 활용</h3>
<p>제네릭은 코드의 크기와 성능에 영향을 미치는 중요한 기능이다. 제네릭으로 변경하면 일반적으로 코드의 크기가 작아지지만, 때로는 더 커질 수도 있는데, 이는 타입 매개변수의 지정 방식과 닫힌 제네릭 타입의 생성 수에 따라 달라진다.</p>
<h2 id="제네릭의-동작-방식">제네릭의 동작 방식</h2>
<p><strong>IL과 JIT 컴파일러의 역할</strong>
IL에서 제네릭은 타입을 부분적으로 정의하며, JIT 컴파일러는 닫힌 제네릭 타입으로 객체를 인스턴스화할 때 타입의 정의를 완성한다. 이는 코드의 크기와 성능 사이의 절충안으로 작용한다.</p>
<p><strong>참조 타입과 값 타입의 차이</strong>
참조 타입이 타입 매개변수로 사용될 경우, 동일한 머신 코드가 생성되어 공유된다. 예를 들어:</p>
<pre><code class="language-csharp">List&lt;string&gt; stringList = new List&lt;string&gt;();
List&lt;Stream&gt; OpenFiles = new List&lt;Stream&gt;();
List&lt;MyClassType&gt; anotherList = new List&lt;MyClassType&gt;();</code></pre>
<p>반면, 값 타입이 타입 매개변수로 사용될 경우에는 서로 다른 머신 코드가 생성된다:</p>
<pre><code class="language-csharp">List&lt;double&gt; doubleList = new List&lt;double&gt;();
List&lt;int&gt; markers = new List&lt;int&gt;();
List&lt;MyStruct&gt; values = new List&lt;MyStruct&gt;();</code></pre>
<h2 id="jit-컴파일러의-최적화">JIT 컴파일러의 최적화</h2>
<p><strong>값 타입의 처리 과정</strong>
JIT 컴파일러는 값 타입을 처리할 때 두 단계를 거친다:</p>
<ol>
<li>닫힌 제네릭 타입을 표현하는 새로운 IL 클래스 생성</li>
<li>대체가 완료된 타입을 이용한 실제 머신 코드 생성</li>
</ol>
<p><strong>성능과 메모리 관리</strong>
제네릭 타입의 타입 매개변수로 값 타입을 사용하면 박싱과 언박싱을 피할 수 있어 코드와 데이터의 크기가 줄어든다. 또한 컴파일러가 타입 안정성을 보장하므로 런타임에서의 타입 확인이 불필요해져 성능이 개선된다.</p>