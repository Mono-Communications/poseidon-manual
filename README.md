# Poseidon 통합 매뉴얼

Poseidon 통합 매뉴얼 v1 — © ㈜모노커뮤니케이션즈

| 항목 | 내용 |
| --- | --- |
| **버전** | v1 |
| **작성일** | 2026.07.30 |
| **최종수정일** | 2026.07.31 |
| **고객센터** | 02-333-7223 |

## <mark style="color:blue;">들어가기 전에</mark>

***

{% hint style="danger" %}
**이 문서는 고객지원팀 내부용입니다.** 고객사에 이 매뉴얼이나 관제 콘솔 주소·계정을 공유하지 마십시오. 고객사에 전달하는 것은 **Odyssey 매뉴얼의 Poseidon 모니터링 연동 절차**와 발급된 설정값(주소·인증 키)입니다.
{% endhint %}

이 프로그램은 본질적으로 외부의 위험한 상황에서 사용하도록 개발된 것이 아닙니다. 따라서 그런 목적으로 사용된 경우, 사용자는 응용 프로그램의 안전한 사용을 보장하기 위한 모든 적절한 안전 조치, 백업, 대비 및 기타 조치를 반드시 취해야 합니다. 프로그램이 이러한 목적으로 사용되었을 경우 ㈜모노커뮤니케이션즈는 이러한 프로그램 사용으로 인한 피해를 책임지지 않습니다.

이 프로그램(소프트웨어와 설명서 포함)은 저작권법, 특허 및 기타 지적재산권 관련 법규에 의해 보호됩니다. 이 프로그램을 리버스 엔지니어링하거나 분해하거나 또는 역 컴파일하는 것은 금지되어 있습니다.

사용자는 시스템 해킹을 방지하기 위하여 백신 프로그램을 이용한 주기적인 PC 관리를 하여야 하며, 아이디/비밀번호를 외부로 노출할 수 없고, 지정된 장소에서만 프로그램을 사용하여야 합니다.

이 문서의 내용은 사전 공지 없이 변경될 수 있습니다.

## <mark style="color:blue;">Poseidon이란</mark>

***

**Poseidon**은 고객사에 설치된 발송·수신 엔진이 **살아 있는지, 무슨 오류가 났는지**를 원격에서 감시하는 모니터링 플랫폼입니다. 고객지원팀이 사용하는 화면은 그중 **Poseidon Ops**(관제 콘솔)이며, 고객사별 상태 확인·인스턴스 등록·API 키 발급을 여기서 처리합니다.

```
 [고객사 내부망]                        │      [모노 클라우드]
                                       │
  Odyssey / Twoway / CS / M2X          │    Poseidon 서버 ──► Poseidon Ops
   (+ Cypher 오류 수집)                 │                      (이 매뉴얼의 화면)
            │                          │
            └─ 30초마다 상태·지표 ─────►│
               오류 발생 시 오류 보고     │
               (Outbound 단방향)         │
```

### <mark style="color:cyan;">감시 대상 서비스 4종</mark>

화면 곳곳에서 이 4개가 색이 다른 칩으로 표시됩니다.

| 서비스 | 정체 | 화면에서 함께 보이는 것 |
| --- | --- | --- |
| **ODYSSEY** | 문자·RCS·카카오 **발송** 엔진 | 채널별 전송 규격(KT / CPAAS / MN), 세션 목록 |
| **TWOWAY** | **수신**(양방향) 엔진 | SMS·LMS·MMS·RCS 수신 여부, 수신 URL, tw 계정 |
| **CS** | 데스크톱 메시징 클라이언트 | 머신 UUID·OS·앱 버전, 발송 계정 식별자 |
| **M2X** | 레거시 제품 | 자체 신호가 없어 **Cypher 사이드카**로 상태를 추정 |

### <mark style="color:cyan;">Poseidon이 보는 것 / 보지 않는 것</mark>

| 구분 | 내용 | 한계 |
| --- | --- | --- |
| **생존 신호** | 엔진이 살아있는지 | 30초 주기. 180초(3분) 미수신 시 DOWN |
| **오류** | ERROR / FATAL 예외 | Cypher가 로그를 가로채 보고. WARN 이하는 수집 안 함 |
| **연동 구성** | 채널·전송 규격·세션·수신 설정 | 마지막으로 보고된 값 |

{% hint style="danger" %}
**Poseidon은 "발송이 잘 되는지"를 보지 않습니다.** 엔진의 생존과 예외만 봅니다. 화면이 전부 정상(UP·오류 없음)인데 실제 발송이 안 되는 상황은 실제로 가능합니다 ([16. 플레이북 G](part-4/16.-playbook.md)).
{% endhint %}

### <mark style="color:cyan;">통신 방향 — 고객에게 가장 많이 받는 질문</mark>

{% hint style="success" %}
통신은 **고객사 → Poseidon 단방향(Outbound)** 입니다. Poseidon이 고객사 서버로 접속하는 경로는 **없습니다.** 인바운드 포트 개방도, VPN도 필요 없습니다.
{% endhint %}

## <mark style="color:blue;">목차</mark>

***

Poseidon을 처음 본다면 **Part 1. 시작하기** 부터 따라가세요. 접속·계정부터 화면 구조까지 단계별로 안내합니다.

운영 중에는 **Part 2. 화면별 사용법 / Part 3. 동작 원리 이해하기 / Part 4. 고객 응대**를 reference로 활용합니다.

### <mark style="color:cyan;">Part 1. 시작하기</mark>

* [1. 접속과 계정](part-1/1.-access.md)
* [2. 화면 구조 한눈에 보기](part-1/2.-layout.md)

### <mark style="color:cyan;">Part 2. 화면별 사용법</mark>

* [3. 대시보드](part-2/3.-dashboard.md)
* [4. 인스턴스](part-2/4.-instances.md)
* [5. 이벤트](part-2/5.-events.md)
* [6. 고객사](part-2/6.-tenants.md)
* [7. 인스턴스 관리 (ADMIN)](part-2/7.-admin-instances.md)
* [8. 사용자 (ADMIN)](part-2/8.-admin-users.md)
* [9. 설정](part-2/9.-settings.md)

### <mark style="color:cyan;">Part 3. 동작 원리 이해하기</mark>

* [10. 시스템 구성과 데이터 흐름](part-3/10.-architecture.md)
* [11. 상태 판정 기준](part-3/11.-status.md)
* [12. 오류 수집 구조와 Cypher](part-3/12.-errors.md)

### <mark style="color:cyan;">Part 4. 고객 응대</mark>

* [13. 고객 온보딩 절차](part-4/13.-onboarding.md)
* [14. 고객 설명 스크립트](part-4/14.-script.md)
* [15. 아웃바운드 방화벽 안내](part-4/15.-firewall.md)
* [16. 증상별 대응 플레이북](part-4/16.-playbook.md)
* [17. 에스컬레이션과 알려진 제약](part-4/17.-escalation.md)

## <mark style="color:blue;">빠른 찾기</mark>

***

| 하려는 일 | 보는 곳 |
| --- | --- |
| 전체 인스턴스 상태 한눈에 확인 | [3. 대시보드](part-2/3.-dashboard.md) |
| 특정 고객사 엔진이 살아있는지 확인 | [4. 인스턴스](part-2/4.-instances.md) |
| 오류·기동·종료 이력 추적 | [4. 인스턴스](part-2/4.-instances.md) 상세 / [5. 이벤트](part-2/5.-events.md) |
| 고객사 등록·현황 확인 | [6. 고객사](part-2/6.-tenants.md) |
| **API 키 발급·승인·해지** | [7. 인스턴스 관리](part-2/7.-admin-instances.md) |
| 운영자 계정 승인·잠금 해제 | [8. 사용자](part-2/8.-admin-users.md) |
| 고객 문의 대응 절차 | [16. 플레이북](part-4/16.-playbook.md) |
| 방화벽 안내 | [15. 방화벽](part-4/15.-firewall.md) |

{% hint style="info" %}
**용어 주의.** 화면 표기는 **"고객사"** 입니다. 내부 API·URL에서는 같은 개념이 `tenant`로 나옵니다(`/tenants`). 고객 응대·내부 커뮤니케이션 모두 "고객사"로 통일하십시오. 예전 문서의 "테넌트"는 같은 뜻입니다.
{% endhint %}
