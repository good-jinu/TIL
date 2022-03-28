# switch and store

switch와 restore는 기존의 checkout이 하는 일을 분리해서 나온 명령어이다.

- checkout: Switch branches or restore working tree files
- switch: Switch branches
- restore: Restore working tree files



## 브랜치를 변경할 때

checkout의 사용

`git checkout new-branch`

switch 사용

`git switch new-branch`



## 수정한 작업내용을 다시 되돌릴 때

checkout의 사용

`git checkout -- README.md`

restore의 사용

`git restore README.md`