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