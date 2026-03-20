# RunBeat - Enumalabs 배포 계획

## 앱 정보

| 항목 | 값 |
|------|-----|
| 앱 이름 | `running-playlist` |
| 접속 URL | `https://running-playlist.enumalabs.com` |
| 컨테이너명 | `enumalabs-running-playlist` |
| 프레임워크 | 정적 HTML (nginx) |
| 폴더 경로 | `apps/running-playlist/` |

---

## 현재 상태

- [x] `index.html` 단일 파일 완성 (HTML/CSS/JS)
- [x] 테마별 플레이리스트 생성 (기분/상황/BPM)
- [x] 1시간 분량 자동 구성
- [x] 내 플리 탭 (저장된 플레이리스트 조회)
- [x] 러닝 기록 + 플레이리스트 연동
- [x] 대시보드 (통계, 차트, 기록 목록)
- [x] localStorage 기반 데이터 저장

---

## 배포 방식: 정적 HTML (가장 단순)

순수 HTML/CSS/JS로만 구성되어 있으므로 nginx 기반 정적 배포를 사용합니다.

### Dockerfile

```dockerfile
FROM nginx:alpine
COPY . /usr/share/nginx/html/
EXPOSE 80
```

### 디렉토리 구조

```
apps/running-playlist/
├── Dockerfile
└── index.html
```

---

## 배포 절차

### Step 1. Dockerfile 생성

`running-playlist/` 폴더에 위의 Dockerfile을 추가합니다.

### Step 2. 코드 전달

**방법 A (권장): 웹에서 ZIP 업로드**
1. `https://enumalabs.com/import/` 접속
2. 앱 이름: `running-playlist`, 제목: `RunBeat - 러닝 플레이리스트`
3. `running-playlist/` 폴더를 ZIP으로 압축 후 업로드
4. AI가 Dockerfile 확인 → Git Push → 배포 자동 처리

**방법 B: Git Push**
```bash
cd apps/running-playlist
git init
git remote add origin https://git.enumalabs.com/apps/running-playlist.git
git add .
git commit -m "feat: RunBeat 러닝 플레이리스트 앱 초기 배포"
git push -u origin main
```

### Step 3. 관리자에게 배포 요청

- Django Admin에서 앱 등록 (이름, 도메인, 접근 권한)
- 배포 실행

---

## 인증 연동 (선택사항)

현재는 localStorage에 데이터를 저장하므로 사용자별 데이터 분리가 안 됩니다.
추후 사용자별 데이터가 필요하면:

1. **백엔드 추가** (FastAPI/Django)
2. 인증 헤더 활용:
   - `X-User-Email`: 사용자 이메일
   - `X-User-Name`: 사용자 이름
   - `X-User-Id`: 사용자 DB ID
3. `/app/storage`에 사용자별 데이터 저장
4. Dockerfile을 FastAPI 템플릿으로 변경

```python
# 예시: FastAPI로 사용자별 데이터 관리
@app.get("/api/records")
async def get_records(request: Request):
    user_id = request.headers.get("X-User-Id")
    # user_id별로 /app/storage에서 데이터 로드
```

---

## 향후 확장 가능성

| 기능 | 난이도 | 설명 |
|------|--------|------|
| 사용자별 데이터 | 중 | FastAPI 백엔드 + `/app/storage` DB |
| Spotify 연동 | 중 | Spotify API로 실제 재생/플레이리스트 내보내기 |
| 러닝 GPS 추적 | 상 | Geolocation API로 경로 기록 + 지도 표시 |
| 크루 기능 | 상 | 다른 사용자와 기록 공유, 랭킹 |
| 환경변수 | - | 필요 시 `SPOTIFY_CLIENT_ID` 등 Django Admin에서 관리 |

---

## 주의사항

- 앱 이름은 **영문 소문자, 숫자, 하이픈(`-`)**만 사용
- `EXPOSE` 포트(80)와 nginx 기본 포트 일치 확인
- 대용량 파일(음악 파일 등)은 Git에 올리지 않기
- 현재 정적 파일만이므로 환경변수, 스토리지 설정 불필요
