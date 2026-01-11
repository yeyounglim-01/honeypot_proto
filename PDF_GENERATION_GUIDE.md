# 인수인계서 PDF 생성 가이드

## 개요

PyPDF2, python-docx, reportlab 라이브러리를 사용하여 인수인계서를 PDF로 생성하고 저장하는 기능이 추가되었습니다.

## 설치된 라이브러리

```txt
PyPDF2>=3.0.0        # PDF 메타데이터 처리
python-pptx>=0.6.23  # PowerPoint 문서 처리 (향후 확장용)
reportlab>=4.0.0     # PDF 생성
```

## 의존성 설치

```bash
pip install -r requirements.txt
```

## 한글 폰트 설치 (필수)

PDF에서 한글을 표시하려면 나눔고딕 폰트가 필요합니다.

### Linux (Ubuntu/Debian)
```bash
sudo apt-get update
sudo apt-get install fonts-nanum fonts-nanum-coding
```

### macOS
```bash
brew tap homebrew/cask-fonts
brew install --cask font-nanum-gothic
```

또는 [나눔글꼴 공식 사이트](https://hangeul.naver.com/2017/nanum)에서 다운로드하여 수동 설치

## API 사용법

### 엔드포인트

```
POST /generate-pdf
```

### 인증

- JWT 토큰 필요 (Authorization 헤더)
- CSRF 토큰 필요 (X-CSRF-Token 헤더)

### 요청 형식

```json
{
  "handover_data": {
    "overview": {
      "transferor": {"name": "김철수", "position": "과장", "contact": "010-1234-5678"},
      "transferee": {"name": "이영희", "position": "대리", "contact": "010-9876-5432", "startDate": "2025-02-15"},
      "reason": "부서 이동",
      "background": "시스템 고도화 프로젝트 진행 중",
      "period": "2022-01-01 ~ 2025-01-31",
      "schedule": [
        {"date": "2025-02-01", "activity": "업무 개요 설명"},
        {"date": "2025-02-10", "activity": "주요 시스템 교육"}
      ]
    },
    "jobStatus": {
      "title": "백엔드 개발 담당",
      "responsibilities": ["API 개발 및 유지보수", "데이터베이스 설계"],
      "authority": "개발 서버 관리 권한",
      "reportingLine": "팀장 → 본부장",
      "teamMission": "안정적인 서비스 제공",
      "teamGoals": ["시스템 성능 개선", "신규 기능 개발"]
    },
    "priorities": [
      {
        "rank": 1,
        "title": "보안 취약점 개선",
        "status": "진행중",
        "solution": "외부 보안 감사 진행",
        "deadline": "2025-03-01"
      }
    ],
    "stakeholders": {
      "manager": "박부장",
      "internal": [{"name": "최민수", "role": "QA"}],
      "external": [{"name": "정외주", "role": "고객사 담당자"}]
    },
    "teamMembers": [
      {
        "name": "박준호",
        "position": "선임",
        "role": "프론트엔드 개발",
        "notes": "React 전문"
      }
    ],
    "ongoingProjects": [
      {
        "name": "시스템 고도화",
        "owner": "김철수",
        "status": "진행중",
        "progress": 70,
        "deadline": "2025-03-31",
        "description": "메인 기능 개발 완료, 최적화 단계"
      }
    ],
    "risks": {
      "issues": "일정 지연 가능성",
      "risks": "신규 인력 적응 기간 필요"
    },
    "roadmap": {
      "shortTerm": "알파 테스트 완료",
      "longTerm": "서비스 오픈"
    },
    "resources": {
      "docs": [
        {
          "category": "설계서",
          "name": "API 명세서",
          "location": "Confluence"
        }
      ],
      "systems": [
        {
          "name": "개발 서버",
          "usage": "SSH 접속",
          "contact": "인프라팀"
        }
      ],
      "contacts": [
        {
          "category": "외부",
          "name": "고객사 담당자",
          "position": "과장",
          "contact": "02-1234-5678"
        }
      ]
    },
    "checklist": [
      {"text": "업무 인수인계서 작성 완료", "completed": true},
      {"text": "주요 시스템 접근 권한 이관", "completed": false}
    ]
  },
  "save_to_blob": false
}
```

### 응답 형식

#### 직접 다운로드 모드 (save_to_blob: false)

```
Content-Type: application/pdf
Content-Disposition: attachment; filename=handover_20250111_143052.pdf

[PDF 바이너리 데이터]
```

#### Blob 저장 모드 (save_to_blob: true)

```json
{
  "success": true,
  "message": "PDF가 성공적으로 생성되어 저장되었습니다.",
  "blob_url": "https://storage.blob.core.windows.net/container/handovers/user@example.com/handover_user_20250111_143052.pdf?sv=...",
  "filename": "handover_user_20250111_143052.pdf",
  "size": 245678
}
```

## 프론트엔드 통합 예시

### React/TypeScript

```typescript
// HandoverForm.tsx에 PDF 생성 버튼 추가
const handleGeneratePDF = async () => {
  try {
    const response = await fetch('/generate-pdf', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${authToken}`,
        'X-CSRF-Token': csrfToken
      },
      body: JSON.stringify({
        handover_data: handoverData,
        save_to_blob: false  // true로 설정하면 Blob에 저장
      })
    });

    if (response.ok) {
      // 직접 다운로드
      const blob = await response.blob();
      const url = window.URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `handover_${new Date().getTime()}.pdf`;
      document.body.appendChild(a);
      a.click();
      window.URL.revokeObjectURL(url);
      document.body.removeChild(a);
    }
  } catch (error) {
    console.error('PDF 생성 실패:', error);
  }
};

// JSX
<button onClick={handleGeneratePDF}>
  PDF 다운로드
</button>
```

### Blob 저장 후 URL 받기

```typescript
const handleSavePDFToBlob = async () => {
  try {
    const response = await fetch('/generate-pdf', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${authToken}`,
        'X-CSRF-Token': csrfToken
      },
      body: JSON.stringify({
        handover_data: handoverData,
        save_to_blob: true
      })
    });

    const result = await response.json();

    if (result.success) {
      console.log('PDF URL:', result.blob_url);
      console.log('파일명:', result.filename);
      console.log('크기:', result.size, 'bytes');

      // URL을 사용하여 다른 용도로 활용 가능
      // 예: 이메일 전송, 공유, 데이터베이스 저장 등
    }
  } catch (error) {
    console.error('PDF 저장 실패:', error);
  }
};
```

## PDF 구조

생성되는 PDF는 다음과 같은 섹션으로 구성됩니다:

1. **인수인계 개요** - 인계자/인수자 정보, 배경
2. **직무 현황** - 직책, 책임사항, 팀 미션
3. **우선 과제** - 주요 업무 및 마감일
4. **주요 관계자** - 내부/외부 관계자 목록
5. **팀원 정보** - 팀 구성원 정보
6. **진행 중인 프로젝트** - 프로젝트 상태 및 진행률
7. **리스크 및 이슈** - 현안 사항 및 위험 요소
8. **주요 리소스** - 문서, 시스템, 연락처
9. **체크리스트** - 인수인계 확인 항목
10. **서명란** - 인계자, 인수자, 승인자 서명

## 테스트

### 로컬 테스트

```bash
# 백엔드 실행
cd /home/user/honeypot_proto
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# 프론트엔드 실행 (다른 터미널)
cd /home/user/honeypot_proto/frontend
npm run dev
```

### cURL 테스트

```bash
curl -X POST "http://localhost:8000/generate-pdf" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -H "X-CSRF-Token: YOUR_CSRF_TOKEN" \
  -H "Content-Type: application/json" \
  -d @test_handover_data.json \
  --output handover.pdf
```

## 기술 스택

- **reportlab**: PDF 레이아웃 및 테이블 생성
- **PyPDF2**: PDF 메타데이터 추가 및 후처리
- **python-pptx**: PowerPoint 문서 처리 (향후 확장용)
- **NanumGothic**: 한글 폰트 지원

## 특징

- ✅ 한글 완벽 지원 (나눔고딕 폰트)
- ✅ A4 크기, 전문적인 레이아웃
- ✅ 섹션별 색상 구분 및 표 스타일
- ✅ PDF 메타데이터 포함
- ✅ Azure Blob Storage 저장 지원
- ✅ 직접 다운로드 지원
- ✅ CSRF 보호 및 JWT 인증
- ✅ 사용자별 파일 관리

## 문제 해결

### 한글이 깨지는 경우

나눔고딕 폰트가 설치되지 않았을 가능성이 높습니다. 위의 "한글 폰트 설치" 섹션을 참고하세요.

### Blob 저장 실패

Azure Storage 계정 설정을 확인하세요:
- `AZURE_STORAGE_ACCOUNT_NAME`
- `AZURE_STORAGE_ACCOUNT_KEY`
- 컨테이너 접근 권한

### PDF 생성 오류

```bash
# 의존성 재설치
pip install --upgrade PyPDF2 reportlab python-pptx

# 폰트 경로 확인
find /usr/share/fonts -name "*Nanum*"
find /Library/Fonts -name "*Nanum*"
```

## 향후 개선 사항

- [ ] 다양한 템플릿 지원
- [ ] 로고 및 이미지 삽입
- [ ] 전자 서명 기능
- [ ] Word 문서 생성 (python-docx 활용)
- [ ] PowerPoint 생성 (python-pptx 활용)
- [ ] PDF 암호화 및 권한 설정
- [ ] 다국어 지원
