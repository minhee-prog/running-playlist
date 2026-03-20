# CLAUDE.md

## 프로젝트 개요
RunBeat - 러닝 플레이리스트 생성 & 기록 앱

## 구조
- 단일 파일 `index.html` (HTML/CSS/JS 올인원)
- localStorage 기반 데이터 저장
  - `rb_playlists`: 저장된 플레이리스트
  - `rb_records`: 러닝 기록
  - `rb_theme`: 선택된 테마

## 기능
- 테마별 플레이리스트 생성 (기분/상황/BPM)
- 1시간 분량 자동 구성 (~150곡 DB)
- 곡 클릭 → YouTube 검색 이동
- 내 플리 탭 (저장된 플레이리스트 조회)
- 러닝 기록 + 플레이리스트 연동
- 대시보드 (통계, Chart.js 차트, 기록 목록)
- UI 테마 6종: 미드나잇/네온/오션(다크), 선셋/포레스트/체리(라이트)

## 로컬 서버
```bash
python3 -m http.server 8192 -d running-playlist
```
- port: 8192

## 배포
- GitHub Pages: https://minhee-prog.github.io/running-playlist/
- Enumalabs: plan.md 참조
- Dockerfile: nginx:alpine 정적 배포

## 스타일링
- CSS 커스텀 프로퍼티로 테마 관리 (`data-theme` 속성)
- 주요 변수: `--bg`, `--surface`, `--accent`, `--grad-btn`, `--grad-record`, `--shadow`, `--card-border`
