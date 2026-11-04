# 웹 서버

사용자가 보낸 캡처에서 확인한 명령과 핵심 결과를 옮긴 기록입니다. 자동 수집한 원본 전체 로그가 아닙니다. #으로 시작하는 줄은 확인한 출력의 발췌·요약입니다. 실습 순서를 기록했으므로 전체를 한 번에 실행하지 마세요.

```text
docker build -t kdt-workspace:1.0 .
# FINISHED
docker run -d --name kdt-web -p 127.0.0.1:8080:80 kdt-workspace:1.0
docker ps --filter name=kdt-web
# Up, 127.0.0.1:8080->80/tcp
```

브라우저 127.0.0.1:8080에서 직접 만든 웹 페이지 확인. kdt_workspace를 입력한 오류는 kdt-workspace로 고쳐 해결.

