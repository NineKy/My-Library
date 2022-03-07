# 해시\(Hash\)

Hash란?

Key-Value 쌍으로 데이터를 저장하는 자료구조



Key : Hash에서 매핑할 떄 사용하는 인덱스, 키는 절대로 중복되지 않는다!! 그리고 만약 같은 key에 값을 넣게되면 값이 덮어써진다



선언

Map &lt;key타입, value타입&gt; 변수명 = new HashMap&lt;&gt;\(\);

함수

.get\(키\) : 키에 해당하는 값을 가져옴

.put\(키, 값\) : 키에 값을 매핑시킴

.entrySet\(\) : 키와 벨류값 모두 가져오는

.keySet\(\) : 키값만 전부 가져올때

.containsValue\(값\) : 인자로 들어간 값이 있으면 true, 없으면 false 리턴



<br><br><br><br><br><br><br><br><br><br>