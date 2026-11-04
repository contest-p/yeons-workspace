# STEP 6 · 볼륨 영속성

캡처에서 확인한 실행 기록입니다.

```powershell
docker volume create kdt-step6-data
docker run -d --name kdt-volume-first --mount type=volume,source=kdt-step6-data,target=/data ubuntu sleep infinity
docker exec kdt-volume-first bash -c 'echo KDT-volume-proof > /data/proof.txt && cat /data/proof.txt'
docker stop kdt-volume-first
docker rm kdt-volume-first
docker ps -a --filter name=kdt-volume-first
docker run --name kdt-volume-second --mount type=volume,source=kdt-step6-data,target=/data ubuntu cat /data/proof.txt
```

첫 번째 쓰기 직후와 마지막 읽기에서 모두 `KDT-volume-proof`가 출력됐습니다. 삭제 후 목록에는 열 제목만 표시됐습니다. 원본 캡처는 노션의 STEP 6에 있습니다.
