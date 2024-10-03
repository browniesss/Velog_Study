<blockquote>
<p>노멀맵은 왜 항상 파란색으로 보여질까 ?</p>
</blockquote>
<p><img alt="" src="https://velog.velcdn.com/images/kl45678/post/1fa030ef-21ea-4b14-a0a0-19a317a3828e/image.png" /></p>
<p>쉐이더를 공부하다 <strong>노멀 맵이 항상 파란색으로 보여지는 이유</strong>가 궁금해서 찾아보았습니다.</p>
<p>그 이유는 굉장히 간단했는데, 노멀 맵이 <strong>방향</strong>을 <strong>RGB</strong>로 표현하기 때문입니다.</p>
<p>방향은 벡터값을 사용하는데 벡터값은 <strong>-1 ~ 1</strong> 까지이며, 이 값을 컬러(RGB)값을 사용하여 표현합니다.</p>
<p>컬러 값은 <strong>0 ~ 1(0 ~ 255)</strong> 사이의 값을 사용하는데 x값은 Red를 표현하고, y는 Green을 표현하고, z는 Blue를 표현합니다.
<img alt="" src="https://velog.velcdn.com/images/kl45678/post/205c18dc-67a1-4e56-8344-5492a8bcf8b0/image.png" /></p>
<p>아래 이미지를 통해 설명하자면, 공간 좌표계 상에서 z값은 높이를 나타냅니다. 노멀 맵 상에서 굴곡을 표현할 때, z값은 높이를 나타내므로 0보다 낮아질 수 없습니다. 따라서 z값은 항상 0 이상이 되며, 벡터에서 0은 RGB 값으로 변환 시 127.5가 됩니다. 이 때문에 노멀 맵은 항상 파란색을 띕니다.
<img alt="" src="https://velog.velcdn.com/images/kl45678/post/d7f01d68-d939-4f39-8e3b-4493f10f3326/image.png" /></p>
<blockquote>
</blockquote>
<p>아래 영상을 참고하여 포스팅 하였습니다.
<a href="https://www.youtube.com/watch?v=7XxNj6NDI7I&amp;t=129s">https://www.youtube.com/watch?v=7XxNj6NDI7I&amp;t=129s</a></p>