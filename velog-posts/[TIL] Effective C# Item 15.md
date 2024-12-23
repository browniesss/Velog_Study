<blockquote>
<p>Effective C# 책을 읽고 간단히 정리한 글입니다.</p>
</blockquote>
<h3 id="불필요한-객체를-만들지-말라">불필요한 객체를 만들지 말라</h3>
<p>C#은 가비지 컬렉터에서 사용하지 않는 객체들을 스스로 제거해주지만, 제거해주는 작업에서 비용이 발생하지 않는것은 아니여서, 과도하게 동작하지 않도록 주의해야한다.</p>
<h4 id="가비지-컬렉터의-작업을-줄일-수-있는-방법">가비지 컬렉터의 작업을 줄일 수 있는 방법</h4>
<ul>
<li><p>모든 참조 타입의 객체는 지역 변수라고 하더라도 동적으로 메모리를 할당한다. 그렇기에 아래 예제 중 올바른 예제처럼 지역 변수에서 매번 가비지가 발생하지 않도록 멤버 변수로 빼는식의 작업이 필요하다.</p>
<pre><code>  // 나쁜 예
  protected override void OnPaint(PaintEventArgs e)
  {
      using (Font MyFont = new Font(&quot;Arial&quot;, 10.0f))
      {
          ~~~~
      }

      base.OnPaint(e);
  }

  // 올바른 예
  protected override void OnPaint(PaintEventArgs e)
  {
      e.Graphics.DrawString(DateTime.Now.ToString(), myFont, Brushes.Black, new PointF(0, 0));

      base.OnPaint(e);
  }</code></pre></li>
<li><p>종속성 삽입을 활용해  자주 사용되는 객체를 생성했다가 이를 재활용해야한다.</p>
<ul>
<li><p>.NET Framework의 Brushes 클래스 예제이다.</p>
<pre><code>private static Brush blackBrush;

public static Brush black
{
 get
 {
     if (blackBrush == null)
         blackBrush = new SolidBrush(Color.Black);
     return blackBrush;
 }
}</code></pre></li>
</ul>
</li>
</ul>
<h4 id="변경-불가능한-타입-관리">변경 불가능한 타입 관리</h4>
<p>변경 불가능한 타입의 대표적인 예로는 System.String이 있다. string 객체가 가지고 있는 문자열 내용은 기본적으로 수정이 불가능한데 프로그래밍 내에서 문자열을 변경할 수 있는 것 처럼 보인다. 하지만, 이는 변경하는게 아닌 새로운 문자열을 가지는 새로운 string 객체가 생성되는 것이며, 이전 문자열을 가진 객체는 가비지가 된다.</p>
<pre><code>public static void Main(string[] args)
{
    string msg = &quot;Hello, &quot;;
    msg += thisUser.Name;
    msg += &quot;. Today is &quot;;
    msg += System.DateTime.Now.ToString();
}</code></pre><p>위 예제는 객체들이 계속 생성되어 매우 비효율적이며, 문자열 보간이나 StringBuilder 클래스를 사용하도록 대체해야한다.</p>
<pre><code>StringBuilder msg = new StringBuilder(&quot;Hello, &quot;);

msg.Append(thisUser.Name);
msg.Append(~~~);
~~~~</code></pre><p>StringBuilder클래스는 실제로 수정 가능한 문자열을 나타내기 위한 타입이며, 최종적으로는 변경 불가능한 string 타입의 객체를 가져오는 기능을 제공한다. StringBuilder를 통해 배울 수 있는 점은 불변타입을 사용하게 된다면 이런 Builder 클래스를 만들어 StringBuilder와 같은 기능을 제공하는 것도 고려해야 한다.</p>