# 올드밀 웹사이트 (oldmill-web)

충남 아산 가족 레스토랑 **The Old Mill** 의 공식 웹사이트.

> 🔗 라이브: https://oldmill-web.pages.dev (임시 도메인)  
> 📁 레포: 비공개 (Private)

---

## 1. 한눈에 보기

```
[ 내 노트북 ]                [ GitHub ]              [ Cloudflare ]
                                                    
  코드 작성     ──git push──▶  레포 저장   ──webhook──▶  자동 빌드
  npm run dev                  (private)              자동 배포
       ↓                                                  ↓
  localhost:4321                                  oldmill-web.pages.dev
  (나만 보는 미리보기)                          (전 세계가 보는 실서비스)
```

핵심 원칙: **서버 운영 0대, 비용 0원, 배포 명령어 0개.**  
`git push` 한 번이면 1~2분 뒤 인터넷에 자동 반영.

---

## 2. 사용 기술 스택

| 영역 | 기술 | 역할 | 비용 |
|---|---|---|---|
| 프레임워크 | [Astro](https://astro.build) v6 | 정적 사이트 생성기 | 무료 |
| 스타일 | [Tailwind CSS](https://tailwindcss.com) v4 | 유틸리티 CSS | 무료 |
| 호스팅 | [Cloudflare Pages](https://pages.cloudflare.com) | 정적 호스팅 + CDN | 무료 |
| 코드 저장소 | [GitHub](https://github.com) | 버전 관리 | 무료 (Private) |
| DNS | Cloudflare | 도메인 → 서버 연결 | 무료 |
| SSL | Cloudflare 자동 발급 | HTTPS 인증서 | 무료 |
| 폰트 | Pretendard, Cormorant Garamond | 한글/영문 타이포 | 무료 |

**총 운영비: 도메인 비용 외 0원** (도메인 미연결 상태에선 0원)

---

## 3. 각 기술 설명

### 3-1. Astro — "사이트 만드는 도구"

워드프레스나 PHP처럼 사용자가 접속할 때마다 서버에서 페이지를 만들어내는 방식이 아니라, **빌드 시점에 미리 HTML을 다 만들어두는** 정적 사이트 생성기.

#### 왜 Astro인가
- 결과물이 단순 HTML/CSS/JS → **서버 필요 없음** (정적 호스팅으로 충분)
- 사용자 응답 속도 매우 빠름 (DB 조회 0번, PHP 실행 0번)
- 해킹 표면이 거의 없음 (실행되는 백엔드가 없으니 SQL 인젝션 등 불가)
- 콘텐츠 위주 사이트(레스토랑/포트폴리오/블로그)에 최적

#### 작동 흐름
```
.astro 파일들 → npm run build → dist/ 폴더에 정적 HTML 생성 → Cloudflare가 그대로 서빙
```

#### 폴더 구조 규칙
- `src/pages/` 안의 `.astro` 파일이 그대로 URL이 됨
  - `src/pages/index.astro` → `/`
  - `src/pages/about.astro` → `/about`
  - `src/pages/space/1f-hall.astro` → `/space/1f-hall`
- `src/components/` — 재사용 가능한 UI 조각
- `src/layouts/` — 페이지 공통 골격 (헤더/푸터 포함)
- `src/styles/` — 전역 CSS
- `public/` — 이미지, favicon 등 정적 파일 (빌드 시 그대로 복사)

### 3-2. Tailwind CSS — "스타일링 도구"

CSS 파일을 따로 안 쓰고 HTML 클래스로 직접 스타일을 적용하는 방식.

```html
<!-- 기존 CSS 방식 -->
<button class="my-btn">예약</button>
<style>.my-btn { padding: 12px 24px; background: black; }</style>

<!-- Tailwind 방식 -->
<button class="px-6 py-3 bg-black text-white rounded-lg">예약</button>
```

#### 커스텀 토큰 (디자인 시스템)
이 프로젝트는 `src/styles/global.css`의 `@theme` 블록에서 색상/폰트 정의:
- `bg-bg`, `text-ink`, `text-muted`, `text-accent`, `bg-cta` 등
- 모든 페이지/컴포넌트가 이 토큰만 사용 → 색 바꾸려면 `global.css` 한 곳만 수정

### 3-3. Cloudflare Pages — "호스팅 + CDN"

GitHub 레포에 코드 푸시되면 자동으로 빌드해서 전 세계 데이터센터에 뿌려주는 서비스.

#### 작동 흐름
```
1. GitHub에 푸시 발생
2. Cloudflare가 webhook으로 감지
3. 빌드 환경 컨테이너 띄움 → npm install → npm run build
4. 결과물(dist/)을 전 세계 200+ 데이터센터에 배포
5. oldmill-web.pages.dev 에서 즉시 확인 가능
```

#### 무료 플랜 한도 (현실적으로 절대 못 채움)
- 트래픽: 무제한
- 빌드: 월 500회 (= 하루 16번 푸시)
- 동시 빌드: 1개

### 3-4. GitHub — "코드 저장소 + 협업 + 백업"

코드의 모든 변경 이력을 저장. 주요 사용:
- `git push` → Cloudflare 배포 트리거
- `git tag` → 특정 시점 백업 (롤백 가능)
- Private 설정 → 코드 비공개

---

## 4. 프로젝트 구조

```
oldmill-web/
├── public/                      ← 정적 자산 (이미지/favicon)
│   ├── favicon.svg
│   └── images/
│       ├── hero/                ← 홈 메인 이미지
│       ├── space/               ← 공간 사진
│       ├── events/              ← 행사 사진
│       └── about/               ← 소개 페이지 사진
│
├── src/
│   ├── pages/                   ← URL별 페이지 (파일명 = URL)
│   │   ├── index.astro          → /
│   │   ├── about.astro          → /about
│   │   ├── contact.astro        → /contact
│   │   ├── 404.astro            → 존재하지 않는 URL
│   │   ├── space/
│   │   │   ├── index.astro      → /space
│   │   │   ├── 1f-hall.astro    → /space/1f-hall
│   │   │   ├── 2f-dining.astro  → /space/2f-dining
│   │   │   └── 3f-rooms.astro   → /space/3f-rooms
│   │   └── events/
│   │       ├── doljanchi.astro  → /events/doljanchi
│   │       ├── wedding.astro    → /events/wedding
│   │       └── pizza-class.astro → /events/pizza-class
│   │
│   ├── layouts/
│   │   └── BaseLayout.astro     ← 모든 페이지 공통 골격
│   │
│   ├── components/              ← 재사용 UI 컴포넌트
│   │   ├── Header.astro         ← 상단 네비게이션 + 모바일 햄버거
│   │   ├── Footer.astro         ← 하단 푸터
│   │   ├── PageHero.astro       ← 페이지 상단 큰 제목 영역
│   │   ├── Section.astro        ← 본문 섹션 공통 박스
│   │   ├── CTASection.astro     ← 페이지 하단 행동 유도 박스
│   │   ├── Picture.astro        ← 단일 이미지 (페이드인)
│   │   ├── Gallery.astro        ← 사진 격자 갤러리
│   │   └── ScrollReveal.astro   ← 스크롤 시 등장 애니메이션
│   │
│   └── styles/
│       └── global.css           ← Tailwind v4 + 색상/폰트 토큰
│
├── astro.config.mjs             ← Astro 설정
├── package.json                 ← npm 의존성 정의
├── tsconfig.json
└── README.md                    ← 이 파일
```

---

## 5. 페이지 목록

| 경로 | 설명 |
|---|---|
| `/` | 홈 — 히어로, 3대 행사 소개, 공간 미리보기 |
| `/about` | 소개 — 올드밀 운영 이야기, 가치 |
| `/space` | 공간 안내 — 3개 층 개요 |
| `/space/1f-hall` | 1층 이벤트 홀 (돌잔치/피자체험/스몰웨딩) |
| `/space/2f-dining` | 2층 메인 다이닝 (16 테이블) |
| `/space/3f-rooms` | 3층 A·B룸 (프라이빗 스몰웨딩) |
| `/events/doljanchi` | 돌잔치 패키지 안내 |
| `/events/wedding` | 스몰웨딩 패키지 안내 |
| `/events/pizza-class` | 어린이 피자체험 안내 |
| `/contact` | 문의/예약 폼 (백엔드 연결 예정) |
| `/404` | 존재하지 않는 URL 안내 |

---

## 6. 로컬 개발

### 사전 준비
- [Node.js](https://nodejs.org) 20 이상
- [Git](https://git-scm.com)
- VSCode 권장

### 처음 클론할 때

```sh
git clone https://github.com/Ha-kunamatata/oldmill-web.git
cd oldmill-web
npm install
```

### 매일 작업 시작할 때

```sh
cd C:\oldmill-web
npm run dev
```

브라우저에서 `http://localhost:4321` 접속.  
코드 수정 후 저장하면 브라우저 자동 새로고침.

### 작업 끝낼 때
PowerShell에서 `Ctrl + C`로 dev 서버 종료. 또는 그냥 창 닫기.

### 자주 쓰는 npm 명령어

| 명령 | 설명 |
|---|---|
| `npm run dev` | 로컬 개발 서버 시작 (파일 변경 시 자동 새로고침) |
| `npm run build` | 프로덕션 빌드 (`dist/` 폴더에 결과물 생성) |
| `npm run preview` | 빌드 결과물을 로컬에서 미리보기 |

---

## 7. 배포 워크플로우

### 일반 작업 흐름

```sh
# 1. 코드 수정 (VSCode에서)

# 2. 커밋
git add .
git commit -m "어떤 변경인지 설명"

# 3. 푸시 → Cloudflare가 자동 빌드/배포
git push
```

1~2분 후 `oldmill-web.pages.dev`에 반영.

### 배포 상태 확인
1. [Cloudflare 대시보드](https://dash.cloudflare.com) 로그인
2. **Workers & Pages** → `oldmill-web` → **Deployments** 탭
3. 최신 배포에 ✅ Success 표시 확인

### 빌드 실패 시
대시보드의 실패한 배포 클릭 → 빌드 로그에서 에러 메시지 확인.  
주요 원인:
- import 경로 오타 (대소문자 차이 — Windows는 관대하지만 Linux는 엄격)
- 누락된 컴포넌트 파일
- TypeScript 타입 에러

---

## 8. 백업 / 롤백 (Git 태그)

중요한 시점은 태그로 박제:

```sh
git tag -a v1.0-baseline -m "초기 완성본"
git push origin v1.0-baseline
```

### 현재 태그 목록
- `v1.0-baseline` — 초기 사이트 완성 (디자인 업그레이드 전)
- `v1.1-design-upgrade` — 디자인 시스템 정합화 + 마이크로 인터랙션

### 특정 시점으로 돌아가기 (보기만)

```sh
git checkout v1.0-baseline   # 그 시점 코드로 이동
git checkout main            # 다시 최신으로 복귀
```

### 완전히 되감기 (주의: 이후 커밋 사라짐)

```sh
git reset --hard v1.0-baseline
git push --force
```

---

## 9. 콘텐츠 업데이트 가이드

### 텍스트 수정
해당 페이지 파일(`src/pages/...`) 열어서 텍스트 수정 → 저장 → 커밋/푸시.

### 사진 추가/교체
1. 사진을 `public/images/` 안의 적절한 폴더에 넣기
2. 코드에서 `<img src="/images/..." />` 경로 맞추기
3. 파일명은 영문/숫자/하이픈만 (한글/공백 금지)
4. 권장 크기: 가로 1600px 이하, 1MB 이하 (webp 추천)

### 새 페이지 추가
`src/pages/` 안에 `새페이지.astro` 만들면 자동으로 `/새페이지` URL 생성.  
기본 골격:

```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import PageHero from '../components/PageHero.astro';
---

<BaseLayout title="페이지 제목">
  <PageHero eyebrow="EYEBROW" title="큰 제목" subtitle="부제" />
  <!-- 본문 -->
</BaseLayout>
```

### 헤더 메뉴 변경
`src/components/Header.astro` 상단의 `navItems` 배열 수정.

### 색상 변경
`src/styles/global.css`의 `@theme` 블록에서 `--color-*` 변수만 수정.  
모든 페이지에 자동 반영.

---

## 10. 디자인 시스템

### 색상 토큰

| 변수 | 용도 | 값 |
|---|---|---|
| `--color-bg` | 메인 배경 | `#FAF7F2` (아이보리) |
| `--color-ink` | 본문 텍스트 | `#2A2520` (진한 브라운블랙) |
| `--color-muted` | 보조 텍스트 | `#6B635C` (그레이브라운) |
| `--color-accent` | 포인트 | `#8B6F47` (우드 톤) |
| `--color-cta` | CTA 버튼 | `#1F1B16` (딥 블랙) |
| `--color-border-warm` | 구분선 | `#E8E0D5` (옅은 베이지) |

### 타이포그래피 클래스
- `.text-display` — 홈 히어로용 거대 제목 (반응형)
- `.text-headline` — 일반 페이지 제목
- `.text-eyebrow` — 영문 윗줄 (예: "ABOUT")
- `.prose-body` — 본문 (한글 가독성 최적화)

### 컴포넌트 클래스
- `.btn .btn-cta` — 검은 채움 메인 버튼
- `.btn .btn-outline` — 외곽선 보조 버튼
- `.btn .btn-light` — 어두운 배경 위의 흰 버튼
- `.card-bordered` — 외곽선 카드
- `.card-hover` — 호버 시 살짝 떠오르는 카드
- `.link-underline` — 좌→우 슬라이드 밑줄 링크
- `.reveal` — 스크롤 시 페이드인+위로 등장
- `.reveal-stagger` — 자식 요소들 연쇄 등장

---

## 11. 향후 작업 로드맵

### 단기
- [ ] 실제 올드밀 사진 촬영 및 교체
- [ ] 문의 폼 백엔드 연결 (Resend + 카카오 알림톡)
- [ ] 도메인 연결 (`oldmill.co.kr` 등)
- [ ] 개인정보처리방침 / 이용약관 작성
- [ ] favicon 리브랜딩 (로고 적용)

### 중기
- [ ] SEO 메타태그 + JSON-LD (Restaurant 스키마)
- [ ] 네이버 서치어드바이저 / 구글 서치콘솔 등록
- [ ] 사이트맵 자동 생성
- [ ] OG 이미지 (카톡/SNS 공유 미리보기)
- [ ] Google Analytics 또는 Plausible 연결

### 장기
- [ ] CMS 연동 (Notion API 또는 Sanity) — 비개발자도 콘텐츠 수정 가능하게
- [ ] 갤러리 라이트박스 (사진 클릭 시 크게 보기)
- [ ] 다국어 지원 (한/영, 외국 손님용)
- [ ] 후기/리뷰 섹션

---

## 12. 자주 만나는 문제 / 트러블슈팅

### dev 서버가 안 뜸

```sh
# 의존성 다시 설치
Remove-Item -Recurse -Force node_modules, .astro, package-lock.json
npm install
npm run dev
```

### 브라우저에서 변경사항이 안 보임
- 브라우저 강력 새로고침: `Ctrl + Shift + R`
- 그래도 안 되면 dev 서버 재시작 (`Ctrl + C` → `npm run dev`)

### Cloudflare 빌드 실패
- import 경로 대소문자 확인 (Linux 빌드 환경은 엄격함)
- 로컬에선 `npm run build`로 빌드가 통과하는지 먼저 테스트

### Vite 버전 경고

```sh
npm pkg set "overrides.vite=^7"
npm install
```

---

## 13. 참고 링크

- [Astro 공식 문서](https://docs.astro.build)
- [Tailwind CSS v4 문서](https://tailwindcss.com/docs)
- [Cloudflare Pages 문서](https://developers.cloudflare.com/pages/)
- [Pretendard 폰트](https://github.com/orioncactus/pretendard)

---

## 14. 운영 정보

- **상호**: The Old Mill (올드밀)
- **위치**: 충남 아산시
- **운영시간**: 매일 11:00 — 21:30 (라스트오더 점심 15:00 / 저녁 20:30)
- **전화**: 041-546-1080
- **운영 시작**: 2015년

---

*Last updated: 2026-04*  
*Maintained by: Ha-kunamatata*