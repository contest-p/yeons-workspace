# STEP 8 · 최종 제출 검증

```powershell
git pull --ff-only origin main
git log -1 --oneline
Get-ChildItem .\evidence\logs -Name
docker build -t kdt-workspace:1.0 .
docker run -d --name kdt-web-verify -p 127.0.0.1:8082:80 kdt-workspace:1.0
docker ps --filter name=kdt-web-verify
```

- 검증 당시 로컬과 원격 main은 `94bf2cb`였다.
- 빌드가 끝났고 `kdt-web-verify`가 `Up`, 포트는 `127.0.0.1:8082->80/tcp`였다.
- 브라우저에서 올인원 1차 미션 페이지가 열렸고, 로그아웃 상태에서 GitHub README를 열었다.

[터미널 화면](../screenshots/step-08-terminal.png) · [웹 페이지](../screenshots/step-08-web-8082.png) · [GitHub 공개 열람](../screenshots/step-08-public-github.png)
