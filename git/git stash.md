# git stash

---

하던 작업을 임시로 저장해 두어야 할 필요가 생기는 경우가 존재한다. 예를 들면 작업 도중에 다른 브랜치로 이동해야 할 때. 그럴 때 쓸 수 있는 명령어가 `git stash`



`git stash`

> 새로운 스태시를 스택에 만들어 하던 작업을 임시로 저장한다.

`git stash list`

> 지금까지 스태시한 목록들을 확인할 수 있다.

`git stash apply`

> 가장 최근의 스태시를 가져와 적용한다.
>
> 제일 중요한 것은 가져오는거랑 삭제하는 거랑은 별개이다.
>
> 0번을 가져왔다고 해서 0번이 삭제되지는 않는다. 때문에 가장 최신의 스태시를 연속적으로 가져오고 싶다면 apply와 drop을 반복해야 한다.

`git stash apply [stash 이름]`

> ex) `$ git stash apply stash@{1}` 특정한 이름의 스태시를 가져와 적용한다.

`git stash drop`

> 가장 최근의 스태시 제거

`git stash drop [stash 이름]`

> 특정한 이름의 스태시를 제거

`git stash pop`

> 위의 `git stash apply`의 문제는 해당 stash를 삭제하지 않는다는 점에 있다. 보통 apply로 적용을 시키게 되면 해당 stash는 더이상 필요하지 않음으로 drop 하는 경우가 굉장히 많다. 두번의 명령어를 입력하는 귀찮음을 해소하기 위해서는 pop을 사용하면 된다.
>
> `git stash apply` + `git stash drop` = `git stash pop` 이다.



## 참고문헌

---

https://gmlwjd9405.github.io/2018/05/18/git-stash.html