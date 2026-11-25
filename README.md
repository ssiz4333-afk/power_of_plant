# 🌿 식물의 힘 — 도테라 AI 아로마 상담 (체험판 v1.4)

도테라 에센셜 오일 24종을 **한국어 · English · 日本語**로 상담하는 프리미엄 모바일 웹앱입니다.
서버·빌드 없이 `index.html` 한 장으로 동작하고, 그대로 PWA 설치·앱스토어 패키징까지 이어집니다.

![chat](preview/01_chat.jpg)

| 상담 답변 | 식물 도감 | 멤버십(유료화 미리보기) |
|---|---|---|
| ![answer](preview/02_answer.jpg) | ![library](preview/04_library.jpg) | ![membership](preview/06_membership.jpg) |

## 무엇이 들어 있나

| 기능 | 설명 |
|---|---|
| **오늘의 향기** | 시간대·계절·요일에 맞춰 7가지 큐레이션 중 1장 — 실제 오일 병 사진, 무드 카피, 한 줄 레시피, '이 향으로 상담하기' |
| **AI 아로마 상담** | 자연어 질문 → 관련 오일 2~3종 + 블렌딩 레시피 + 주의사항 카드 |
| **증상별 가이드** | 수면·두통·소화·호흡기·면역·피부·집중·감정 8개 카테고리 — 테마별 파스텔 아이콘 |
| **식물 도감** | 싱글 14 + 블렌드 10 = 24종, 검색·필터·상세(A/T/I·효능·레시피·주의·궁합) |
| **3개국어** | `KR / EN / JP` 즉시 전환. 질문 언어를 자동 감지해 그 언어로 답변 |
| **안전장치** | 희석 비율·주의사항을 답변마다 표시, 질병 진단·치료 대체 아님 고지(3개국어) |
| **모션·감성 디테일** | '오늘의 향을 고르는 중…' 0.4초 향 고르기 → 답변 카드 상승 + 오일 카드 70ms 순차 등장, 아치 유리 부유감·빛결, 쫀득한 터치 피드백, 탭 페이드 전환 (`prefers-reduced-motion` 대응) |
| **체험판 쿼터** | 하루 5회 무료 + 멤버십 미리보기 모달 (유료화 연결 지점) |
| **PWA** | 홈 화면 설치, 오프라인 동작, 앱 아이콘 4종 |

## 바로 쓰기

```bash
# 가장 빠른 방법: 파일 열기
open index.html          # 또는 브라우저로 드래그

# 기기에서 보기 (같은 와이파이)
python3 -m http.server 8000
# → http://<내 IP>:8000
```

배포·앱 패키징은 **[DEPLOY.md](DEPLOY.md)**, 보안·유료화·앱스토어 설계는 **[ARCHITECTURE_SECURITY.md](ARCHITECTURE_SECURITY.md)** 를 보세요.

## 구조

```
index.html      앱 전체 (UI + 3개국어 + 24종 지식 DB + 매칭 엔진 + askAromaAI 어댑터)
manifest.json   PWA 매니페스트
sw.js           오프라인 캐시
icon-*.png      앱 아이콘 (192 / 512 / maskable / apple-touch)
assets/oils/    도테라 코리아 공식 오일 병 사진 24종 (id.webp · 높이 300px · 투명 배경)
server/         라이브 LLM 모드용 Supabase 스키마·Edge Function (선택)
tools/          아이콘 생성, 정적 검증, 브라우저 E2E
```

## 라이브 AI 모드로 전환

`index.html` 상단의 한 줄만 바꾸면 됩니다.

```js
var CONFIG = {
  apiBase: 'https://<PROJECT_REF>.supabase.co/functions/v1/aroma-ai',  // ← 여기
  freePerDay: 5
};
```

- 비어 있으면 → 내장 로컬 지식 엔진 (`mode:'demo'`, 키·서버 불필요)
- 채우면 → 실제 LLM RAG 답변 (`mode:'live'`)
- 서버가 죽어도 → 자동으로 로컬 엔진으로 폴백 (앱이 멈추지 않음)

## 검증

```bash
python3 tools/verify.py    # 구문·지식DB 무결성·오늘의 향기·i18n·PWA·v1.4 모션 ← 77항목
python3 tools/e2e.py       # 브라우저 E2E + 스크린샷        ← 31항목 (Chromium 필요)
```

현재 상태: **verify 77/77 · e2e 31/31 통과, 콘솔 오류 0건.**

## 브랜드 바꾸기

```js
brand: { ko: '식물의 힘', en: 'Power of Plants', ja: '植物の力', handle: '@erang_coach' }
```

이름·핸들·색상(`:root`의 CSS 변수)만 바꾸면 전체 톤이 따라옵니다.

---

> ⚠️ 이 앱은 도테라 코리아의 공식 앱이 아닙니다. 질병의 진단·치료를 대체하지 않는 웰니스 활용 가이드이며,
> 제품명·상표는 각 권리자의 소유입니다. 사용 전 전문가 상담을 권장합니다.
