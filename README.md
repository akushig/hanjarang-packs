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
  "baseUrl": "https://cdn.example/",
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
- `baseUrl`(선택)이 있으면 앱은 **팩 파일만** 그 주소에서 받습니다. manifest 자체는 계속
  이 저장소에 머뭅니다. 나중에 팩을 R2·CDN으로 옮길 때 **앱 업데이트 없이** 전환하기 위한
  우회로입니다 — 베이스 URL이 앱에 박혀 있으면 스토어 심사가 필요하고, 업데이트하지 않은
  구버전 앱은 영구히 옛 위치를 보게 됩니다. http(s)가 아니면 앱이 무시합니다.
  설정: `node backend/tools/publish_pack_gh.mjs --set-base-url <URL>` (빈 문자열로 되돌림)

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
| `kla_relations`, `daehan_relations` | 한자랑 자체 큐레이션(유의자·반의자·약자) | 앱과 동일 |
| `qual_hanja` | 각 기관이 공개한 급수별 배정한자 목록(한국어문회·대한검정회 등) + 훈음 | 배정한자 목록은 각 기관 공개 자료. 훈음 일부는 libhangul(BSD) |
| `kla_words`, `daehan_words` | **우리말샘**(국립국어원) 기반 한자어·뜻풀이 + 자체 큐레이션 | **CC BY-SA 2.0 KR** |

### 우리말샘 출처 표시 (CC BY-SA 2.0 KR)

`kla_words`·`daehan_words`에 포함된 한자어 표제·뜻풀이는 국립국어원 **우리말샘**
(<https://opendict.korean.go.kr>)의 데이터를 가공한 것입니다. 원 저작물의 라이선스인
[크리에이티브 커먼즈 저작자표시-동일조건변경허락 2.0 대한민국](https://creativecommons.org/licenses/by-sa/2.0/kr/)에
따라 배포하며, 이 팩을 다시 가공·배포할 때도 같은 조건을 적용해야 합니다.

### 획순 데이터

`packs/` 에는 **획순 데이터를 넣지 않습니다.** 획순은
[Make Me a Hanzi](https://github.com/skishore/makemeahanzi) 기반이고 **Arphic Public
License**의 고지 의무가 따르므로, 앱 번들에만 유지합니다(앱 설정 → 정보에 고지가 있습니다).
앞으로 획순을 팩으로 옮기게 되면 이 문서에 Arphic 고지 전문을 함께 실어야 합니다.

> 새 팩을 추가할 때는 **이 표와 고지 문구를 반드시 함께 갱신**하십시오. 공개 저장소에 올리는
> 것은 곧 재배포입니다. 앱 안에도 같은 고지가 있습니다(설정 → 정보).
