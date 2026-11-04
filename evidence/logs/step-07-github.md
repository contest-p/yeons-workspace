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
