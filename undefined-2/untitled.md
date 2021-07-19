# Git Branch

### Branch란? 여러 개발자들이 동시에 다양한 작업을 독립적으로 할 수 있게 만들어 주는 기능

필요에 의해서 각각의 브랜치를 생성해서 작업을 진행한 뒤, 다른 브랜치와 merge함으로써, 작업한 내용을 다시 새로운 브랜치로 모으는 것이 가능!

master branch : 기본적으로 처음으로 저장소를 만들게 되면 생성되는 브랜치이다.

생성된 브랜치를 통합하는 방법은 크게 2가지가 있다.

* merge : 
* rebase

브랜치 생성

git branch 이름

브랜치 옮기기

git checkout 이름

#### 브랜치 합치기

**git merge**

git의 merge는 두 개의 부모를 가리키는 특별한 커밋을 생성한다. 두개의 부모가 있는 커밋이라는 것은 

한 부모의 모든 작업내역과 나머지 부모의 모든 작업, 그리고 두 부모의 모든 부모들의 작업내역을 포함한다.

bugFix라는 브랜치로 merge 실습

브랜치 생성

git branch bugFix

브랜치 이동

git checkout bugFix

bugFix 브랜치에서 작업

git commit

메인 브랜치이동

git checkout main

메인 작업 

git commit

메인에 bugFix 브랜치에서의 작업을 합치기\(현재 main 브랜치\)

git merge bugFix

#### git rebase

rebase는 기본적으로 커밋을 모아서 복사한 뒤, 다른 곳에 떨구는 작업

커밋의 흐름을 하나로 모으는 작업이기 때문에 가독성이 좋다!

실습

bugFix라는 브랜치 생성

git branch bugFix

bugFix 브랜치로 이동해서 커밋

git checkout bugFix

git commit

main 브랜치로 다시 돌아가서 커밋

git checkout main

git commit

bugFix로 돌아가서 main에 rebase\(bugFix에서 main에 붙여야 하기 때문에 돌아가서 진행\)

git checkout bugFix

git rebase main

#### Commit tree에서 이동할 수 있는 여러가지 방법들

HEAD란? : 현재 체크아웃된 커밋 = 현재 작업 중인 커밋\(가장 최근 커밋을 가리키고 있음\)

일반적으로 HEAD는 브랜치의 이름을 가리키고 있고, 커밋을 하게 되면 상태가 바뀌게 되고 이는 HEAD를 통해서 확인이 가능

