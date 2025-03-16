모니터링이란??
모니터링은 시스템, 애플리케이션 및 인프라의 상태와 성능을 지속적으로 관찰하고 측정하는 과정입니다. 이는 문제를 조기에 감지하고, 성능을 최적화하며, 사용자 경험을 개선하기 위한 필수적인 활동입니다. 효과적인 모니터링을 통해 조직은 시스템 가용성을 유지하고 문제가 심각해지기 전에 선제적으로 대응할 수 있습니다.




## 1. 메트릭 (Metrics)

메트릭은 시스템의 상태와 성능을 수치화한 측정값으로, 시간에 따른 변화를 추적하고 분석하는 데 사용됩니다.

### 프론트엔드 메트릭

**주요 메트릭 종류**:

- **Core Web Vitals**
	-  LCP (Largest Contentful Paint)
		- 화면에 가장 큰 콘텐츠(이미지, 텍스트 블록 등)가 로드되는 시점을 측정
		- 사용자가 페이지의 주요 콘텐츠를 볼 수 있는 시간을 나타냄
		- 좋은 사용자 경험을 위해 2.5초 이내가 권장됨
		- 이미지 최적화, 서버 응답 시간 개선, 리소스 우선순위 지정으로 향상 가능
	- FID (First Input Delay) / INP (Interaction to Next Paint)
		- FID: 사용자가 처음 페이지와 상호작용할 때부터 브라우저가 반응할 때까지의 지연 시간
		- INP: FID를 대체할 예정인 새로운 지표로, 페이지 전체 생명주기 동안의 모든 상호작용 응답성을 측정
		- 좋은 사용자 경험을 위해 FID는 100ms 이내, INP는 200ms 이내가 권장됨
		- 긴 JavaScript 작업 분할, 불필요한 JavaScript 제거로 향상 가능
	- CLS (Cumulative Layout Shift)
		- 페이지 로드 과정에서 발생하는 예상치 못한 레이아웃 이동을 측정
		- 시각적 안정성을 나타내는 지표
		- 0.1 이하의 점수가 좋은 사용자 경험으로 간주됨
		- 이미지와 광고에 크기 속성 지정, 동적 콘텐츠를 위한 공간 확보로 향상 가능
- **로딩 성능**: TTFB, FCP, TTI
	- TTFB (Time To First Byte)
		-  브라우저가 웹 서버에 요청을 보낸 후 첫 번째 바이트를 받기까지의 시간
		- 서버 처리 시간과 네트워크 지연을 포함
		- 좋은 성능을 위해 0.8초 이내가 권장됨
		- 서버 최적화, CDN 사용, 캐싱 전략으로 개선 가능
	- FCP (First Contentful Paint)
		- 브라우저가 DOM에서 첫 번째 콘텐츠(텍스트, 이미지, 캔버스 등)를 렌더링하는 시점
		- 사용자가 로딩이 시작되었다고 인식하는 첫 피드백
		- 좋은 사용자 경험을 위해 1.8초 이내가 권장됨
		- 불필요한 리소스 제거, 중요 CSS 인라인화, 웹폰트 최적화로 향상 가능
	- TTI (Time To Interactive)
		- 페이지가 완전히 상호작용 가능한 상태가 되는 시점
		- 페이지가 시각적으로 렌더링되었고, 사용자 입력에 안정적으로 응답할 수 있는 때
		- 좋은 사용자 경험을 위해 3.8초 이내가 권장됨
		- JavaScript 코드 분할, 서드파티 스크립트 지연 로딩, 중요하지 않은 JavaScript 작업 연기로 향상 가능
- **리소스 사용**: JavaScript 힙 크기, 메모리 사용량
- **사용자 참여**: 페이지뷰, 이탈률, 전환율
- **에러율**: JavaScript 에러, API 호출 실패율

**수집 도구**:

1. **상용 도구**:
    - New Relic Browser
    - Datadog RUM
    - Dynatrace
    - LogRocket
    - Sentry Performance
2. **오픈소스/무료 도구**:
    - Google Lighthouse
    - Chrome User Experience Report (CrUX)
    - Web Vitals JavaScript 라이브러리
    - OpenTelemetry
    - Prometheus + Grafana (클라이언트 측 메트릭을 백엔드로 전송)
3. **내장 API**:
    - Performance API
    - Navigation Timing API
    - Resource Timing API

### 백엔드 메트릭

**주요 메트릭 종류**:

- **인프라 메트릭**: CPU, 메모리, 디스크 사용량
- **애플리케이션 메트릭**: 응답 시간, 처리량, 요청 큐 길이
- **데이터베이스 메트릭**: 쿼리 성능, 커넥션 풀 상태
- **네트워크 메트릭**: 대역폭 사용, 패킷 손실

**수집 도구**:

1. **상용 도구**:
    - Datadog
    - New Relic APM
    - Dynatrace
    - AppDynamics
    - Splunk Infrastructure Monitoring
2. **오픈소스 도구**:
    - Prometheus
    - Grafana
    - InfluxDB
    - Telegraf
    - Nagios
    - Zabbix
3. **클라우드 제공 도구**:
    - AWS CloudWatch
    - Google Cloud Monitoring
    - Azure Monitor

## 2. 로깅 (Logging)

로깅은 시스템이나 애플리케이션에서 발생하는 이벤트를 기록하는 과정으로, 구체적인 상황과 컨텍스트를 포함합니다.

### 프론트엔드 로깅

**주요 로그 종류**:

- **에러 로그**: JavaScript 예외, 스택 트레이스
- **사용자 행동 로그**: 사용자 상호작용, 페이지 탐색
- **네트워크 로그**: API 요청/응답 상세 정보
- **디버그 로그**: 개발 중 상태 변화 추적

**수집 도구**:

1. **상용 도구**:
    - Sentry
    - LogRocket
    - Datadog Log Management
    - Bugsnag
    - Rollbar
2. **오픈소스/자체 구현**:
    - 자체 로깅 서버로 전송 (API 엔드포인트)
    - LocalStorage 또는 IndexedDB에 저장 후 일괄 전송
    - OpenTelemetry (로그 수집)
### 백엔드 로깅

**주요 로그 종류**:

- **애플리케이션 로그**: 서비스 동작, 내부 상태 변화
- **액세스 로그**: API 호출, 요청 정보
- **에러 로그**: 예외, 스택 트레이스
- **감사 로그**: 중요 데이터 변경, 인증 이벤트

**수집 도구**:

1. **상용 도구**:
    - Splunk
    - Datadog Log Management
    - Logz.io
    - Sumo Logic
    - Papertrail
2. **오픈소스 도구**:
    - ELK Stack (Elasticsearch, Logstash, Kibana)
    - Graylog
    - Fluentd/Fluent Bit
    - Loki + Grafana
    - Vector
3. **로깅 라이브러리**:
    - Java: Log4j, Logback, SLF4J

## 3. 알람 (Alerts)

알람은 모니터링 시스템이 특정 조건이나 임계값을 초과할 때 담당자에게 통지하는 메커니즘입니다.

### 프론트엔드 알람

**주요 알람 대상**:

- 높은 JavaScript 에러율
- Core Web Vitals 저하
- API 호출 실패 증가
- 사용자 경험 지표 악화

**알람 도구**:

1. **상용 도구**:
    - Sentry Alerts
    - New Relic Alerts
    - Datadog Monitors
    - LogRocket Alerts
    - Google Analytics Alerts
2. **통합 서비스**:
    - PagerDuty
    - OpsGenie
    - VictorOps

### 백엔드 알람

**주요 알람 대상**:

- 높은 서버 자원 사용률
- 비정상적인 오류율
- 응답 시간 증가
- 서비스 가용성 문제
- 보안 관련 이벤트

**알람 도구**:

1. **상용 도구**:
    - Datadog Monitors
    - New Relic Alerts
    - Dynatrace Problem Detection
    - AppDynamics Policies
    - PagerDuty
2. **오픈소스 도구**:
    - Prometheus Alertmanager
    - Grafana Alerting
    - Nagios Alerts
    - Zabbix Triggers
    - ElastAlert (Elasticsearch)
3. **알림 채널 통합**:
    - Slack
    - Email
    - Microsoft Teams
    - Discord