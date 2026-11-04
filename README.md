# 내 컴퓨터에 개발자용 작업실 꾸미기

올인원 1차 미션의 실습 기록입니다. Ubuntu 터미널에서 파일과 권한을 다뤄 보고, Docker로 내가 만든 웹 페이지를 실행했습니다. 지금은 **STEP 5까지 실습 완료**했고, 다음은 **STEP 6 볼륨 실습**입니다.

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
- [ ] STEP 6. 컨테이너를 삭제한 뒤에도 볼륨의 파일이 남는지 확인
- [ ] STEP 7. Git 설정과 GitHub 저장소 연동
- [ ] STEP 8. 제출용 로그와 캡처 연결 및 최종 검증

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

## 트러블슈팅

| 문제 | 원인 확인 | 해결과 결과 |
|---|---|---|
| `rm`이 `renamed.txt`를 찾지 못함 | 목록을 보니 `copy.txt`가 남아 있었음 | `mv`로 이름을 바꾼 뒤 삭제하고 목록에서 확인 |
| `docker run`에서 `pull access denied` 발생 | 빌드 결과는 `kdt-workspace:1.0`이었고 실행할 때 밑줄을 넣었음 | 이미지 이름을 맞춰 다시 실행하고 컨테이너와 브라우저 응답 확인 |
| HTML을 바꿔도 브라우저 문장이 그대로였음 | 주소창이 이미지에 복사한 파일을 제공하는 `8080`이었음 | 바인드 마운트가 연결된 `8081`에서 수정 전후 문장이 달라진 것을 확인 |

## 증거와 다음 작업

현재 화면 증거는 노션의 **제출 증빙 모음 · 대조 후 정리본**에 있습니다. STEP 5 브라우저 화면은 페이지 맨 아래의 **8081 수정 전·후 교정본**을 사용합니다. 최종 제출 전에는 캡처를 `evidence/screenshots/`에, 실제 명령과 출력은 `evidence/logs/`에 넣고 이 README에서 상대 경로로 연결합니다.

다음 실습은 **STEP 6**입니다. 이름 있는 볼륨에 파일을 저장한 뒤 기존 컨테이너를 삭제하고, 새 컨테이너에서 같은 파일을 읽어 데이터가 남았는지 확인합니다.
