# 📅 Memo-to-Schedule API Specification (v1.0)

이 문서는 AI 기반 일정 추출 서비스의 API 규격을 정의합니다. 모든 요청과 응답은 `application/json` 형식을 사용하며, 시간 데이터는 `ISO 8601` 형식을 따릅니다.

---

## 1. Memos (메모 리소스)

### 1.1 메모 생성 및 일정 자동 추출
사용자가 입력한 메모 텍스트를 저장함과 동시에, AI(LangChain)가 일정 정보를 분석하여 반환합니다.

- **Method**: `POST`
- **URL**: `/api/v1/memos`
- **Description**: 메모 저장 및 AI 일정 분석 실행

#### Request Body
| Field | Type | Required | Description |
| :--- | :--- | :--- | :--- |
| **userId** | Long | Yes | 사용자의 고유 식별 번호 |
| **content** | String | Yes | 사용자가 입력한 메모 전체 텍스트 |
| **requestTime** | String | Yes | 클라이언트의 현재 시간 (예: `2026-03-10T18:00:00`) |

#### Response Body (201 Created)
```json
{
  "memoId": 100,
  "content": "내일 3시 강남역 스터디",
  "createdAt": "2026-03-10T18:00:00",
  "extractedSchedule": {
    "scheduleId": 50,
    "title": "스터디",
    "startDateTime": "2026-03-11T15:00:00",
    "location": "강남역",
    "isConfirmed": false
  }
}
```

#### Error Cases
| Status | Error Code | Description |
| :--- | :--- | :--- |
| 400 | INVALID_INPUT | content 필드가 누락되었거나 비어 있음 |
| 400 | CONTENT_TOO_LONG | 메모 내용이 제한 글자수(1,000자)를 초과함 |
| 500 | AI_SERVICE_ERROR | AI 서버(LangChain) 응답 지연 또는 분석 실패 |
| 500 | INTERNAL_SERVER_ERROR | 서버 내부의 알 수 없는 논리 오류 |

---

## Design Notes (설계 의도)

이 프로젝트의 API는 다음과 같은 의도로 설계되었습니다.

1. **POST & 201 Created**
   - 단순히 데이터를 전달하는 것을 넘어, 서버 측에서 새로운 리소스(메모 및 일정)가 생성되었음을 HTTP 상태 코드를 통해 명확히 명시합니다.
2. **requestTime**
   - 사용자가 "내일", "모레"와 같은 상대적인 날짜를 입력했을 때, AI가 정확한 날짜를 계산할 수 있도록 기준점(오늘 날짜/시간)을 클라이언트로부터 전달받습니다.
3. **Nesting(계층화) 구조**
   - `extractedSchedule`을 별도 객체로 분리하여, 향후 일정 정보에 필드(참석자, 알림 설정 등)가 추가되더라도 메모 정보와 독립적으로 확장할 수 있게 설계했습니다.
4. **isConfirmed 필드 (데이터 신뢰도 관리)**
   - AI의 분석 결과는 100% 완벽하지 않을 수 있습니다. 사용자가 최종적으로 "확인/저장" 버튼을 누르기 전까지는 `false` 상태로 유지하여, 시스템 데이터의 신뢰도를 보장합니다.