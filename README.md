# Developer Portfolio

## FinSight — 주택담보대출 상담·신청·심사 서비스 3개

직접 만들었던 금융 서비스 구성(FinSight)을 **처음부터 다시 구현하며 문제를 찾아 고친 기록**입니다.
기능을 단계별로 다시 만들고, 테스트와 실제 실행으로 확인한 문제를 번호로 남겨 하나씩 고쳤습니다.

| 저장소 | 역할 |
| --- | --- |
| [homefin-mate](https://github.com/poi9438-blip/homefin-mate) | 사용자 서비스: 대출 계산·DTI, 정책대출 판정·추천, 통합 분석, 대출 신청 (React + Spring Boot + FastAPI) |
| [finflow-engine](https://github.com/poi9438-blip/finflow-engine) | 신청 처리: 접수·상태 흐름·감사 기록, 멱등성, 만료·집계 배치 |
| [riskwatch-system](https://github.com/poi9438-blip/riskwatch-system) | 심사 규칙 엔진: 판정과 규칙별 근거, DB 기준표와 기준 버전 이력 |

```
사용자 ─▶ HomeFin ──서비스 토큰──▶ RiskWatch   상담 단계 판정
            │                          ▲
            └──▶ FinFlow ── 심사 시작 ─┘   신청 접수·심사
```

### 이렇게 만들었습니다
- **문제를 기록하고 고침:** 다시 구현하면서 찾은 문제를 known-issues에 번호로 남기고, 원인·확인 방법·수정 PR을 함께 적었습니다 (HomeFin 99건).
- **테스트를 먼저 씀:** 기능마다 테스트 명세(TC)를 먼저 쓰고, 명세와 실행 결과를 스크립트로 대조해 CI에서 확인합니다. 코드를 일부러 망가뜨려 테스트가 잡는지도 확인했습니다(변이 검사).
- **실패를 전제로 연결:** 서비스 간 호출에 타임아웃·재시도·서킷 브레이커·fallback을 두고, 신청은 `Idempotency-Key`로 재시도해도 한 번만 접수됩니다.
- **측정으로 개선:** k6로 장애와 데이터 누적을 재현해 고쳤습니다. 외부 시스템이 멈춘 동안 다른 기능의 실패율 64% → 0%, 감사 기록 100만 건 조회 실패 → 0.2초 등이 그 결과입니다.
- **보이게 운영:** OpenTelemetry로 세 서비스의 요청을 한 추적으로 잇고, Grafana 대시보드와 업무 알림(RiskWatch 평가 불가, 신청 실패 등)을 둡니다. 추적으로 첫 요청 지연의 원인을 찾아 1.77초 → 0.37초로 줄였습니다.

### 기술
Java 17 · Spring Boot 3.2 · Spring Security · JPA · Flyway · MySQL 8 · React · FastAPI · JUnit 5 · Testcontainers · k6 · Docker Compose · GitHub Actions · OpenTelemetry · Grafana

### 실행
세 저장소를 같은 폴더에 나란히 받은 뒤, `homefin-mate`에서 Docker Compose로 세 서비스와 관측 도구를 함께 띄울 수 있습니다 ([실행 방법](https://github.com/poi9438-blip/homefin-mate#5-세-서비스-함께-실행-선택)).
