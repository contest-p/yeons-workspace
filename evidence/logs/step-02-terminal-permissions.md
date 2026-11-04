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

## 원본 캡처

- [현재 위치, 폴더·빈 파일 생성, 내용 출력, 복사와 권한 변경](../screenshots/step-02-files-full.png)
- [권한 변경 전후, 이름 변경과 삭제 후 목록](../screenshots/step-02-permissions.png)

권한 문자열이 보이는 원본으로 기존의 왼쪽이 잘린 캡처를 교체했습니다. 원본에서 처음 만든 파일 이름은 `,hidden-note`이며, 이후 `mv -- ,hidden-note .hidden-note`로 수정했습니다. 출력이 없는 생성·복사·이동·삭제 명령은 이어지는 목록과 내용 조회로 결과를 확인합니다.

