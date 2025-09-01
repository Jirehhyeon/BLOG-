# 블로그 자동화 시스템 설치 가이드 🛠️

## 📋 사전 요구사항

### 필수 소프트웨어
- **Docker Desktop** (v20.10 이상)
- **Git** (v2.30 이상)
- **Node.js** (v16 이상, 선택사항)

### 필요 계정 및 API 키
1. **YouTube Data API v3**
   - [Google Cloud Console](https://console.cloud.google.com) 접속
   - 새 프로젝트 생성
   - YouTube Data API v3 활성화
   - API 키 생성

2. **NoteGPT API**
   - [NoteGPT](https://notegpt.io) 가입
   - API 키 발급

3. **OpenAI API**
   - [OpenAI Platform](https://platform.openai.com) 가입
   - API 키 생성
   - 크레딧 충전

4. **WordPress**
   - WordPress 관리자 계정
   - Application Password 생성
   - REST API 활성화 확인

5. **Instagram (Meta)**
   - Facebook Developer 계정
   - Instagram Basic Display API 설정
   - Access Token 발급

## 🚀 설치 단계

### Step 1: 저장소 클론
```bash
git clone https://github.com/Jirehhyeon/BLOG-.git
cd blog-automation-study
```

### Step 2: 환경 변수 설정
```bash
# .env 파일 생성
cp .env.example .env

# .env 파일 편집하여 API 키 입력
nano .env
```

### Step 3: Docker 컨테이너 실행
```bash
# Docker Compose 실행
docker-compose up -d

# 컨테이너 상태 확인
docker-compose ps

# 로그 확인
docker-compose logs -f n8n
```

### Step 4: N8N 초기 설정
1. 브라우저에서 http://localhost:5678 접속
2. 관리자 계정 생성
3. 기본 설정 완료

### Step 5: 워크플로우 임포트
1. N8N 대시보드에서 'Import Workflow' 클릭
2. `workflows/blog-automation-complete.json` 파일 선택
3. 임포트 완료

### Step 6: 크레덴셜 설정
각 노드별 크레덴셜 설정:

#### YouTube API
```json
{
  "apiKey": "YOUR_YOUTUBE_API_KEY"
}
```

#### NoteGPT API
```json
{
  "apiKey": "YOUR_NOTEGPT_API_KEY",
  "baseUrl": "https://api.notegpt.io/v1"
}
```

#### OpenAI
```json
{
  "apiKey": "YOUR_OPENAI_API_KEY",
  "organization": "YOUR_ORG_ID" // 선택사항
}
```

#### WordPress
```json
{
  "url": "https://your-blog.com",
  "username": "your-username",
  "password": "APPLICATION_PASSWORD"
}
```

#### Instagram
```json
{
  "accessToken": "YOUR_INSTAGRAM_ACCESS_TOKEN",
  "accountId": "YOUR_INSTAGRAM_ACCOUNT_ID"
}
```

## 🧪 테스트

### 워크플로우 테스트
1. N8N에서 워크플로우 열기
2. 'Execute Workflow' 클릭
3. 테스트 데이터 입력:
```json
{
  "videoId": "dQw4w9WgXcQ"
}
```
4. 실행 결과 확인

### Webhook 테스트
```bash
curl -X POST http://localhost:5678/webhook/youtube-to-blog \
  -H "Content-Type: application/json" \
  -d '{"videoId": "dQw4w9WgXcQ"}'
```

## 🔧 고급 설정

### SSL 설정 (Production)
```yaml
# docker-compose.yml에 추가
services:
  traefik:
    image: traefik:v2.9
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./traefik.yml:/traefik.yml
      - ./acme.json:/acme.json
      - /var/run/docker.sock:/var/run/docker.sock
```

### 백업 설정
```bash
# 데이터베이스 백업
docker exec blog-automation-db pg_dump -U n8n n8n > backup.sql

# N8N 데이터 백업
docker run --rm -v blog-automation-study_n8n_data:/data -v $(pwd):/backup alpine tar czf /backup/n8n-backup.tar.gz /data
```

### 모니터링 설정
1. Grafana 접속: http://localhost:3000
2. 기본 로그인: admin/admin
3. 대시보드 임포트: `monitoring/dashboards/n8n-dashboard.json`

## 🚨 문제 해결

### Docker 관련 문제
```bash
# 컨테이너 재시작
docker-compose restart

# 전체 리셋
docker-compose down -v
docker-compose up -d
```

### N8N 접속 불가
1. 포트 확인: `netstat -an | grep 5678`
2. 방화벽 설정 확인
3. Docker 데스크탑 재시작

### API 에러
- Rate Limiting: 재시도 로직 추가
- 인증 실패: API 키 재확인
- 네트워크 에러: Proxy 설정 확인

## 📚 추가 자료

- [N8N 공식 문서](https://docs.n8n.io)
- [Docker Compose 가이드](https://docs.docker.com/compose/)
- [API 통합 베스트 프랙티스](./api-integration-guide.md)

## 🆘 지원

문제가 해결되지 않으면:
1. [GitHub Issues](https://github.com/Jirehhyeon/BLOG-/issues) 생성
2. 에러 로그 첨부
3. 환경 정보 포함

---
*최종 업데이트: 2024.01*