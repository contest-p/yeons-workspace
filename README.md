# 내 컴퓨터에 개발자용 작업실 꾸미기

올인원 1차 미션의 실습 기록입니다. Ubuntu 터미널에서 파일과 권한을 다뤄 보고, Docker로 내가 만든 웹 페이지를 실행했습니다. **STEP 1~8 실습과 제출 검증을 완료**했습니다.

## 실습 환경

| 항목 | 사용한 환경 |
|---|---|
| 운영체제와 터미널 | Windows, VS Code 내장 터미널, PowerShell 5.1 |
| 리눅스 실습 | WSL에 설치한 Ubuntu, Bash |
| Git | 2.56.0.windows.4 |
| Docker | Docker Desktop의 Linux 엔진, CLI와 서버 버전 29.9.2 |

## 진행 상황

- [x] STEP 1. 개발 환경 확인
- [x] STEP 2. 리눅스 파일 조작과 권한 변경
- [x] STEP 3. Docker 기본 명령과 컨테이너 상태 확인
- [x] STEP 4. Dockerfile로 웹 페이지 실행
- [x] STEP 5. 바인드 마운트로 HTML 수정 반영 확인
- [x] STEP 6. 컨테이너를 삭제한 뒤에도 볼륨의 파일이 남는지 확인
- [x] STEP 7. Git 설정과 GitHub 저장소 연동
- [x] STEP 8. 제출용 로그와 캡처 연결 및 최종 검증

## STEP 1. 개발 환경 확인

PowerShell에서 현재 폴더, 셸 버전, WSL 배포판과 도구 버전을 확인했습니다. 처음에는 WSL 배포판이 없어서 Ubuntu를 설치했습니다.

```powershell
Get-Location
$PSVersionTable.PSVersion
wsl -l -v
git --version
docker --version
```

`docker --version`은 명령줄 도구의 버전을 보여줍니다. 실제로 컨테이너를 실행하는 Docker 엔진과 연결되는지는 STEP 3에서 `docker info`로 확인했습니다.

## STEP 2. 터미널에서 파일과 권한 다루기

Ubuntu에서 `cd ~`로 사용자 홈에 이동하고 `pwd`로 전체 경로를 확인했습니다. `/home/사용자이름`처럼 루트에서 시작하는 주소는 **절대 경로**, `note.txt`처럼 현재 폴더를 기준으로 찾는 주소는 **상대 경로**입니다.

아래는 실습을 새 폴더에서 다시 해볼 때 사용하는 명령입니다.

```bash
cd ~
pwd
mkdir kdt-step2-practice
cd kdt-step2-practice
pwd
mkdir demo-dir
touch .hidden-note empty.txt
echo "KDT terminal practice" > note.txt
ls -la
cat note.txt
cp note.txt copy.txt
mv copy.txt renamed.txt
ls -l
rm -- renamed.txt
ls -l
```

`ls -la`에서 점으로 시작하는 숨김 파일을 확인했습니다. 복사본을 `renamed.txt`로 바꾼 뒤 삭제했고, 원본 `note.txt`는 목록에 남았습니다. 같은 이름의 실습 폴더가 이미 있으면 새 폴더 이름으로 바꿔 실행합니다.

```bash
chmod 644 note.txt
chmod 755 demo-dir
ls -ld note.txt demo-dir
chmod 600 note.txt
chmod 700 demo-dir
ls -ld note.txt demo-dir
```

| 대상 | 변경 전 | 변경 후 |
|---|---|---|
| 파일 `note.txt` | `644` · `-rw-r--r--` | `600` · `-rw-------` |
| 디렉토리 `demo-dir` | `755` · `drwxr-xr-x` | `700` · `drwx------` |

권한 숫자는 **소유자, 그룹, 기타 사용자** 순서입니다. 읽기 `r=4`, 쓰기 `w=2`, 실행·경로 통과 `x=1`을 더합니다. 예를 들어 디렉토리 권한의 `5`는 목록을 읽고 그 경로를 통과할 수 있다는 뜻입니다.

## STEP 3. Docker 기본 운영

PowerShell에서 `docker info`의 `Server Version: 29.9.2`를 확인했습니다. `hello-world`는 메시지를 출력한 뒤 정상 종료되어 `docker ps -a`에 `Exited (0)`으로 표시됐습니다.

```powershell
docker info
docker pull hello-world
docker run hello-world
docker ps -a
docker pull ubuntu
docker images
```

**이미지**는 실행에 필요한 파일과 설정을 묶은 것이고, **컨테이너**는 그 이미지로 만든 실행 단위입니다. 두 Ubuntu 컨테이너로 주 프로세스와 별도로 실행한 셸의 차이를 확인했습니다.

```powershell
docker run -d --name kdt-ubuntu ubuntu sleep infinity
docker exec -it kdt-ubuntu bash
```

컨테이너 안에서 `ls`, `echo "inside the ubuntu container"`, `exit`를 실행했습니다. PowerShell에서 `docker ps`를 확인하니 주 프로세스인 `sleep infinity`가 계속 실행되어 컨테이너가 `Up`으로 표시됐습니다.

```powershell
docker stats --no-stream kdt-ubuntu
docker run -dit --name kdt-attach-2 ubuntu bash
docker attach kdt-attach-2
```

이번에는 연결된 Bash가 주 프로세스였습니다. 컨테이너 안에서 `echo "Attached to the main Bash process"`를 실행하고 `exit`하자 `kdt-attach-2`가 `Exited (0)`으로 바뀌었습니다. 이어서 로그를 조회하고 대기 중이던 `kdt-ubuntu`도 중지했습니다.

```powershell
docker ps -a
docker logs kdt-attach-2
docker stop kdt-ubuntu
docker ps -a
```

## STEP 4. 직접 만든 웹 페이지 실행

- [Dockerfile](Dockerfile)은 NGINX 웹 서버가 들어 있는 `nginx:alpine`을 바탕으로 이미지를 만듭니다.
- [site/index.html](site/index.html)은 브라우저에서 보여줄 페이지입니다. Dockerfile의 `COPY`는 `site/`의 파일을 NGINX가 읽는 경로로 복사합니다.

VS Code에서 프로젝트 폴더를 열고 PowerShell에서 실행합니다. 같은 이름의 컨테이너가 이미 만들어져 있다면 `docker ps -a`로 먼저 상태를 확인합니다.

```powershell
docker build -t kdt-workspace:1.0 .
docker run -d --name kdt-web -p 127.0.0.1:8080:80 kdt-workspace:1.0
docker ps --filter name=kdt-web
```

`8080:80`은 **내 PC의 8080으로 들어온 요청을 컨테이너의 80으로 전달**한다는 뜻입니다. `kdt-web`이 `Up`으로 표시됐고, 브라우저에서 `http://127.0.0.1:8080`을 열어 직접 만든 페이지를 확인했습니다.

## STEP 5. 바인드 마운트로 수정 내용 확인

바인드 마운트는 **내 PC의 폴더를 컨테이너 안의 경로에 연결**하는 기능입니다. 프로젝트 폴더에서 다음과 같이 실행했습니다.

```powershell
$sitePath = (Resolve-Path .\site).Path
docker run -d --name kdt-web-bind -p 127.0.0.1:8081:80 --mount "type=bind,source=$sitePath,target=/usr/share/nginx/html,readonly" kdt-workspace:1.0
docker ps --filter name=kdt-web-bind
```

`source`는 내 PC의 폴더이고 `target`은 컨테이너가 파일을 읽는 경로입니다. `readonly`는 컨테이너 쪽에서 파일에 쓰는 것을 막으며, VS Code에서는 계속 수정할 수 있습니다.

브라우저에서 `http://127.0.0.1:8081`을 열고, `site/index.html`의 문장을 수정해 저장한 뒤 같은 주소에서 새로고침했습니다. **이미지 재빌드나 컨테이너 재시작 없이** 새 문장이 표시됐습니다.

| 두 번째 수정 전 | 두 번째 수정 후 |
|---|---|
| [8081 브라우저 화면](evidence/screenshots/05-before-8081.png) | [8081 브라우저 화면](evidence/screenshots/05-after-8081.png) |
| 바운드 마운트로 수정한 문장이 반영됐습니다. | 같은 컨테이너에서 두 번째 수정이 반영됨 너무 어렵다. |

## 트러블슈팅

| 문제 | 원인 확인 | 해결과 결과 |
|---|---|---|
| `rm`이 `renamed.txt`를 찾지 못함 | 목록을 보니 `copy.txt`가 남아 있었음 | `mv`로 이름을 바꾼 뒤 삭제하고 목록에서 확인 |
| `docker run`에서 `pull access denied` 발생 | 빌드 결과는 `kdt-workspace:1.0`이었고 실행할 때 밑줄을 넣었음 | 이미지 이름을 맞춰 다시 실행하고 컨테이너와 브라우저 응답 확인 |
| HTML을 바꿔도 브라우저 문장이 그대로였음 | 주소창이 이미지에 복사한 파일을 제공하는 `8080`이었음 | 바인드 마운트가 연결된 `8081`에서 수정 전후 문장이 달라진 것을 확인 |

## STEP 6. 볼륨에 저장한 파일 확인

첫 번째 컨테이너에서 볼륨에 `proof.txt`를 저장했습니다. 그 컨테이너를 삭제한 뒤 같은 볼륨을 새 컨테이너에 연결하자 `KDT-volume-proof`가 다시 출력됐습니다.

## STEP 7. GitHub에 프로젝트 올리기

`main`에서 첫 커밋(`6b4ab01`)을 만들고 GitHub에 푸시했습니다. 저장소에서 `Dockerfile`, `README.md`, `site/`가 표시되는 것을 확인했습니다. Git은 내 컴퓨터에서 변경 이력을 관리하고, GitHub는 그 이력을 원격 저장소에 올려 공유하는 데 사용합니다.

## STEP 8. 제출 전 다시 실행하기

Docker Desktop을 켠 뒤, 이 저장소의 폴더를 VS Code에서 열고 PowerShell에서 실행합니다. 폴더 위치는 PC마다 달라도 됩니다. 아래 명령은 프로젝트 폴더를 기준으로 동작합니다.

```powershell
docker build -t kdt-workspace:1.0 .
docker run -d --name kdt-web-verify -p 127.0.0.1:8082:80 kdt-workspace:1.0
docker ps --filter name=kdt-web-verify
```

컨테이너가 `Up`이면 브라우저에서 `http://127.0.0.1:8082`를 엽니다. 이름이나 포트가 이미 사용 중이면 다른 컨테이너 이름과 호스트 포트를 지정합니다. Windows에서 실행한 결과와 GitHub 로그아웃 상태에서 README를 연 화면을 아래에 연결했습니다.

## 요구사항 검증표

| 단계 | 로그 | 캡처 | 결과 |
|---|---|---|---|
| 1. 환경 확인 | [실행 기록](evidence/logs/step-01-environment.md) | [화면](evidence/screenshots/step-01-environment.png) | PowerShell·Git·Docker 버전 확인 |
| 2. 파일과 권한 | [실행 기록](evidence/logs/step-02-terminal-permissions.md) | [화면](evidence/screenshots/step-02-permissions.png) | 파일과 폴더 권한 변경 확인 |
| 3. Docker 기본 운영 | [실행 기록](evidence/logs/step-03-docker-basics.md) | [화면](evidence/screenshots/step-03-hello-world.png) | hello-world 출력과 종료 상태 확인 |
| 4. 웹 페이지 실행 | [실행 기록](evidence/logs/step-04-web.md) | [화면](evidence/screenshots/step-04-web-8080.png) | 8080에서 직접 만든 페이지 확인 |
| 5. 바인드 마운트 | [실행 기록](evidence/logs/step-05-bind-mount.md) | [화면](evidence/screenshots/step-05-before.png) | 같은 8081에서 수정 전후 비교 |
| 6. 볼륨 영속성 | [실행 기록](evidence/logs/step-06-volume-persistence.md) | [화면](evidence/screenshots/step-06-volume.png) | 첫 컨테이너 삭제 후 파일 다시 읽기 |
| 7. GitHub 연동 | [실행 기록](evidence/logs/step-07-github.md) | [화면](evidence/screenshots/step-07-push.png) | main 푸시 및 파일 확인 |
| 8. 제출 검증 | [실행 기록](evidence/logs/step-08-verification.md) | [화면](evidence/screenshots/step-08-web-8082.png) | 재빌드와 공개 열람 확인 |


추가 화면 · [STEP 5 수정 후](evidence/screenshots/step-05-after.png), [STEP 8 터미널](evidence/screenshots/step-08-terminal.png), [STEP 8 로그아웃 상태 GitHub](evidence/screenshots/step-08-public-github.png)

[GitHub 제출 링크](https://github.com/contest-p/yeons-workspace)
