# 🍯 꿀단지 (HoneyPot)
> **"개인의 경험을 조직의 자산으로"** > AI-powered HR SaaS for seamless handover and tacit knowledge management.

---

## 1. 🎯 프로젝트 개요 (Project Overview)
* **배경(Background)**: 퇴사 시 발생하는 업무 지식(암묵지)의 소실 및 비표준화된 인수인계 프로세스 문제 해결
* **솔루션(Solution)**: 업무 문서(PDF, Code, Data 등)를 분석하여 AI가 구조화된 인수인계서 초안을 생성하고 관리하는 SaaS 플랫폼
* **개발 기간(Period)**: 2025.12.22 ~ 2026.01.02
* **팀 구성(Team)**: PM, SALES/UX, **Frontend(Lead)**, Backend, RAG/LLM

## 2. 🏗️ 시스템 아키텍처 (System Architecture)
* **Frontend**: React 19.2 (Vite), TypeScript, Tailwind CSS
* **Backend**: FastAPI (Python), JWT, CSRF Protection
* **AI/LLM**: Azure OpenAI (GPT-4o), Google Gemini 2.0 Flash
* **Search/Storage**: Azure AI Search (RAG), Azure Blob Storage, Azure Key Vault

## 3. 🛠️ 핵심 기능 (Key Features)
* **AI Handover Report**: 6개 섹션(개요, 직무, 과제, 현황, 자료, 확인) 자동 생성 및 구조화
* **RAG-based AI Mentor**: 업로드된 문서 및 인덱스를 기반으로 한 실시간 도메인 특화 Q&A
* **Enterprise-grade Security**: Microsoft Azure 환경 통합 및 사내망 보안 프로토콜 준수

---

## 💻 My Contributions (Lead Frontend Developer)

저는 프로젝트의 프론트엔드 아키텍처를 설계하고, **복잡한 AI 프로세스를 사용자 중심의 직관적인 워크플로우로 전환**하는 데 주력했습니다.

### ✅ 전문 인수인계 워크플로우 고도화 (Workflow Optimization)
* **기능적 모듈 분리**: 기존의 단순 챗봇 UI를 **[전문 리포트 생성 존]**과 **[채팅 세션 관리 존]**으로 분리하여 비즈니스 전문성 강화.
* **Interactive Editing UI**: AI가 생성한 초안을 사용자가 실시간으로 검토 및 수정/추가할 수 있는 인터페이스를 구현하여 최종 문서의 무결성 보장.
* **Approval Process Implementation**: 실무 프로세스를 반영하여 인계자/인수자 및 **최종 매니저 승인 서명란**을 추가, 신뢰 기반의 문서화 완료.

### ✅ 실시간 상태 대시보드 및 데이터 시각화 (Data Visualization)
* **Real-time Polling Dashboard**: `fetchStats()` 함수를 통해 5초 간격으로 백엔드의 인덱싱 상태(문서 개수, 최근 업로드 현황)를 실시간 업데이트하여 시스템 투명성 제공.
* **Dynamic Index Controller**: Azure AI Search 인덱스를 드롭다운 형태로 선택 가능하게 구현하여, 부서 및 역할에 따른 유연한 리포트 생성 환경 구축.

### ✅ 보안 및 확장성 강화 (Security & Scalability)
* **Enterprise Auth System**: JWT 인증 및 CSRF 방어 로직이 적용된 보안 로그인 시스템을 구축하고 Azure Key Vault와 연동된 안전한 데이터 통신 지원.
* **Multi-Format Ingestion**: 이미지(.webp, .svg), 소스코드(.tsx, .sql), 데이터 파일(.ipynb, .json) 등 20여 종의 확장자를 처리하는 지능형 업로드 모듈 개발.

---

## 📂 Project Structure
```text
.
├── frontend/             # React, TypeScript, Vite (UI/UX Logic)
├── backend/              # FastAPI, Azure SDK (API & Auth)
├── core/                 # RAG & LLM Logic (GPT-4o, Gemini Strategy)
└── infra/                # Azure Resource Templates (Key Vault, AI Search)
