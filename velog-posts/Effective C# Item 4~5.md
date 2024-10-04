<p>Effective C# Chapter 1. Item 4 ~ 5 항목의 내용을 정리한 글입니다.
<img height="25%" src="https://velog.velcdn.com/images/kl45678/post/3e98c70c-e754-4b1f-9dd6-4302d59e2679/image.png" width="25%" /></p>
<h3 id="아이템-4--stringformat을-보간-문자열로-대체하라">아이템 4 : string.Format()을 보간 문자열로 대체하라</h3>
<ul>
<li>이전에 사용되던 string.Format이 아닌 C#에서는 ${} 형식의 보간 문자열로 대체해서 사용해야한다.<ul>
<li>사용되면 장점은 아래와 같음.<ul>
<li>코드 가독성이 대폭 향상된다</li>
<li>컴파일러 입장에서 정적 타입 검사를 수행할 수 있기 때문에 개발자의 실수를 방지 가능</li>
<li>기존 방식에 비해 문자열을 생성하기 위한 표현식이 더 풍성하다.</li>
</ul>
</li>
</ul>
</li>
</ul>
<h3 id="아이템-5--문화권별로-다른-문자열을-생성하려면-formattablestring을-사용하라">아이템 5 : 문화권별로 다른 문자열을 생성하려면 FormattableString을 사용하라</h3>
<p>여러 문화권과 다양한 언어를 다뤄야 할 경우 세부적인 제어가 필요하기 때문에, 문자열을 생성하는 과정을 좀 더 자세히 알고 있어야 한다. 문자열 보간 기능의 결과로 생성되는 반환값은 문자열일 수도 있지만 FormattableString을 상속한 타입일 수도 있다.</p>
<pre><code class="language-cs">// String
string first = $&quot;It's the {DateTime.Now.Day} of the {DateTime.Now.Month} month&quot;;
// FormattableString
FormattableString second = $&quot;It's the {DateTime.Now.Day} of the {DateTime.Now.Month} month&quot;;
// String or FormattableString
var third = $&quot;It's the {DateTime.Now.Day} of the {DateTime.Now.Month} month&quot;;</code></pre>
<p><strong>FormattableString 타입</strong>은 컴퓨터에 지정된 문화권을 고려해서 문자열을 생성한다.</p>
<pre><code class="language-cs">public static string ToGerman(FormattableString src) 
{ 
      return string.Format(null, System.Globalization.CultureInfo.CreateSpecificCulture(&quot;de-de&quot;), src.Format, src.GetArguments()); 
} 

public static string ToFrenchCanada(FormattableString src) 
{ 
      return string.Format(null, System.Globalization.CultureInfo.CreateSpecificCulture(&quot;fr-CA&quot;), src.Format, src.GetArguments());
}</code></pre>
<p>그렇기에 var로 변수를 선언했음에도 FormmatableString 객체가 반환되기를 기대한다면 아래 사항을 주의해야한다.</p>
<ul>
<li>문자열을 매개변수로 취하는 메서드에 이 변수를 전달하는 코드를 작성하면 var 변수가 string 타입이 된다.</li>
<li>string과 FormattableString을 모두 받아들일 수 있는 오버로딩 메서드를 작성하면 선언한 변수가 string 타입이 된다.</li>
</ul>