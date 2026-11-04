# STEP 7 · Git과 GitHub 연동

터미널과 GitHub 캡처에서 확인한 내용을 정리했습니다. 개인 이메일은 생략했습니다.

- Git 버전은 `2.56.0.windows.4`입니다.
- `git init -b main`으로 초기화했고, 로컬 커밋 이름과 이메일을 설정했습니다.
- 첫 커밋 `6b4ab01`에 `Dockerfile`, `README.md`, `site/index.html`을 넣었습니다.
- [공개 GitHub 저장소](https://github.com/contest-p/yeons-workspace)의 `main`에서 같은 커밋과 파일 목록을 확인했습니다.

## 제출 검증 중 확인한 Git 설정

현재 작업 폴더에서 실행한 결과 중 사용자 설정과 브랜치에 관련된 줄을 발췌했습니다. 이름과 이메일을 가렸습니다.

```text
> git config --list
user.name=[masked]
user.email=[masked]
init.defaultbranch=master
branch.main.remote=origin
branch.main.merge=refs/heads/main
```

현재 브랜치는 별도로 확인했습니다.

```text
> git branch --show-current
main
```

## VS Code 로그인과 저장소 연동 확인

2026-11-04에 현재 VS Code에서 직접 확인했습니다.

- [계정 메뉴의 GitHub 로그인 상태](../screenshots/step-07-vscode-github-account.png)
- [VS Code 내장 터미널의 git remote -v 결과](../screenshots/step-07-vscode-repository.png)

계정 캡처는 메뉴 부분을 잘라 저장했습니다. 공개 GitHub 계정명은 로그인 상태 확인을 위해 표시되어 있습니다.

저장소 연동 캡처에는 `origin`의 fetch·push 주소가 모두 `https://github.com/contest-p/yeons-workspace.git`로 표시됩니다.

`git init -b main`으로 이 저장소의 초기 브랜치를 지정했습니다. `init.defaultbranch=master`는 브랜치 옵션을 생략하고 새 저장소를 초기화할 때의 기본값이며, 현재 저장소의 `main`과 별개입니다.

## 현재 설정 목록 확인

2026-11-04에 git config --list를 실행한 결과입니다. 이름·이메일은 가렸고, 아래 검증에 필요하지 않은 기타 설정값은 생략했습니다.

```text
> git config --list
diff.astextplain.textconv=[value omitted]
filter.lfs.clean=[value omitted]
filter.lfs.smudge=[value omitted]
filter.lfs.process=[value omitted]
filter.lfs.required=[value omitted]
http.sslbackend=schannel
core.autocrlf=true
core.fscache=true
core.symlinks=false
pull.rebase=false
credential.helper=manager
credential.https://dev.azure.com.usehttppath=[value omitted]
init.defaultbranch=master
core.repositoryformatversion=0
core.filemode=false
core.bare=false
core.logallrefupdates=true
core.symlinks=false
core.ignorecase=true
user.name=[masked]
user.email=[masked]
remote.origin.url=https://github.com/contest-p/yeons-workspace.git
remote.origin.fetch=[value omitted]
branch.main.remote=origin
branch.main.merge=refs/heads/main
```
