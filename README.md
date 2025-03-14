📌 Google Calendar API Quickstart - README

⸻

📖 개요

이 프로젝트는 Google Calendar API를 활용하여 사용자의 Google 캘린더에서 일정을 조회하고 추가하는 기능을 체험해볼 수 있습니다.

⸻

🚀 설치 및 실행 방법

1️⃣ 필요한 패키지 설치

먼저, 프로젝트 폴더에서 HTTP 서버를 실행하기 위해 http-server 패키지를 설치합니다.

npm install http-server

⸻

2️⃣ 로컬 서버 실행

설치가 완료되면 아래 명령어를 입력하여 로컬 서버(8000 포트)를 실행합니다.

npx http-server -p 8000

이제 브라우저에서 http://localhost:8000 에 접속하면 실행됩니다.

⸻

🔑 OAuth 설정 및 권한 승인 1. 웹페이지에서 Authorize 버튼을 클릭하면 Google 계정으로 OAuth 인증을 요청합니다. 2. 로그인한 계정에 대한 접근 권한을 요청하는 팝업이 나타납니다. 3. 계정을 선택하고 “Allow(허용)” 버튼을 클릭하여 권한을 승인합니다. 4. 승인된 후 Sign Out 버튼이 나타나면, 인증이 완료된 것입니다.

⸻

🎯 기능 설명

✅ 1. Google Calendar API 인증 (Authorize 버튼)
• Authorize 버튼을 누르면 OAuth 인증이 진행됩니다.
• 승인 후, 사용자의 Google 캘린더에 접근할 수 있습니다.

✅ 2. 일정 조회 (listUpcomingEvents 함수)
• 승인 후, 현재 로그인한 계정의 캘린더에서 최근 10개의 일정을 조회합니다.
• 조회된 일정이 웹페이지에 표시됩니다.

✅ 3. 일정 추가 (Add Google Calendar Event 버튼)
• 사용자가 버튼을 클릭하면, 새로운 일정이 Google 캘린더에 추가됩니다.
• 일정이 추가되면, Google Calendar에서 확인할 수 있는 링크(htmlLink)가 제공됩니다.

⸻

📌 코드 주요 부분 설명

🔹 Google API 및 OAuth 초기화

```
function gapiLoaded() {
gapi.load("client", initializeGapiClient);
}

async function initializeGapiClient() {
await gapi.client.init({
apiKey: API_KEY,
discoveryDocs: ["https://www.googleapis.com/discovery/v1/apis/calendar/v3/rest"],
});
gapiInited = true;
maybeEnableButtons();
}

function gisLoaded() {
tokenClient = google.accounts.oauth2.initTokenClient({
client_id: CLIENT_ID,
scope: "https://www.googleapis.com/auth/calendar.events",
callback: "", // 후에 정의됨
});
gisInited = true;
maybeEnableButtons();
}
```

✅ Google API를 로드하고 OAuth 인증을 처리하는 코드입니다.

---

🔹 일정 조회 기능

```
async function listUpcomingEvents() {
let response;
try {
response = await gapi.client.calendar.events.list({
calendarId: "primary",
timeMin: new Date().toISOString(),
maxResults: 10,
singleEvents: true,
orderBy: "startTime",
});
} catch (err) {
document.getElementById("content").innerText = err.message;
return;
}

const events = response.result.items;
if (!events || events.length == 0) {
document.getElementById("content").innerText = "No events found.";
return;
}

const output = events.reduce(
(str, event) => `${str}${event.summary} (${event.start.dateTime || event.start.date})\n`,
"Events:\n"
);
document.getElementById("content").innerText = output;
}
```

✅ 사용자의 Google 캘린더에서 일정 10개를 가져오는 코드입니다.

---

🔹 일정 추가 기능

```
function addGoogleCalendarEvent() {
const event = {
summary: "Google I/O 2025",
location: "San Francisco, CA",
description: "A chance to hear more about Google's developer products.",
start: {
dateTime: "2025-05-28T09:00:00-07:00",
timeZone: "America/Los_Angeles",
},
end: {
dateTime: "2025-05-28T17:00:00-07:00",
timeZone: "America/Los_Angeles",
},
recurrence: ["RRULE:FREQ=DAILY;COUNT=1"],
attendees: [{ email: "abc@abc.co.kr" }],
reminders: {
useDefault: false,
overrides: [
{ method: "email", minutes: 24 * 60 },
{ method: "popup", minutes: 10 },
],
},
};

gapi.client.calendar.events.insert({
calendarId: "primary",
resource: event,
}).then((response) => {
const event = response.result;
appendPre(
`✅ Event created: <a href="${event.htmlLink}" target="_blank">${event.summary}</a>`
);
console.log("✅ Event created:", event);
}).catch((error) => {
console.error("🚨 Error creating event:", error);
});
}
```

✅ 일정을 추가하고, 생성된 이벤트 링크를 반환하는 코드입니다.

⸻

⚡ 예상되는 실행 과정 1. 로컬 서버 실행

npx http-server -p 8000

    2.	브라우저에서 실행
    •	http://localhost:8000으로 이동
    •	Authorize 버튼 클릭하여 Google OAuth 인증 진행
    3.	기능 테스트
    •	Authorize 후 자동으로 캘린더 일정이 조회됨
    •	Add Google Calendar Event 버튼을 눌러 새 일정을 추가

⸻

🛠 필요한 설정

🔹 Google Cloud Console 설정

1. Google Cloud Console에 접속
2. Google Calendar API를 활성화
3. OAuth 2.0 클라이언트 ID를 생성 후 CLIENT_ID 값으로 사용
4. API 키 생성 후 API_KEY 값으로 사용
5. “승인된 자바스크립트 원본” 에 http://localhost:8000 추가
6. “승인된 리디렉션 URI” 에 http://localhost:8000 추가

⸻

✅ 참고 자료

• 📖 Google Calendar API 공식 문서

• 📖 Google Identity Services (OAuth)

⸻

🚀 이제 Google Calendar API를 활용하여 일정을 조회하고 추가할 수 있습니다! 🎉
