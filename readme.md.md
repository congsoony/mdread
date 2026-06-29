# 🚀 StudyFlow Backend Tech Stack & Architecture

<style>
/* 트래픽이 흘러가는 애니메이션 (VSCode 마크다운 프리뷰 등에서 동작) */
@keyframes dashFlow {
  to { stroke-dashoffset: -20; }
}
/* Mermaid가 그린 SVG 선(Path)에 글로벌 애니메이션 일괄 적용 */
.mermaid .edgePath .path {
  stroke-dasharray: 6 6 !important;
  animation: dashFlow 1s linear infinite !important;
  stroke: #38bdf8 !important; /* 빛나는 파란색 선 */
}
</style>

> 💡 **Tip:** VSCode 우측 상단의 **미리보기(Preview) 돋보기 버튼**을 켜시면 아래 다이어그램에서 실시간 트래픽 애니메이션이 작동합니다.

---

## 1. 💻 핵심 프레임워크 및 코어 (Core)
가장 안정적이면서도 최신의 트렌드를 모두 수용한 모던 자바 엔터프라이즈 환경입니다.
* **언어**: `Java 21` (가상 스레드, 패턴 매칭 등 최신 문법 활용)
* **프레임워크**: `Spring Boot 3.5.14`
* **보안(Security)**: `Spring Security` + `JWT (0.12.6)` + `OAuth2 Client` (소셜 로그인 및 토큰 기반 무상태 인증)

## 2. 🗄️ 데이터베이스 및 영속성 (Data Layer)
복잡한 쿼리와 데이터베이스 병목을 막기 위해 고도화된 ORM 기술을 사용합니다.
* **데이터베이스**: `MySQL` (AWS RDS 환경)
* **ORM**: `Spring Data JPA` (기본적인 CRUD 및 엔티티 생명주기 관리)
* **동적 쿼리**: `QueryDSL 5.1.0 (Jakarta)` (컴파일 단계에서 SQL 오류를 잡아내고, 복잡한 검색 및 필터링 쿼리를 객체지향적으로 작성)

## 3. ⚡ 실시간 및 비동기 처리 (Real-time & Async)
사용자가 로딩 창을 보며 기다리게 하지 않고, 실시간 상호작용을 가능하게 하는 핵심 기술들입니다.
* **실시간 양방향 통신**: `Spring WebSocket` + `STOMP` (화이트보드 그리기 좌푯값, 채팅, 리액션 전송)
* **논블로킹 스트리밍**: `Spring WebFlux` (WebClient, SSE) 
  * AI가 답변을 생성할 때 서버의 톰캣 쓰레드를 멈춰 세우지 않고(Non-blocking), 타이핑 치듯 한 글자씩 프론트로 내려보내기 위해(Server-Sent Events) 적용되었습니다.

## 4. 🌐 분산 시스템 및 인프라 (Distributed Systems)
서버가 2대 이상으로 스케일 아웃(Scale-out) 되더라도 데이터가 꼬이거나 찢어지지 않도록 방어하는 아키텍처입니다.
* **메시지 브로커**: `Spring Data Redis` (Pub/Sub)
  * 서버 A에 접속한 철수와 서버 B에 접속한 영희가 같은 화이트보드 화면을 볼 수 있도록, Redis가 중간에서 웹소켓 메시지를 릴레이 해줍니다.
* **분산 락 스케줄링**: `ShedLock` (Redis Provider)
  * 정해진 시간에 작동해야 하는 스케줄러(배치 작업 등)가 서버 2대에서 중복으로 2번 실행되는 대참사를 막기 위해, Redis에 "내가 실행 중!"이라는 락(Lock)을 걸어 단일 실행을 보장합니다.

## 5. 🤖 AI & 서드파티 연동 (Integrations)
* **AI 통신**: `Spring AI (OpenAI)` (복잡한 API 통신 로직을 추상화하여 안전하게 LLM과 통신)
* **파일 스토리지**: `AWS SDK S3` (로컬 디스크가 아닌 클라우드에 직접 이미지, 오디오 파일 업로드)
* **API 문서화**: `SpringDoc (Swagger UI)` (개발 및 프론트엔드 협업용 자동화 문서)

<br>

---

## 🏗️ 백엔드 실시간 분산 아키텍처 (Animated Diagram)
백엔드 내부에서 요청이 어떻게 처리되고, 분산 시스템(Redis/DB)과 어떻게 맞물려 돌아가는지 보여주는 아키텍처입니다.

```mermaid
flowchart TB
    classDef default fill:#1E293B,stroke:#475569,stroke-width:2px,color:#F8FAFC
    classDef db fill:#EC4899,stroke:#DB2777,stroke-width:2px,color:white
    classDef cache fill:#F59E0B,stroke:#D97706,stroke-width:2px,color:white
    classDef ai fill:#8B5CF6,stroke:#7C3AED,stroke-width:2px,color:white
    classDef app fill:#10B981,stroke:#059669,stroke-width:2px,color:white

    Client["🧑 프론트엔드 (React)"]:::default

    subgraph SpringBoot["Spring Boot 3.5 (App Layer)"]
        MVC["⚙️ Spring MVC<br/>(REST API / JPA)"]:::app
        Socket["⚡ STOMP WebSocket<br/>(실시간 판서 / 채팅)"]:::app
        WebFlux["🌀 Spring WebFlux<br/>(SSE / AI 스트리밍)"]:::app
    end

    subgraph External["External Services"]
        AI["🤖 OpenAI API<br/>(Spring AI)"]:::ai
        S3["🪣 AWS S3<br/>(File Upload)"]:::cache
    end

    subgraph DataStore["Stateful Distributed Layer"]
        Redis["🔥 Redis<br/>(Pub/Sub 릴레이 & ShedLock 분산 락)"]:::cache
        MySQL[("🗄️ MySQL (RDS)<br/>(JPA & QueryDSL)")]:::db
    end

    %% 트래픽 화살표 연결 (글로벌 CSS로 전부 애니메이션 됨)
    Client -->|1. 일반 API 요청| MVC
    Client <-->|2. ws:// 양방향 통신| Socket
    Client <--|3. text/event-stream| WebFlux

    MVC -->|데이터 CRUD| MySQL
    MVC -->|파일 전송| S3
    
    Socket <-->|메시지 브로드캐스트| Redis
    
    WebFlux <-->|논블로킹 프롬프트| AI
    WebFlux -->|스트리밍 응답| Client
```

> **📌 시스템 핵심 포인트 요약**
> 백엔드의 가장 큰 성과는 **WebFlux와 Redis를 도입한 점**입니다. 
> 1. AI 응답을 기다리는 동안 쓰레드가 블로킹되면 대규모 접속 시 서버가 죽을 수 있는데, 이를 **WebFlux(비동기)**로 해결했습니다.
> 2. 다중 서버 환경에서 웹소켓 세션이 분리되는 문제를 **Redis Pub/Sub**으로 완벽하게 극복해냈습니다.
