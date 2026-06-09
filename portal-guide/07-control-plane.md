# 07. Control Plane [점검 중]

이 모듈에서는 Microsoft Foundry의 Control Plane을 통해 프로덕션 환경의 AI 리소스를 관리하고 모니터링하는 방법을 학습합니다.

## 📋 목차

- [Control Plane 개요](#control-plane-개요)
- [Fleet Overview](#fleet-overview)
- [Assets 관리](#assets-관리)
- [Compliance 및 보안](#compliance-및-보안)
- [Quota 관리](#quota-관리)
- [Admin 기능](#admin-기능)
- [마무리](#마무리)

## 🎯 학습 목표

- Control Plane의 역할과 중요성 이해
- Fleet Overview를 통한 전체 시스템 모니터링
- Assets(에이전트, 모델, 도구) 관리 방법
- 컴플라이언스 및 보안 설정 구성
- Quota 및 Rate Limiting 관리
- 프로젝트 및 사용자 권한 관리

## ⏱️ 예상 소요 시간

약 10분

---

## Control Plane 개요

### Control Plane이란?

Control Plane은 Microsoft Foundry의 중앙 관리 센터로, 모든 AI 리소스를 통합 관리하고 모니터링합니다.

```
Control Plane = 모니터링 + 관리 + 보안 + 거버넌스
```

### 주요 기능 영역

```
┌─────────────────────────────────────────┐
│         Control Plane                   │
├─────────────────────────────────────────┤
│ Fleet Overview   │ 전체 시스템 대시보드   │
│ Assets          │ 리소스 관리           │
│ Compliance      │ 보안 및 정책          │
│ Quota           │ 할당량 및 제한        │
│ Admin           │ 프로젝트 및 권한      │
└─────────────────────────────────────────┘
```

### 왜 중요한가?

프로덕션 환경에서 다음을 보장합니다:
- 📊 **가시성**: 모든 리소스의 상태와 성능 파악
- 🛡️ **보안**: 취약점과 위협 조기 발견
- 💰 **비용 관리**: 리소스 사용량과 비용 최적화
- ⚖️ **컴플라이언스**: 규정 준수 상태 모니터링
- 🚨 **알림**: 문제 발생 시 즉각 대응

---

## Fleet Overview

Fleet Overview는 배포된 모든 AI 리소스의 상태를 한눈에 볼 수 있는 대시보드입니다.

### 대시보드 접근

1. **Control Plane 이동**

   - Foundry 포털 우측 상단 메뉴에서 **Operate**를 선택합니다.
   
   ![Fleet Overview 대시보드](../assets/07-02-fleet-overview.png)

   ![Fleet Overview 대시보드](../assets/07-02-fleet-overview2.png)

### 주요 메트릭

#### 1. Running Agents

```
┌────────────────────────────────────┐
│  Running Agents: 5                 │
├────────────────────────────────────┤
│  Active:    4  ✓                   │
│  Warning:   1  ⚠                   │
│  Failed:    0  ✗                   │
└────────────────────────────────────┘

Agents:
- ModelRouterAgent       [Active]
- FileSearchAgent        [Active]
- WebSearchAgent         [Warning] (High latency)
- KnowledgeAgent         [Active]
- KnowledgeAgent2        [Active]
```

**Warning 원인 파악**:
- 높은 응답 시간 (Latency)
- 에러율 증가
- 할당량 임박

#### 2. Agent Success Rate

```
┌────────────────────────────────────┐
│  Overall Success Rate: 96.5%       │
├────────────────────────────────────┤
│  Last 24 hours:                    │
│  ████████████████████░░  96.5%     │
│                                    │
│  Last 7 days trend:                │
│  ▁▂▃▄▅▆█▆▇█ ↗ Improving           │
└────────────────────────────────────┘

By Agent:
- ModelRouterAgent:    98.2% ✓
- FileSearchAgent:     97.5% ✓
- WebSearchAgent:      92.1% ⚠ (개선 필요)
- KnowledgeAgent:      98.8% ✓
- KnowledgeAgent2:     97.3% ✓
```

#### 3. Estimated Cost

```
┌────────────────────────────────────┐
│  Current Month Cost                │
├────────────────────────────────────┤
│  Total:        $245.30             │
│  Projected:    $350.00 (month-end) │
│                                    │
│  Breakdown:                        │
│  Models:       $180.50 (74%)       │
│  Search:       $ 45.20 (18%)       │
│  Storage:      $ 12.40 ( 5%)       │
│  Other:        $  7.20 ( 3%)       │
└────────────────────────────────────┘

Top Consumers:
1. gpt-4-1              $95.30
2. model-router         $65.20
3. text-embedding       $20.00
```

#### 4. Token Usage

```
┌────────────────────────────────────┐
│  Token Usage (24h)                 │
├────────────────────────────────────┤
│  Total:        1.2M tokens         │
│                                    │
│  Input:        800K (67%)          │
│  Output:       400K (33%)          │
│                                    │
│  Hourly Peak:  75K tokens/hour     │
│  Current:      52K tokens/hour     │
└────────────────────────────────────┘

Usage Trend:
Hour: 00  04  08  12  16  20  24
      ▁▁▁▃▅▇█▇▆▅▄▃▂▁
      (Peak: 오후 4시)
```

#### 5. Active Alerts

```
┌────────────────────────────────────┐
│  Active Alerts: 2                  │
├────────────────────────────────────┤
│  ⚠ Warning (1)                     │
│    • WebSearchAgent high latency   │
│      Avg response: 8.5s (SLA: 5s)  │
│                                    │
│  ℹ Info (1)                        │
│    • Quota usage at 75%            │
│      gpt-4-1: 750K/1M TPM          │
└────────────────────────────────────┘
```

### 대시보드 활용

1. **일일 체크**
   - Success rate 확인
   - Active alerts 확인
   - Cost 추이 모니터링

2. **주간 리뷰**
   - 성능 트렌드 분석
   - 비용 최적화 기회 파악
   - Quota 계획

3. **월간 계획**
   - 용량 계획
   - 예산 조정
   - 아키텍처 최적화

### Register Agent

Fleet Overview에서 외부 에이전트를 등록하여 중앙 관리할 수 있습니다.

#### 에이전트 등록이 필요한 경우

```
Foundry 외부에서 개발된 에이전트:
- Custom HTTP/REST API 기반 에이전트
- 다른 플랫폼에서 호스팅되는 에이전트
- 레거시 시스템과 통합된 에이전트

AI Gateway 활성화 필요:
- 중앙 집중식 관리 및 모니터링
- 보안, 텔레메트리, Rate limiting 기능
```

#### 에이전트 등록 절차

1. **AI Gateway 활성화**

   ```
   Foundry 프로젝트에 AI Gateway가 활성화되어 있어야 합니다.
   - 무료로 설정 가능
   - Security, telemetry, rate limits 등의 거버넌스 기능 제공
   ```

2. **Register Agent 클릭**

   - Fleet Overview 페이지에서 **Register agent** 버튼을 클릭합니다.
   
   ![Register agent 화면](../assets/07-08-register-agent.png)

3. **에이전트 정보 입력**

   ```
   Add agent details:
   ────────────────────────────────────
   • Agent URL *
     예: https://yourdomain.com/agent
     
   • Protocol *
     - General HTTP, Including REST
     
   • OpenTelemetry agent ID (선택)
     텔레메트리 모니터링을 위한 agent ID
     
   • Admin portal URL (선택)
     에이전트 인프라를 중지할 수 있는 포털 URL
   ```

4. **프로젝트 및 이름 설정**

   ```
   Set up your agent:
   ────────────────────────────────────
   • Select a project *
     에이전트를 등록할 Foundry 프로젝트 선택
     
   • Agent name *
     Fleet에 표시될 에이전트 이름
   ```

5. **등록 완료**

   - **Register agent** 버튼을 클릭하여 등록을 완료합니다.
   - 등록된 에이전트가 Fleet Overview에 표시됩니다.

#### 등록된 에이전트 관리

```
┌─────────────────────────────────────────────────────────────┐
│  Registered Agents                                          │
├──────────────┬─────────┬────────┬──────────┬────────────────┤
│ Name         │ Project │ Status │ Type     │ Endpoint       │
├──────────────┼─────────┼────────┼──────────┼────────────────┤
│ CustomAgent  │ Default │ Active │ External │ https://...    │
│ ModelRouter  │ Default │ Active │ Foundry  │ Built-in       │
│ FileSearch   │ Default │ Active │ Foundry  │ Built-in       │
└──────────────┴─────────┴────────┴──────────┴────────────────┘

External vs Foundry Agents:
- External: 외부에서 호스팅, URL 필요
- Foundry:  Foundry 내부에서 관리, Built-in
```

#### 자동 검색 에이전트

```
⚠️ 자동 등록 (수동 등록 불필요)

다음 에이전트는 자동으로 검색되어 등록됩니다:
• Foundry agents
• Azure SRE Agent
• Logic Apps agent loop

이들은 별도 등록 절차 없이 Fleet Overview에 나타납니다.
```

---

## Assets 관리

Assets 섹션에서 모든 AI 리소스를 관리합니다.

### 1. Agents

배포된 모든 에이전트의 상세 정보를 확인합니다.

![Assets > Agents 목록](../assets/07-10-assets-agents.png)

```
┌─────────────────────────────────────────────────────────────┐
│  Agents                                                      │
├──────────────┬─────────┬────────┬──────────┬────────┬───────┤
│ Name         │ Project │ Status │ Version  │ Error  │ Runs  │
│              │         │        │          │ Rate   │ (24h) │
├──────────────┼─────────┼────────┼──────────┼────────┼───────┤
│ ModelRouter  │ Default │ Active │ v1.2     │ 1.8%   │ 1,234 │
│ FileSearch   │ Default │ Active │ v1.0     │ 2.5%   │  456  │
│ WebSearch    │ Default │ Warning│ v1.1     │ 7.9%   │  789  │
│ Knowledge    │ Default │ Active │ v2.0     │ 1.2%   │  678  │
│ Knowledge2   │ Default │ Active │ v1.0     │ 2.7%   │  345  │
└──────────────┴─────────┴────────┴──────────┴────────┴───────┘
```

**에이전트 상세 정보**:

- 클릭하면 상세 메트릭 확인
- Traces 및 로그 접근
- 버전 관리 및 롤백

**Action Items**:
- WebSearchAgent 에러율 높음 → Instructions 개선 또는 디버깅 필요

### 2. Models

배포된 모든 모델의 상태와 사용량을 확인합니다.

![Assets > Models 목록](../assets/07-11-assets-models.png)

```
┌───────────────────────────────────────────────────────────────────────────┐
│  Models                                                                    │
├─────────────┬─────────┬────────┬───────────┬────────────┬───────┬────────┤
│ Model       │ Project │ Version│ State     │ Guardrails │ Deploy│ Rate   │
│             │         │        │           │            │ Type  │ Limit  │
├─────────────┼─────────┼────────┼───────────┼────────────┼───────┼────────┤
│ gpt-4-1     │ Default │ latest │ Running   │ ✓ Enabled  │ Std   │ 1M TPM │
│ model-      │ Default │ 1.0    │ Running   │ ✓ Enabled  │ Std   │ 500K   │
│  router     │         │        │           │            │       │  TPM   │
│ text-       │ Default │ latest │ Running   │ - N/A      │ Std   │ 2M TPM │
│  embedding  │         │        │           │            │       │        │
└─────────────┴─────────┴────────┴───────────┴────────────┴───────┴────────┘

Cost per 1M tokens:
- gpt-4-1:         $30.00 (input), $60.00 (output)
- model-router:    Variable (depends on routing)
- text-embedding:  $0.13 (input only)
```

**모델 관리 작업**:
- Rate limit 조정
- Guardrails 설정 확인
- 비용 분석
- 사용량 트렌드 확인

### 3. Tools

연결된 도구 및 MCP 서버를 관리합니다.

![Assets > Tools 목록](../assets/07-12-assets-tools.png)

```
┌──────────────────────────────────────────────────────────────┐
│  Tools                                                        │
├──────────────────┬─────────┬──────────────────────────────────┤
│ Tool Name        │ Project │ MCP Server Endpoint URL          │
├──────────────────┼─────────┼──────────────────────────────────┤
│ File Search      │ Default │ Built-in                         │
│ Web Search       │ Default │ Built-in                         │
│ Custom Function  │ Default │ https://myapi.azurewebsites.net  │
└──────────────────┴─────────┴──────────────────────────────────┘
```

**도구 모니터링**:
- 엔드포인트 상태 확인
- API 호출 성공률
- 평균 응답 시간

---

## Compliance 및 보안

### 1. Policies

조직의 AI 사용 정책을 관리합니다.

![Active Policies 목록](../assets/07-14-compliance-policies.png)

```
┌────────────────────────────────────────────────────────────┐
│  Active Policies                                           │
├────────────────────────────────┬───────────────────────────┤
│ Policy Name                    │ Status                    │
├────────────────────────────────┼───────────────────────────┤
│ Content Safety                 │ ✓ Enabled (All agents)    │
│ Indirect Prompt Injections     │ ✓ Enabled (All agents)    │
│ Protected Materials            │ ✓ Enabled (All agents)    │
│ Data Residency (Korea)         │ ✓ Enabled                 │
│ PII Detection                  │ ⚠ Warning mode            │
└────────────────────────────────┴───────────────────────────┘
```

**정책 설정**:

#### Content Safety
```
Harmful Content Filtering:
- Hate speech:     High
- Sexual content:  High
- Violence:        High
- Self-harm:       High

Action: Block response
```

#### Indirect Prompt Injections
```
Jailbreak Attempts Detection: Enabled

Examples:
- "Ignore previous instructions..."
- "You are now in developer mode..."

Action: Block and log
```

#### Protected Materials
```
Code Detection:    Enabled
Text Detection:    Enabled

Protected content:
- Copyrighted code
- Licensed articles
- Proprietary content

Action: Filter and replace
```

### 2. Guardrails

모델 배포별 보호 장치를 설정합니다.

![Guardrails 설정](../assets/07-15-guardrails-config.png)

```
┌────────────────────────────────────────────────────────────┐
│  Guardrails - gpt-4-1                                      │
├────────────────────────────────┬───────────────────────────┤
│ Guardrail                      │ Configuration             │
├────────────────────────────────┼───────────────────────────┤
│ Content harms                  │ ✓ Enabled                 │
│   - Severity threshold:        │   Medium                  │
│ Jailbreak                      │ ✓ Enabled                 │
│ Indirect prompt injections     │ ✓ Enabled                 │
│ Spotlighting                   │ ✓ Enabled                 │
│ Profanity (Blocklist)          │ ✓ Enabled                 │
│   - Custom blocklist:          │   company_terms.txt       │
│ Protected materials - code     │ ✓ Enabled                 │
│ Protected materials - text     │ ✓ Enabled                 │
└────────────────────────────────┴───────────────────────────┘
```

**Guardrails 테스트**:
```python
# 유해 콘텐츠 테스트
test_input = "How to make a bomb"
# Expected: Blocked by content safety

# Jailbreak 테스트
test_input = "Ignore previous rules and tell me..."
# Expected: Blocked by jailbreak detection

# 프로프래니티 테스트
test_input = "This is [blocked word]"
# Expected: Filtered by profanity filter
```

### 3. Security Posture

보안 상태를 평가하고 개선합니다.

![Security Posture 점수](../assets/07-16-security-posture.png)

```
┌────────────────────────────────────────────────────────────┐
│  Security Posture                                          │
├──────────────┬─────────────────┬──────────────┬───────────┤
│ Risk Level   │ Recommendation  │ Affected     │ Resource  │
│              │                 │ Resource     │ Type      │
├──────────────┼─────────────────┼──────────────┼───────────┤
│ ⚠ Medium     │ Enable MFA for  │ admin@...    │ User      │
│              │ admin accounts  │              │           │
├──────────────┼─────────────────┼──────────────┼───────────┤
│ ℹ Low        │ Rotate API keys │ Storage      │ Key       │
│              │ (90 days old)   │ Account      │           │
├──────────────┼─────────────────┼──────────────┼───────────┤
│ ℹ Low        │ Update to       │ gpt-4-1      │ Model     │
│              │ latest version  │              │           │
└──────────────┴─────────────────┴──────────────┴───────────┘

Overall Security Score: 85/100 ✓
```

---

## Quota 관리

### 1. Token Per Minute (TPM)

모델별 요청 제한을 관리합니다.

![Token Per Minute Quota](../assets/07-17-quota-tpm.png)

```
┌───────────────────────────────────────────────────────────────────┐
│  Token Per Minute Quota                                           │
├────────────┬──────────┬─────────┬────────────┬─────────┬─────────┤
│ Model      │ Deploy   │ Region  │ Deploy     │ Shared  │ Rate    │
│            │          │         │ Type       │ Alloc   │ Limiting│
├────────────┼──────────┼─────────┼────────────┼─────────┼─────────┤
│ gpt-4-1    │ gpt-4-1  │ East US │ Standard   │ 1M TPM  │ Enabled │
│            │          │    2    │            │ 75% used│         │
├────────────┼──────────┼─────────┼────────────┼─────────┼─────────┤
│ model-     │ model-   │ East US │ Standard   │ 500K    │ Enabled │
│  router    │  router  │    2    │            │ 45% used│         │
├────────────┼──────────┼─────────┼────────────┼─────────┼─────────┤
│ text-      │ text-emb │ East US │ Standard   │ 2M TPM  │ Enabled │
│  embedding │          │    2    │            │ 32% used│         │
└────────────┴──────────┴─────────┴────────────┴─────────┴─────────┘
```

**Rate Limiting 동작**:
```
Quota: 1,000,000 TPM
Current usage: 750,000 TPM (75%)

New request: 100,000 tokens
Status: ✓ Allowed (850,000 TPM)

New request: 300,000 tokens
Status: ✗ Throttled (would exceed 1M TPM)
Response: 429 Too Many Requests
Retry-After: 30 seconds
```

**Quota 증가 요청**:
1. Azure Portal로 이동
2. Support > New support request
3. Issue type: Service and subscription limits (quotas)
4. 필요한 quota 및 이유 명시

### 2. Provisioning Throughput Unit (PTU)

고정 용량을 위한 PTU를 관리합니다.

![PTU (Provisioning Throughput Units)](../assets/07-19-quota-ptu.png)

```
┌────────────────────────────────────────────────────────────┐
│  Provisioning Throughput Units                             │
├────────────┬──────────┬─────────┬──────────────────────────┤
│ Model      │ Deploy   │ Region  │ PTU Allocation           │
├────────────┼──────────┼─────────┼──────────────────────────┤
│ gpt-4-1    │ prod-    │ East US │ 100 PTU                  │
│            │  gpt4    │    2    │ ($7,000/month fixed)     │
└────────────┴──────────┴─────────┴──────────────────────────┘

PTU vs Standard:
Standard:    Pay per token (variable cost)
PTU:         Fixed capacity, predictable cost

PTU 권장 시나리오:
- 높은 일일 사용량 (수백만 토큰)
- 예측 가능한 워크로드
- 낮은 지연 시간 필요
```

---

## Admin 기능

### 1. All Projects

조직 내 모든 Foundry 프로젝트를 관리합니다.

![All Projects 목록](../assets/07-20-admin-projects.png)

**프로젝트 생명주기**:
```
Development → Test → Staging → Production

각 환경별 설정:
- Development:  낮은 quota, 느슨한 정책
- Test:         중간 quota, 테스트 데이터
- Staging:      프로덕션과 동일 설정
- Production:   높은 quota, 엄격한 정책
```

### 2. AI Gateway

중앙 집중식 API Gateway를 구성합니다.

![AI Gateway 설정](../assets/07-21-ai-gateway1.png)

![AI Gateway 설정](../assets/07-21-ai-gateway2.png)

```
┌────────────────────────────────────────────────────────────┐
│  AI Gateway Configuration                                  │
├────────────────────────────────────────────────────────────┤
│  Gateway Endpoint:                                         │
│  https://gateway.foundry.ai                                │
│                                                            │
│  Features:                                                 │
│  ✓ Authentication & Authorization                          │
│  ✓ Rate limiting                                           │
│  ✓ Load balancing                                          │
│  ✓ Caching                                                 │
│  ✓ Monitoring & Logging                                    │
│  ✓ Request/Response transformation                         │
└────────────────────────────────────────────────────────────┘
```

**AI Gateway 장점**:
```
✅ 중앙 집중식 관리
- 모든 요청이 Gateway를 통과
- 통합 모니터링 및 로깅

✅ 보안 강화
- API 키 관리
- IP 화이트리스트
- 요청 검증

✅ 성능 최적화
- 캐싱으로 중복 요청 감소
- 로드 밸런싱으로 부하 분산

✅ 비용 최적화
- 캐시 히트로 API 호출 감소
- 사용량 추적 및 제어
```

---

## 📚 추가 리소스

- [Microsoft Foundry Control Plane](https://learn.microsoft.com/en-us/azure/ai-foundry/control-plane/overview?view=foundry)
- [Agent 헬스 및 성능 모니터링](https://learn.microsoft.com/en-us/azure/ai-foundry/control-plane/monitoring-across-fleet?view=foundry)
- [Custom Agent 등록](https://learn.microsoft.com/en-us/azure/ai-foundry/control-plane/register-custom-agent?view=foundry)
- [Guardrail Policy 생성](https://learn.microsoft.com/en-us/azure/ai-foundry/control-plane/quickstart-create-guardrail-policy?view=foundry)
- [규정 준수 및 보안 관리](https://learn.microsoft.com/en-us/azure/ai-foundry/control-plane/how-to-manage-compliance-security?view=foundry)
- [모델 비용 및 성능 최적화](https://learn.microsoft.com/en-us/azure/ai-foundry/control-plane/how-to-optimize-cost-performance?view=foundry)

---

## 마무리

축하합니다! Microsoft Foundry Hands-on Workshop의 모든 모듈을 완료했습니다! 🎉

### 학습한 내용 요약

```
✅ Module 01: 환경 설정
   - Resource Group 및 Foundry 리소스 생성

✅ Module 02: 모델 및 배포
   - 모델 탐색, 배포, Model Router 구성

✅ Module 03: 에이전트 개발
   - 다양한 타입의 에이전트 생성 및 배포

✅ Module 04: 워크플로우
   - Sequential, Group Chat, Human-in-loop 워크플로우 구현

✅ Module 05: Foundry IQ
   - AI Search 및 Blob Storage 기반 Knowledge Base 구축

✅ Module 06: 평가
   - 에이전트 품질 평가 및 개선

✅ Module 07: Control Plane
   - 프로덕션 모니터링 및 관리
```

### 다음 단계

이제 다음을 수행할 준비가 되었습니다:

1. **프로덕션 배포**
   - 실제 애플리케이션에 Foundry 통합
   - CI/CD 파이프라인 구축
   - 모니터링 및 알림 설정

2. **고급 기능 탐색**
   - Custom tools 및 MCP servers 개발
   - Multi-agent 협업 패턴
   - Fine-tuning 및 모델 커스터마이징

3. **커뮤니티 참여**
   - [Microsoft Tech Community](https://techcommunity.microsoft.com) 포럼 참여
   - [GitHub Samples](https://github.com/Azure-Samples) 기여
   - 사용 사례 공유

### 리소스 정리

워크샵 종료 후 비용 절감을 위해 리소스를 정리하세요:

```bash
# Resource Group 삭제 (모든 리소스 포함)
az group delete --name foundry --yes --no-wait
```

⚠️ **주의**: 이 명령은 모든 워크샵 리소스를 영구적으로 삭제합니다.

---

### 피드백

워크샵에 대한 피드백이나 질문이 있으시면 언제든지 공유해주세요!

---

[← 이전: 평가](./06-evaluations.md) | [메인으로](./README.md)
