# 프록시와 연관관계 관리

이 장에서는 크게

프록시, 즉시로딩, 지연로딩 그리고 영속성 전이와 고아 객체에 대해서 배움



프록시라는 기술?은 객체를 객체 그래프를 통해 연관된 객체들을 탐색하는 과정에서 만약 객체가 데이터 베이스에 저장되어있다면 객체 그래프를 통해서 마음껏 연관된 객들을 탐색하는 것이 어려운데 이런 어려운 점을 해결해주는 기술이다.

프록시를 사용하게 되면 연관된 객체를 처음부터 데이터베이스에서 조회하는 것이 아니라 실제 사용하는 시점에 조회할 수 있다. 또한 자주 함께 사용되는 객체들은 조인을 사용해서 조회하는 것도 가능하다.&#x20;

그리고 이러한 두 방식을 즉시 로딩과 지연 로딩을 통해서 지원하고 있다.



영속성 전이와 고아 객체는 연관된 객체를 함께 저장하거나 함께 삭제하는 과정에서 이 2가지 방식을 통해서 편리하게 사용할 수 있다.


