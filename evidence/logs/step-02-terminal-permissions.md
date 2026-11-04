# 파일과 권한

사용자가 보낸 캡처에서 확인한 명령과 핵심 결과를 옮긴 기록입니다. 자동 수집한 원본 전체 로그가 아닙니다. #으로 시작하는 줄은 확인한 출력의 발췌·요약입니다. 실습 순서를 기록했으므로 전체를 한 번에 실행하지 마세요.

```text
cat note.txt
# KDT terminal practice
chmod 644 note.txt
chmod 755 demo-dir
ls -ld note.txt demo-dir
# note.txt 권한 -rw-r--r--
# demo-dir 권한 drwxr-xr-x
chmod 600 note.txt
chmod 700 demo-dir
ls -ld note.txt demo-dir
# note.txt 권한 -rw-------
# demo-dir 권한 drwx------
mv -- ,hidden-note .hidden-note
mv -- copy.txt renamed.txt
ls -la
rm -- renamed.txt
ls -l
```

마지막 목록에는 demo-dir, empty.txt, note.txt가 남았다. 숨김 파일은 ls -la로 확인. 복사 시 note,txt 오타와 ls-l 공백 누락을 수정했다.

