# 외부 성경 데이터

성경 전문은 이 공개본에 포함되지 않습니다. 사용 권한을 확인한 JSON 배열을 `bible_structured.json`으로 준비합니다. 다음 content는 형식 설명용 문구이며 성경 번역문이 아닙니다.

```json
[{"book":"창","chapter":1,"verse":1,"content":"직접 준비한 사용 가능한 본문"}]
```

server 디렉터리에서 `python manage.py ingest_bible`로 적재합니다. 임베딩은 별도 공급자 설정 후 `python manage.py ingest_bible --embed`로 생성합니다. 인식 가능한 책 코드는 `server/scripture/books.py`를 확인하세요.
