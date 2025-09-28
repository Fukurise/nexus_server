# nexus_server


## 서비스 구성

### 1. Backbone Services (핵심 인프라)
- **Traefik**: 리버스 프록시, SSL 터미네이션, 로드 밸런싱
- **PostgreSQL**: 메인 데이터베이스

### 2. Workflow Services (워크플로우 자동화)
- **n8n**: 비즈니스 프로세스 자동화 플랫폼

### 3. CI/CD Services (지속적 통합/배포)
- **Jenkins**: CI/CD 파이프라인 관리

### 4. Monitoring (서비스 상태 확인)
- **prometheus**: 메트릭 수집
- **grafana**: 메트릭 시각화
- **postgres-exporter**: PostgreSQL 메트릭 수집
- **cadvisor**: 리소스 모니터링
- **node-exporter**: 노드 메트릭 수집(n8n)

## 사전 요구사항

- Docker Engine 20.10+
- Docker Compose 2.0+



## 환경 설정

### 1. 환경 변수 파일 생성

프로젝트 루트에 `.env` 파일을 생성하고 다음 내용을 설정하세요:

```bash
# 도메인 설정
BASE_DOMAIN=your-domain.com

# traefik 설정
LETSENCRYPT_EMAIL=your-email@example.com
TRAEFIK_METRICS_PORT=8081

# jenkins
JENKINS_PORT=8080
JENKINS_SLAVE_PORT=50000

# PostgreSQL 설정
POSTGRES_USER=nexus_user
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=nexus_db
POSTGRES_PORT=5432
POSTGRES_INITDB_ARGS=--encoding=UTF-8 --lc-collate=C --lc-ctype=C

# n8n 설정
N8N_ENCRYPTION_KEY=your_32_character_encryption_key_here
N8N_PORT=5678



# postgreSQL_Exporter 설정
POSTGRES_EXPORTER_PORT=9187

# node-exporter 설정
NODE_EXPORTER_PORT=9100

# cadvisor 설정
CADVISOR_PORT=8080

# grafana 설정
GRAFANA_PORT=3000
GRAFANA_USER=admin
GRAFANA_PASSWORD=admin
```

### 2. traefik 인증파일 생성
```bash
cd traefik

# Let's Encrypt SSL 인증서를 저장 파일 생성
touch acme.json

# 권한 부여
chmod 600 acme.json
```


### 2. Docker 네트워크 생성

```bash
# Traefik 네트워크 생성
docker network create traefik_network

# Nexus 네트워크 생성
docker network create nexus_network
```


## 시작 방법

### 전체 서비스 배포

```bash
# 모든 서비스 시작
docker compose up -d

# 또는 
docker compose --profile backbone --profile workflows --profile cicd up -d

# 또는 단계별 시작
docker compose --profile backbone up -d      # 핵심 인프라
docker compose --profile workflows up -d     # 워크플로우 서비스
docker compose --profile cicd up -d          # CI/CD 서비스
```

### 개별 서비스 시작

#### 1. 핵심 인프라만 시작
```bash
docker compose --profile backbone up -d
```

#### 2. 워크플로우 서비스 추가
```bash
docker compose --profile workflows up -d
```

#### 3. CI/CD 서비스 추가
```bash
docker compose --profile cicd up -d
```


## 서비스 접근

배포 완료 후 다음 URL로 각 서비스에 접근할 수 있습니다:

- **Traefik Dashboard**: `https://traefik.your-domain.com` (개발 환경에서만 활성화)
- **n8n**: `https://n8n.your-domain.com`
- **Jenkins**: `https://jenkins.your-domain.com`



## 추가 정보

- [Traefik 공식 문서](https://doc.traefik.io/traefik/)
- [n8n 공식 문서](https://docs.n8n.io/)
- [Jenkins 공식 문서](https://www.jenkins.io/doc/)
- [PostgreSQL 공식 문서](https://www.postgresql.org/docs/)
