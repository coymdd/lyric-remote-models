# Lyric Remote 번역 모델

IndexTeam의 [Index-Translate 9B](https://huggingface.co/IndexTeam/Index-Translate-9B)를 바탕으로 만든 비공식 경량 모델입니다. 모델과 배포 안내만 제공하며 앱 소스나 사용자 데이터는 포함하지 않습니다.

## Index-Translate 9B 초경량형 · 2026-10-30

[모델 배포 페이지](https://github.com/coymdd/lyric-remote-models/releases/tag/index9-slim-20261030)

다운로드를 지원하는 새 Lyric Remote 앱에서 **Index-Translate 9B 초경량형 → 3.08GB 다운로드**를 선택하면 됩니다. 이전의 파일 가져오기 전용 IPA에는 앱 다운로드 기능이 없습니다.

모델 전체 크기는 3,080,171,360바이트입니다. 깃허브의 파일당 2GiB 제한 때문에 두 파일로 나누어 보관합니다. 앱은 두 파일을 순서대로 받아 각각의 크기·SHA-256을 검증한 뒤 원래 GGUF 하나로 합칩니다. 조각 하나를 모델로 가져오면 사용할 수 없습니다.

맥·리눅스에서 직접 합칠 때:

```sh
cat Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf.part1 Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf.part2 > Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf
shasum -a 256 Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf
```

원래 모델 SHA-256: `3aa6dc5f98dabc1b831ce40b3f62b982e3c71d643ca32c9405fc0af738f31ea2`

조각별 크기와 해시는 model-manifest.json에 있습니다.

## 변경 내용과 검증 범위

고정한 공식 F16 원본에서 중요도 기반 양자화를 적용했습니다. 입력 임베딩과 출력층 두 텐서는 IQ2_S이며 나머지 440개 텐서는 앞서 만든 경량형과 바이트까지 같습니다. 사용하지 않는 MTP를 포함한 442개 텐서의 이름과 모양을 유지했습니다. 문맥 4K, F16 캐시·상태, 배치 64/64를 사용했습니다. IndexTeam 공식 배포본은 아닙니다.

Apple M5 Pro·24GB·macOS 27.0.1에서 Lyric Remote 실제 번역 엔진을 사용하는 CLI의 실행 중 최대 상주 메모리는 3,326,918,656바이트였습니다. 기존 경량형의 검사 전체 최대는 3,790,635,008바이트였습니다. 앱 화면 비용은 포함하지 않았고 이 새 모델의 아이패드·아이폰 메모리는 검증하지 않았습니다. 강제 메모리 상한을 보장하는 수치가 아닙니다.

결과 생성 전에 고정한 일본어→한국어 합성 문장 64개에서 Codex 한 평가자가 판정한 중대 의미 오류는 실제 앱 Index 2B 19개, 기존 경량형 7개, 이 모델 6개였습니다. 사람이 만든 정답이나 독립된 평가가 아니며 모든 가사에서의 정확도 우위를 뜻하지 않습니다. 새로운 의미 오류도 남아 있습니다. 같은 문장군의 전체 실행 시간은 새 모델 40.12초, 2B 14.85초였습니다. 합성 이야기 7개를 같은 프로세스에서 각각 3회 생성한 240줄 검사는 모두 완료됐고 이야기별 세 출력이 같았습니다.

## 출처와 이용 조건

원본 모델 카드가 표시한 라이선스는 Apache-2.0입니다. LICENSE, NOTICE, UPSTREAM_README.md에서 원본의 출처와 고지를 확인할 수 있습니다.
