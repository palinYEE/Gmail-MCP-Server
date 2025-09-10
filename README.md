# Gmail MCP 서버 (액세스 토큰 인증)

Claude Desktop에서 Gmail을 직접 관리할 수 있게 해주는 Model Context Protocol (MCP) 서버입니다. 액세스 토큰을 사용한 간단한 인증으로 AI 어시스턴트가 Gmail을 자연어로 제어할 수 있습니다.

![](https://badge.mcpx.dev?type=server 'MCP Server')
[![smithery badge](https://smithery.ai/badge/@palinYEE/gmail-mcp-server)](https://smithery.ai/server/@palinYEE/gmail-mcp-server)

## 주요 기능

### 📧 이메일 관리
- **이메일 전송**: 제목, 본문, **파일 첨부**, 수신자 지정 가능
- **첨부 파일 완벽 지원**: 파일 첨부 전송 및 수신
- **첨부 파일 다운로드**: 이메일 첨부 파일을 로컬에 저장
- **HTML 이메일 지원**: HTML 및 멀티파트 메시지 (HTML + 일반 텍스트) 지원
- **국제 문자 지원**: 제목과 내용에서 한글, 중국어, 일본어 등 모든 언어 지원
- **이메일 읽기**: 고급 MIME 구조 처리로 메시지 ID별 이메일 조회
- **첨부 파일 상세 정보**: 파일명, 타입, 크기, 다운로드 ID 표시
- **이메일 검색**: 제목, 발신자, 날짜 범위 등 다양한 조건으로 검색
- **이메일 삭제**: 개별 또는 일괄 삭제

### 🏷️ 라벨 관리
- **라벨 생성/수정/삭제**: 완벽한 라벨 CRUD 기능
- **라벨 자동 생성**: 존재하지 않는 라벨 자동 생성 옵션
- **라벨 가시성 설정**: 메시지 목록 및 라벨 목록에서 표시 방법 제어
- **시스템/사용자 라벨 구분**: Gmail 시스템 라벨과 사용자 정의 라벨 모두 관리

### 🔧 필터 관리
- **필터 생성**: 다양한 조건과 동작으로 필터 생성
- **템플릿 기반 필터**: 일반적인 시나리오를 위한 사전 정의된 템플릿
- **필터 조회/삭제**: 기존 필터 관리
- **자동 이메일 정리**: 필터로 수신 메일 자동 분류

### ⚡ 배치 작업
- **대량 이메일 처리**: 최대 50개씩 일괄 처리 (설정 가능)
- **효율적인 작업**: API 제한을 피하기 위한 자동 청킹
- **상세한 결과 보고**: 각 작업의 성공/실패 개별 보고

## 설치 및 인증

### Gmail 액세스 토큰 받기

이 서버를 사용하려면 적절한 권한을 가진 Gmail 액세스 토큰이 필요합니다:

#### 방법 1: Google OAuth Playground (테스트용 - 가장 쉬움)
1. [Google OAuth 2.0 Playground](https://developers.google.com/oauthplayground/) 접속
2. Step 1에서 다음 스코프 선택:
   - `https://www.googleapis.com/auth/gmail.modify`
   - `https://www.googleapis.com/auth/gmail.settings.basic`
3. "Authorize APIs" 클릭 후 Google 계정으로 로그인
4. Step 2에서 "Exchange authorization code for tokens" 클릭
5. 응답에서 "Access token" 복사

#### 방법 2: Google Cloud Console (프로덕션용)
1. [Google Cloud Console](https://console.cloud.google.com/)에서 프로젝트 생성
2. Gmail API 활성화
3. OAuth 2.0 자격 증명 생성
4. 원하는 OAuth 플로우를 사용하여 필요한 스코프로 액세스 토큰 획득

### 서버 실행

액세스 토큰은 두 가지 방법으로 제공할 수 있습니다:

#### 옵션 1: 명령줄 인수
```bash
npx @palinYEE/gmail-mcp-server YOUR_ACCESS_TOKEN_HERE
```

#### 옵션 2: 환경 변수
```bash
export GMAIL_ACCESS_TOKEN=YOUR_ACCESS_TOKEN_HERE
npx @palinYEE/gmail-mcp-server
```

### Claude Desktop 설정

`claude_desktop_config.json` 파일에 다음과 같이 설정:

```json
{
  "mcpServers": {
    "gmail": {
      "command": "npx",
      "args": [
        "@palinYEE/gmail-mcp-server",
        "YOUR_ACCESS_TOKEN_HERE"
      ]
    }
  }
}
```

또는 환경 변수 사용:

```json
{
  "mcpServers": {
    "gmail": {
      "command": "npx",
      "args": [
        "@palinYEE/gmail-mcp-server"
      ],
      "env": {
        "GMAIL_ACCESS_TOKEN": "YOUR_ACCESS_TOKEN_HERE"
      }
    }
  }
}
```

## 사용 가능한 도구

### 1. 이메일 전송 (`send_email`)

새 이메일을 즉시 전송합니다. **파일 첨부 지원**.

기본 이메일:
```json
{
  "to": ["recipient@example.com"],
  "subject": "내일 회의",
  "body": "안녕하세요,\n\n내일 오전 10시 회의 잊지 마세요.\n\n감사합니다",
  "cc": ["cc@example.com"],
  "bcc": ["bcc@example.com"],
  "mimeType": "text/plain"
}
```

**파일 첨부 이메일:**
```json
{
  "to": ["recipient@example.com"],
  "subject": "프로젝트 파일",
  "body": "안녕하세요,\n\n프로젝트 파일을 첨부합니다.\n\n감사합니다",
  "attachments": [
    "/path/to/document.pdf",
    "/path/to/spreadsheet.xlsx"
  ]
}
```

HTML 이메일:
```json
{
  "to": ["recipient@example.com"],
  "subject": "내일 회의",
  "mimeType": "text/html",
  "body": "<html><body><h1>회의 알림</h1><p><b>내일 회의</b> 오전 10시입니다.</p></body></html>"
}
```

### 2. 이메일 초안 (`draft_email`)
전송하지 않고 초안으로 저장합니다. **첨부 파일 지원**.

```json
{
  "to": ["recipient@example.com"],
  "subject": "보고서 초안",
  "body": "검토를 위한 보고서 초안입니다.",
  "attachments": ["/path/to/draft_report.docx"]
}
```

### 3. 이메일 읽기 (`read_email`)
특정 이메일의 내용을 조회합니다. **첨부 파일 정보 포함**.

```json
{
  "messageId": "182ab45cd67ef"
}
```

**응답 예시:**
```
제목: 프로젝트 파일
발신: sender@example.com
수신: recipient@example.com
날짜: Thu, 19 Jun 2025 10:30:00 -0400

이메일 본문...

첨부 파일 (2):
- document.pdf (application/pdf, 245 KB, ID: ANGjdJ9fkTs-i3GCQo5o97f_itG...)
- spreadsheet.xlsx (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, 89 KB, ID: BWHkeL8gkUt...)
```

### 4. **첨부 파일 다운로드 (`download_attachment`)**
이메일 첨부 파일을 로컬에 저장합니다.

```json
{
  "messageId": "182ab45cd67ef",
  "attachmentId": "ANGjdJ9fkTs-i3GCQo5o97f_itG...",
  "savePath": "/path/to/downloads",
  "filename": "downloaded_document.pdf"
}
```

매개변수:
- `messageId`: 첨부 파일이 있는 이메일 ID
- `attachmentId`: 첨부 파일 ID (이메일 읽기에서 확인 가능)
- `savePath`: 저장할 디렉토리 (선택사항, 기본값: 현재 디렉토리)
- `filename`: 사용자 정의 파일명 (선택사항, 기본값: 원본 파일명)

### 5. 이메일 검색 (`search_emails`)
Gmail 검색 문법을 사용하여 이메일을 검색합니다.

```json
{
  "query": "from:sender@example.com after:2024/01/01 has:attachment",
  "maxResults": 10
}
```

### 6. 이메일 수정 (`modify_email`)
이메일에 라벨을 추가하거나 제거합니다 (폴더 이동, 보관 등).

```json
{
  "messageId": "182ab45cd67ef",
  "addLabelIds": ["IMPORTANT"],
  "removeLabelIds": ["INBOX"]
}
```

### 7. 이메일 삭제 (`delete_email`)
이메일을 영구 삭제합니다.

```json
{
  "messageId": "182ab45cd67ef"
}
```

### 8. 라벨 목록 조회 (`list_email_labels`)
사용 가능한 모든 Gmail 라벨을 조회합니다.

```json
{}
```

### 9. 라벨 생성 (`create_label`)
새 Gmail 라벨을 생성합니다.

```json
{
  "name": "중요 프로젝트",
  "messageListVisibility": "show",
  "labelListVisibility": "labelShow"
}
```

### 10. 라벨 수정 (`update_label`)
기존 Gmail 라벨을 수정합니다.

```json
{
  "id": "Label_1234567890",
  "name": "긴급 프로젝트",
  "messageListVisibility": "show",
  "labelListVisibility": "labelShow"
}
```

### 11. 라벨 삭제 (`delete_label`)
Gmail 라벨을 삭제합니다.

```json
{
  "id": "Label_1234567890"
}
```

### 12. 라벨 가져오기 또는 생성 (`get_or_create_label`)
이름으로 라벨을 찾거나 없으면 생성합니다.

```json
{
  "name": "프로젝트 XYZ",
  "messageListVisibility": "show",
  "labelListVisibility": "labelShow"
}
```

### 13. 일괄 이메일 수정 (`batch_modify_emails`)
여러 이메일의 라벨을 효율적으로 일괄 수정합니다.

```json
{
  "messageIds": ["182ab45cd67ef", "182ab45cd67eg", "182ab45cd67eh"],
  "addLabelIds": ["IMPORTANT"],
  "removeLabelIds": ["INBOX"],
  "batchSize": 50
}
```

### 14. 일괄 이메일 삭제 (`batch_delete_emails`)
여러 이메일을 효율적으로 일괄 삭제합니다.

```json
{
  "messageIds": ["182ab45cd67ef", "182ab45cd67eg", "182ab45cd67eh"],
  "batchSize": 50
}
```

## 필터 관리 기능

### 필터 생성 (`create_filter`)
사용자 정의 조건과 동작으로 새 Gmail 필터를 생성합니다.

```json
{
  "criteria": {
    "from": "newsletter@company.com",
    "hasAttachment": false
  },
  "action": {
    "addLabelIds": ["Label_Newsletter"],
    "removeLabelIds": ["INBOX"]
  }
}
```

### 필터 템플릿 (`create_filter_from_template`)
일반적인 시나리오를 위한 사전 정의된 템플릿을 사용합니다.

사용 가능한 템플릿:
- `fromSender`: 특정 발신자 필터링
- `withSubject`: 제목 텍스트 필터링
- `withAttachments`: 첨부 파일 있는 이메일
- `largeEmails`: 대용량 이메일
- `containingText`: 특정 텍스트 포함
- `mailingList`: 메일링 리스트

예시:
```json
{
  "template": "fromSender",
  "parameters": {
    "senderEmail": "notifications@github.com",
    "labelIds": ["Label_GitHub"],
    "archive": true
  }
}
```

## 고급 검색 문법

`search_emails` 도구는 Gmail의 강력한 검색 연산자를 지원합니다:

| 연산자 | 예시 | 설명 |
|--------|------|------|
| `from:` | `from:john@example.com` | 특정 발신자 |
| `to:` | `to:mary@example.com` | 특정 수신자 |
| `subject:` | `subject:"회의 노트"` | 제목에 특정 텍스트 |
| `has:attachment` | `has:attachment` | 첨부 파일 있는 이메일 |
| `after:` | `after:2024/01/01` | 특정 날짜 이후 |
| `before:` | `before:2024/02/01` | 특정 날짜 이전 |
| `is:` | `is:unread` | 특정 상태 |
| `label:` | `label:work` | 특정 라벨 |

여러 연산자 조합 가능: `from:john@example.com after:2024/01/01 has:attachment`

## 고급 기능

### 📎 첨부 파일 지원
- **전송**: `attachments` 배열에 파일 경로 포함
- **자동 감지**: MIME 타입과 파일 크기 자동 감지
- **다운로드**: 모든 이메일 첨부 파일을 로컬에 저장
- **상세 정보**: 파일명, 타입, 크기, 다운로드 ID 표시
- **다양한 형식**: PDF, DOCX, XLSX, PPTX, 이미지, 압축 파일 등 모든 파일 형식 지원

### 🌍 국제 문자 지원
- 한글, 중국어, 일본어, 아랍어 등 모든 언어 지원
- 특수 문자 및 기호 지원
- 올바른 인코딩으로 모든 이메일 클라이언트에서 정상 표시

### 🏷️ 라벨 가시성 옵션
- `messageListVisibility`: 메시지 목록에서 라벨 표시 (`show` 또는 `hide`)
- `labelListVisibility`: 라벨 목록에서 표시 방법 (`labelShow`, `labelShowIfUnread`, `labelHide`)

### ⚡ 배치 작업
- 한 번에 최대 50개 이메일 처리 (설정 가능)
- API 제한 회피를 위한 자동 청킹
- 각 작업의 성공/실패 상세 보고
- 개별 재시도를 통한 우아한 오류 처리

## 보안 주의사항

- **액세스 토큰을 절대 버전 관리 시스템에 커밋하지 마세요**
- 액세스 토큰은 주기적으로 만료됩니다 - 만료 시 새로 발급받아야 합니다
- 사용 사례에 필요한 최소한의 권한만 부여하세요
- Google 계정 설정에서 사용하지 않는 토큰을 정기적으로 검토하고 취소하세요
- **첨부 파일은 로컬에서 처리되며 서버에 영구 저장되지 않습니다**

## 문제 해결

### 1. 액세스 토큰 미제공
- 명령줄 인수 또는 환경 변수로 액세스 토큰을 제공했는지 확인
- 토큰 형식이 올바른지 확인 (추가 공백이나 따옴표 제거)

### 2. 인증 실패 / 401 오류
- 액세스 토큰이 만료되었을 수 있음 - 새로 발급
- 토큰에 필요한 Gmail 스코프가 있는지 확인
- 올바른 Google 계정을 사용하는지 확인

### 3. 403 금지 오류
- Google Cloud 프로젝트에서 Gmail API가 활성화되어 있는지 확인
- 토큰에 필요한 스코프(`gmail.modify` 및 `gmail.settings.basic`)가 있는지 확인

### 4. 배치 작업 실패
- 배치 작업이 실패하면 자동으로 개별 항목 재시도
- 특정 실패에 대한 상세 오류 메시지 확인
- 속도 제한에 걸리면 배치 크기 줄이기 고려

### 5. 첨부 파일 문제
- **파일 찾을 수 없음**: 첨부 파일 경로가 올바르고 접근 가능한지 확인
- **권한 오류**: 서버가 첨부 파일에 대한 읽기 권한이 있는지 확인
- **크기 제한**: Gmail은 이메일당 25MB 첨부 파일 크기 제한
- **다운로드 실패**: 다운로드 디렉토리에 쓰기 권한이 있는지 확인

## 개발 및 테스트

### 평가 실행

MCP 평가를 실행하여 서버 기능을 테스트할 수 있습니다:

```bash
OPENAI_API_KEY=your-key npx mcp-eval src/evals/evals.ts src/index.ts
```

평가 패키지는 index.ts 파일을 실행하는 MCP 클라이언트를 로드하므로 테스트 간에 재빌드가 필요하지 않습니다.

## 기여

기여를 환영합니다! Pull Request를 자유롭게 제출해 주세요.

## 라이선스

MIT

## 지원

문제가 발생하거나 질문이 있으면 GitHub 저장소에 이슈를 등록해 주세요.