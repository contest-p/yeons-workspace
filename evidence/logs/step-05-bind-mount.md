# 바인드 마운트

사용자가 보낸 캡처에서 확인한 명령과 핵심 결과를 옮긴 기록입니다. 자동 수집한 원본 전체 로그가 아닙니다. #으로 시작하는 줄은 확인한 출력의 발췌·요약입니다. 실습 순서를 기록했으므로 전체를 한 번에 실행하지 마세요.

```text
$sitePath = (Resolve-Path .\site).Path
docker run -d --name kdt-web-bind -p 127.0.0.1:8081:80 --mount "type=bind,source=$sitePath,target=/usr/share/nginx/html,readonly" kdt-workspace:1.0
docker ps --filter name=kdt-web-bind
# Up, 127.0.0.1:8081->80/tcp
```

같은 8081 주소에서 HTML 문장 수정 전후 확인. 수정 후 문구는 '같은 컨테이너에서 두 번째 수정이 반영됨 너무 어렵다.'였다. 8080에서 확인하던 포트 혼동을 해결했다.

