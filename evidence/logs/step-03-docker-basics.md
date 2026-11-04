# Docker 운영

사용자가 보낸 캡처에서 확인한 명령과 핵심 결과를 옮긴 기록입니다. 자동 수집한 원본 전체 로그가 아닙니다. #으로 시작하는 줄은 확인한 출력의 발췌·요약입니다. 실습 순서를 기록했으므로 전체를 한 번에 실행하지 마세요.

```text
docker info
# Server Version: 29.9.2
docker pull hello-world
docker run hello-world
# Hello from Docker!
docker ps -a
# hello-world: Exited (0)
docker pull ubuntu
docker images
docker run -d --name kdt-ubuntu ubuntu sleep infinity
docker exec -it kdt-ubuntu bash
ls
echo "inside the ubuntu container"
exit
docker ps
# kdt-ubuntu: Up
docker stats --no-stream kdt-ubuntu
# CPU 0.00%, MEM 3.074MiB / 15.25GiB
docker run -dit --name kdt-attach-2 ubuntu bash
docker attach kdt-attach-2
echo "Attached to the main Bash process"
exit
docker ps -a
# kdt-attach-2: Exited (0)
docker logs kdt-attach-2
# Attached to the main Bash process
docker stop kdt-ubuntu
docker ps -a
# kdt-ubuntu: Exited (137)
```

exec 안의 exit는 별도 Bash를 종료했다. attach로 연결한 주 프로세스 Bash의 exit는 컨테이너를 종료했다. 엔진 연결 오류, --no stream 오타, 기존 이름 충돌을 거쳐 성공했다.

