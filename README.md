# 한자랑 리소스 팩

[한자랑](https://github.com/akushig/hanjarang) 앱이 **앱 업데이트 없이** 데이터를 갱신하기 위한
공개 자산 저장소입니다. 앱은 `manifest.json`을 조건부 GET으로 확인하고, 새 버전이 있으면
해당 팩을 내려받아 sha256을 검증한 뒤 사용합니다. 내려받기에 실패하면 앱에 번들된 자산을
그대로 쓰므로 **네트워크가 없어도 학습이 막히지 않습니다.**

## 구조

```
manifest.json              # 팩 목록과 현재 버전 (앱이 이 파일만 폴링)
packs/<packKey>/<version>/<packKey>.json.gz
```

`manifest.json`:

```json
{
  "schemaVersion": 1,
  "generatedAt": "2026-07-26T12:00:00Z",
  "packs": {
    "kla_relations": {
      "version": 1,
      "path": "packs/kla_relations/1/kla_relations.json.gz",
      "sha256": "…",
      "sizeBytes": 1234,
      "minAppVersion": "1.7.5",
      "changelog": "초기 팩"
    }
  }
}
```

- `version`은 단조 증가하는 정수입니다. 롤백은 manifest의 `version`·`path`를 이전 값으로
  되돌리는 것으로 합니다(팩 파일은 지우지 않습니다 — 지난 버전도 경로에 그대로 남습니다).
- `minAppVersion` 미만의 앱은 그 팩을 무시합니다(구버전 앱이 새 스키마를 못 읽는 사고 방지).
- `sha256`은 **압축된 `.json.gz` 파일**의 해시입니다.

## 발행 방법

팩 생성은 앱 리포의 개발 워크플로(`tool/` 스크립트·AI 생성·수작업 검수)에서만 가능하고,
이 저장소에는 **발행된 결과물만** 올라옵니다. 발행은 앱 리포에서:

```bash
node backend/tools/publish_pack_gh.mjs <packKey> <소스파일> [--changelog "..."] [--min-app 1.7.5]
```

## 라이선스 고지

이 저장소의 팩은 원본 데이터의 라이선스를 따릅니다. 팩별 출처·라이선스:

| 팩 | 출처 | 라이선스 |
|---|---|---|
| `kla_relations`, `daehan_relations` | 한자랑 자체 큐레이션 | 앱과 동일 |

> 아래 출처의 데이터를 담은 팩을 추가할 때는 **이 표와 고지 문구를 반드시 함께 갱신**해야
> 합니다. 앱 안에도 같은 고지가 있습니다(설정 → 정보).
>
> - 획순 데이터 [Make Me a Hanzi](https://github.com/skishore/makemeahanzi) — **Arphic Public
>   License** (배포 시 고지 의무)
> - 한국어 훈음 데이터 libhangul — BSD
> - 우리말샘(국립국어원) 기반 어휘 데이터 — **CC BY-SA 2.0 KR** (출처 표시·동일조건 변경허락)
