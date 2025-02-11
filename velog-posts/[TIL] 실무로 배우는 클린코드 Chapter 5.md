<h3 id="📌-chapter-5-가변성">📌 Chapter 5. 가변성</h3>
<ol>
<li><p><strong>var를 const로 변경하기</strong>  </p>
<ul>
<li>변경될 필요가 없는 변수는 <code>const</code>로 선언해 코드 안정성을 높인다.  </li>
<li><strong>돌연변이 테스트(Mutation Testing)</strong>를 활용해 상수로 변경할 수 있는 변수를 찾는다.  </li>
</ul>
</li>
<li><p><strong>불변성 유지하기</strong>  </p>
<ul>
<li>가변 데이터를 최소화하고, 불변 객체를 활용해 예측 가능성을 높인다.  </li>
<li>객체의 불변성을 유지하려면 <strong>객체를 복사</strong>한 후 변경하는 패턴을 사용한다.  </li>
</ul>
</li>
<li><p><strong>사이드 이펙트 줄이기</strong>  </p>
<ul>
<li>함수 내부에서 외부 상태를 직접 수정하지 않도록 주의한다.  </li>
<li>부수 효과(Side Effect)를 줄이면 디버깅이 쉬워지고 코드 유지보수가 쉬워진다.  </li>
</ul>
</li>
<li><p><strong>함수형 프로그래밍과 불변성</strong>  </p>
<ul>
<li>함수형 프로그래밍에서는 <strong>순수 함수(Pure Function)</strong>를 지향하며, 입력값을 직접 변경하지 않는다.  </li>
<li><code>map</code>, <code>filter</code>, <code>reduce</code> 같은 함수형 메서드를 활용해 가변성을 줄일 수 있다.  </li>
</ul>
</li>
</ol>
<p>즉, <strong>불필요한 변경을 최소화하고, 데이터를 변경할 때는 새로운 값을 생성하는 방식이 더 안전한 코드 작성을 가능하게 한다.</strong></p>