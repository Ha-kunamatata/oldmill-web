# 올드밀 웹사이트 (oldmill-web)

충남 아산 가족 레스토랑 **The Old Mill** 의 고객용 정적 사이트.
배포: https://oldmill-web.pages.dev (Cloudflare Pages, main push → 자동 배포)

> **이 문서는 운영 룰만 담는다. 스택·폴더 구조·디자인 토큰·페이지 목록·배포 흐름·롤백 절차는 README.md를 단일 출처로 본다.**
> 변경 작업 들어가기 전에 README.md 먼저 읽을 것.

## admin 프로젝트와의 관계
- `c:\oldmill-admin` — 사장님·직원 전용 내부 도구 (React + Supabase, 인증 필요)
- `c:\oldmill-web` — 일반 손님이 보는 공개 웹사이트 (Astro 정적, 인증 없음)
- **두 코드베이스는 완전 별개**. 컴포넌트/스타일/유틸 공유 없음. 한쪽 결정을 다른 쪽에 무단 이식 금지.

## 코딩 규칙
- **스타일은 Tailwind v4 토큰만 사용** — 색을 바꿀 때는 `src/styles/global.css`의 `@theme` 블록만 수정. 페이지·컴포넌트에 색 hex 직접 박지 말 것.
- **컴포넌트 재사용 우선** — `BaseLayout` / `PageHero` / `Section` / `CTASection` / `Gallery` / `SmartImage` / `ScrollReveal` 등 이미 만들어진 것을 먼저 찾아 쓸 것. 새 컴포넌트는 정말 필요할 때만.
- **이미지 파일명은 영문·숫자·하이픈만** (한글·공백 금지). 권장 가로 1600px 이하, 1MB 이하, webp.
- **import 경로 대소문자 엄격하게** — Windows 로컬에선 통과해도 Cloudflare Linux 빌드에서 실패함.
- 한국어 주석 OK. 인라인 스타일보다 Tailwind 클래스 우선.
- 모바일 반응형 필수. `Header.astro`에 모바일 햄버거 패턴 있음 — 새 페이지도 동일 패턴 따를 것.

## 워크플로우
1. 변경 전 관련 파일 먼저 읽고, 어떻게 변경할지 한국어로 설명
2. 사용자가 "OK" 또는 "진행해" 하면 코드 적용
3. dev 서버는 이미 켜져 있다고 가정 (`npm run dev`, http://localhost:4321)
4. 사용자가 브라우저 확인 후 OK 하면 `git commit`
5. `git push`는 사용자가 명시적으로 "푸시해" 라고 요청할 때만

## 절대 하지 말 것
- ❌ 사용자 확인 없이 `git push` 금지 — 푸시하면 1~2분 안에 손님이 보는 사이트에 반영됨
- ❌ `git reset --hard` / `git push --force` / `git tag -d` / 태그 이동 — 사용자 명시 요청 없이는 금지
- ❌ `v1.0-baseline`, `v1.1-design-upgrade` 등 기존 백업 태그 건드리지 말 것 (롤백 안전망)
- ❌ `package.json`의 `overrides.vite` 임의 제거 — Tailwind v4 호환성 때문에 고정해둔 것
- ❌ 큰 변경 한 번에 다 하기 — 단계별로 나눠서 사용자에게 확인 받기
- ❌ `public/images/` 안의 기존 사진을 무단으로 덮어쓰거나 삭제 (다른 페이지가 참조 중일 수 있음 — 먼저 grep으로 사용처 확인)

## 자주 쓰는 명령
- `npm run dev` — 로컬 개발 서버 (포트 4321)
- `npm run build` — 프로덕션 빌드 (`dist/`로 출력). 푸시 전에 빌드 통과 확인하면 Cloudflare 빌드 실패 예방됨
- `npm run preview` — 빌드 결과물 로컬 미리보기

## 트러블슈팅
README.md "12. 자주 만나는 문제 / 트러블슈팅" 섹션 참조. 추가로:
- dev 서버 포트 충돌: 4321이 막혀 있으면 Astro가 자동으로 4322 등으로 시도함
- 변경이 브라우저에 안 보임: `Ctrl + Shift + R` 강력 새로고침 → 그래도 안 되면 dev 서버 재시작
