<blockquote>
<p><a href="https://youtu.be/I_2a2wRSF1o?si=v-CO9id_fRlYSQka">해당 영상</a>을 시청 후 정리한 글입니다.</p>
</blockquote>
<h3 id="dlldynamic-link-library--pdbprogram-database">DLL(Dynamic-Link Library) &amp; PDB(Program Database)</h3>
<ul>
<li>Library : 컴퓨터 프로그램이 사용하는 비휘발성 자원(함수, 데이터, 타입 등)의 모임</li>
<li>DLL : 여러 프로그램에서 동시에 사용할 수 있는 코드와 데이터를 포함하는 라이브러리<ul>
<li>유저 작성 스크립트는 DLL로 생성되어 Unity Editor에서 사용됨</li>
</ul>
</li>
<li>pdb 파일에는 앱의 디버그 구성에 대한 링크를 허용하는 디버깅 및 프로젝트 상태 정보가 저장<img alt="" src="https://velog.velcdn.com/images/kl45678/post/c432637b-0393-40cb-863e-46d78a0e44e4/image.png" /></li>
</ul>
<h3 id="assembly-definition">Assembly Definition</h3>
<p><img alt="" src="https://velog.velcdn.com/images/kl45678/post/9fa678e4-1353-4c60-81df-3685fd9d9947/image.png" /></p>
<ul>
<li>컴파일 시 연관이 있는 dll들만 컴파일 하도록 분리.<ul>
<li>그렇기에, 컴파일 시간을 단축할 수 있음. (ex: ThirdParty.dll 수정 시 Stuff.dll, Library.dll은 재컴파일 하지 않음)</li>
</ul>
</li>
</ul>
<h4 id="in-unity">In Unity</h4>
<p align="center"><img height="60%" src="https://velog.velcdn.com/images/kl45678/post/907749e3-a923-49b4-9dbf-d2ecd62cafee/image.png" width="75%" />
<p align="left"> 유니티에서는 위 스크린샷과 같이 Assembly Definition 메뉴를 클릭해주면 
아래와 같은 아이콘(asmdef 파일)이 해당 폴더에 생기면서 dll로 묶여지는데, 해당 아이콘이 위치한 폴더 내부 스크립트들이 생성한 dll로 묶이게 된다.
<img src="https://velog.velcdn.com/images/kl45678/post/760b4417-90ba-44af-a1f9-eabaa75c0b0e/image.png" />