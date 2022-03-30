# reflog와 gc를 이용한 저장소 관리
git은 branch의 commit들의 데이터만 가지고 있는 것이 아니다. 잃어버린 커밋 또한 로그로써 가지고 있다.

## reflog
`git reflog`명령은 HEAD가 거쳐갔던 커밋의 로그를 보여준다. 여기서 보여주는 히스토리를 이용해서 실수로 reset을 해서 잃어버리거나 브랜치를 삭제해서 잃어버린 커밋을 다시 되찾을 수 있다.

`reflog`의 일은 로컬에서 일어난 일만 기록하기 때문에 같은 레포지토리를 공유하는 동료에게는 같은 reflog가 존재하지 않는다.

## gc
`git gc`명령은 Garbage collection의 줄임으로써 저장소에서 필요없는 파일을 삭제하고 남은 파일을 압축한 것이다.

Git은 때가 되면 자동으로 "auto gc"를 실행한다. Loose 개체가 7천 개가 넘거나 Packfile이 50개가 넘지 않으면 Git은 실제로 gc 작업을 실행하지 않는다. 만약 Loose 개체가 너무 많거나, Packfile 자체가 너무 많으면 Git은 그제야 진짜로 git gc 명령이 일하게 한다.

---
## 접근할 수 없는 커밋 제거로 용량 확보
reset한다고 커밋이 실제로 삭제되는 것이 아니다. 그리고 다음의 명령으로 직접 제거가 가능하다.
```git
git reflog expire --expire=now --all
git gc --prune=now
```