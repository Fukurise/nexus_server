# Nexus Server Monitoring Stack

이 디렉토리는 Nexus Server 인프라의 모니터링을 위한 서비스입니다.

## 구성 요소

### 📊 Prometheus
- **역할**: 메트릭 수집 및 저장
- **설정**: `prometheus.yml`에서 스크래핑 대상 정의
- **데이터 보존**: 200시간


### 📈 Grafana
- **역할**: 메트릭 시각화 및 대시보드
- **포트**: ${GRAFANA_PORT}
- **도메인**: `gf.${BASE_DOMAIN}`
- **인증**: 환경변수로 관리되는 관리자 계정

### 🔍 Exporters

#### PostgreSQL Exporter
- **포트**: ${POSTGRES_EXPORTER_PORT}
- **역할**: PostgreSQL 데이터베이스 메트릭 수집

#### Node Exporter
- **포트**: ${NODE_EXPORTER_PORT}
- **역할**: 시스템 리소스 메트릭 수집 (CPU, 메모리, 디스크 등)

#### cAdvisor
- **포트**: ${CADVISOR_PORT}
- **역할**: 컨테이너 리소스 사용량 모니터링

## 모니터링 대상 서비스

다음 서비스들의 메트릭을 수집합니다:

- **n8n** - 워크플로우 자동화 도구
- **Jenkins** - CI/CD 파이프라인
- **PostgreSQL** - 데이터베이스 성능
- **Traefik** - 리버스 프록시
- **시스템 리소스** (Node Exporter)
- **컨테이너 리소스** (cAdvisor)

## 시작하기

### 1. 환경 변수 설정
프로젝트 루트의 `.env` 파일에 다음 변수들을 설정하세요:

```env
# Grafana 설정
GRAFANA_USER=admin
GRAFANA_PASSWORD=your_secure_password
GRAFANA_PORT=3000

# 도메인 설정
BASE_DOMAIN=your-domain.com

# PostgreSQL 설정
POSTGRES_USER=your_db_user
POSTGRES_PASSWORD=your_db_password
POSTGRES_DB=your_db_name
```

### 2. 네트워크 생성
```bash
docker network create nexus_network
```

### 3. 모니터링 스택 시작
```bash
cd monitoring
docker-compose up -d
```

## 접속 정보

- **Grafana**: https://gf.${BASE_DOMAIN} 또는 http://localhost:3030
- **PostgreSQL Exporter**: http://localhost:9187/metrics
- **Node Exporter**: http://localhost:9100/metrics
- **cAdvisor**: http://localhost:8082

## 데이터 저장

- **Prometheus 데이터**: `./prometheus/data/`
- **Grafana 데이터**: `./grafana/data/`

## 보안 고려사항

- Grafana는 HTTPS를 통해 접근 가능
- 관리자 계정은 환경변수로 설정
- 사용자 등록이 비활성화됨
- 쿠키 보안 설정 활성화

## 트러블슈팅

### 포트 충돌
모니터링 스택의 포트들이 다른 서비스와 충돌하는 경우, `docker-compose.yml`에서 포트 매핑을 수정하세요.

### 메트릭 수집 실패
- 대상 서비스가 실행 중인지 확인
- 네트워크 연결 상태 확인
- 방화벽 설정 확인

### Grafana 접속 불가
- Traefik 설정 확인
- SSL 인증서 상태 확인
- 도메인 DNS 설정 확인

## 확장 가능성

추가 모니터링이 필요한 서비스가 있다면:

1. `prometheus.yml`에 새로운 스크래핑 작업 추가
2. 필요시 새로운 exporter 컨테이너 추가
3. Grafana에서 새로운 대시보드 생성
