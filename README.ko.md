
[English](README.md) | [简体中文](README.zh.md) | **한국어** | [Español](README.es.md)

# survivalcraft-js-tools
Survivalcraft API 1.9.3.1에서 게임 내 JavaScript 원격 제어를 통해 JavaScript를 실행하고 텔레포트하는 단일 HTML 파일 도구.

# Survivalcraft JS 실행기 및 텔레포터

Android에서 게임에 내장된 JavaScript 원격 제어 서버를 사용해 **Survivalcraft API 1.9.3.1**에 대해 JavaScript를 실행하는 단일 HTML 파일 도구입니다.

글자 수 제한이 없는 실제 텍스트 편집기, 유용한 명령의 프리셋 드롭다운, 원탭 텔레포터를 제공합니다.

---

## 기능

- **JS 실행기 탭** — 게임 API가 허용하는 JavaScript를 작성하거나 붙여넣은 다음 전송하고 결과를 확인합니다.
- **프리셋 드롭다운** — 알기 쉬운 설명이 붙은 미리 로드된 명령 중에서 선택합니다. 선택하면 코드가 편집기에 로드되어 검토, 수정 또는 바로 실행할 수 있습니다.
- **텔레포터 탭** — X/Y/Z를 입력하고 텔레포트합니다. 미세 조정 버튼, "내 위치는?", 스폰 저장/이동도 포함됩니다.

---

## 요구 사항

- **Survivalcraft API 1.9.3.1**(Android). Play 스토어 기본 버전에는 JavaScript 원격 제어가 포함되어 있지 않습니다.
- **Chrome for Android**(또는 Chromium 기반 브라우저).
- 게임이 **세계를 로드한 상태로 열려 있어야** 하며 **원격 제어가 켜져 있어야** 합니다.
- 스크립트를 실행하는 동안 게임은 **포그라운드** 또는 **분할 화면**에 있어야 합니다. Android가 게임을 일시 중지하면 서버가 종료됩니다.

---

## 플랫폼 지원

이 도구는 **Android**에서 제작 및 테스트되었습니다. 기본 프로토콜(`127.0.0.1:7765`로의 HTTP POST)은 Android 전용이 아니지만, `127.0.0.1`은 브라우저가 실행 중인 기기의 루프백 주소를 가리키므로 게임과 브라우저는 **같은 기기**에서 실행되어야 합니다.

| 플랫폼 | 상태 | 비고 |
|---|---|---|
| Android | ✅ 테스트 완료 | 게임이 포그라운드에 있도록 분할 화면 필요 |
| Windows / Linux | ✅ 작동할 것임 | 같은 PC에서 게임과 Chrome 실행, 분할 화면 불필요 |
| macOS | ⚠️ 미검증 | API 빌드와 JS 원격 제어가 있을 경우에만 |
| iOS | ⚠️ 미검증 | JS 원격은 원래 iOS 우회책으로 추가되었으므로 작동할 수도 있음 |
| 휴대폰 브라우저 → PC 게임 | ❌ 작동 안 함 | `127.0.0.1`은 기기를 넘을 수 없음 |

### PC를 사용하는 경우

이 HTML 파일이 필요 없습니다. `curl`, Python의 `requests` 또는 아무 HTTP 클라이언트로 게임 서버에 직접 POST하면 됩니다.

```
curl -X POST http://127.0.0.1:7765/ -H "password: YOUR_PASSWORD" -d 'findSubsystem("Players").PlayersData.Count'
```

이 HTML 도구가 존재하는 주된 이유는 휴대폰에 쓸 만한 HTTP 클라이언트가 거의 설치되어 있지 않고, 모바일 터미널에 긴 JavaScript를 입력하는 것이 고통스럽기 때문입니다.

## 설정

### 1. 게임에서 원격 제어 켜기

- Survivalcraft API를 실행하고 세계를 로드합니다.
- 게임 내 메뉴를 엽니다.
- **JavaScript Remote Control**을 찾습니다.
- **켜기**로 전환합니다.
- 화면에 표시되는 **포트**(기본값 `7765`)와 **비밀번호**를 기록합니다.

### 2. 비루팅 기기에서 Chrome 명령줄 활성화

- Chrome → `chrome://flags`를 엽니다.
- **"Enable command line on non-rooted devices"**를 검색합니다.
- **Enabled**로 설정하고 Chrome을 다시 시작합니다.

### 3. Chrome 명령줄 파일 만들기

이 파일은 Chrome의 웹 보안을 비활성화하여 게임의 로컬 서버와 통신할 수 있게 합니다.

휴대폰에서 셸 명령을 실행할 방법이 필요합니다. **Shizuku**와 함께 쓰는 **aShell You**가 됩니다. **PC에서 ADB**도 됩니다.

다음 명령을 그대로 실행하세요:

```bash
echo "chrome --disable-web-security --user-data-dir=/data/user/0/com.android.chrome/app_chrome/Default" > /data/local/tmp/chrome-command-line
```

4. Chrome 강제 종료 후 다시 열기

설정 → 앱 → Chrome → 강제 중지. 그런 다음 Chrome을 다시 엽니다.

5. 플래그가 활성화되었는지 확인

Chrome에서 chrome://version을 방문하세요. Command Line 항목 아래에 다음이 보여야 합니다:

```
chrome --disable-web-security --user-data-dir=...
```

보인다면 준비 완료입니다.

6. Chrome과 게임을 분할 화면으로

앱을 전환하면 Android가 게임을 일시 중지시켜 원격 제어 서버가 종료됩니다. Chrome과 Survivalcraft를 분할 화면에 두어 둘 다 계속 실행되게 하세요.

---

도구 사용법

1. Chrome에서 survivalcraft-js-runner.html을 엽니다(다운로드 폴더 또는 저장한 위치에서).
2. 상단의 비밀번호와 포트가 게임에 표시된 것과 일치하는지 확인합니다.
3. JS 실행기 탭 — 드롭다운에서 프리셋을 고르거나 직접 코드를 입력한 뒤 Run을 누릅니다.
4. 텔레포터 탭 — X/Y/Z를 입력하고 Teleport를 누릅니다.

출력 상자에는 게임이 반환한 원시 JSON이 표시됩니다.

---

## 작동 방식

전체 설정은 휴대폰에서 로컬로 실행됩니다. 인터넷으로 나가는 것은 없습니다.

```
[휴대폰]
     │
     ├── Survivalcraft API (분할 화면의 한쪽에서 실행)
     │        └── 127.0.0.1:7765의 내장 HTTP 서버
     │             ("password" 헤더와 JS 본문이 포함된 POST 허용)
     │
     └── Chrome (분할 화면의 다른 쪽에서 실행)
              └── teleport.html (다운로드 폴더에서 로드)
                   └── fetch()가 127.0.0.1:7765로 HTTP POST 전송
                        └── 서버가 JS를 실행하고 JSON 결과 반환
```

HTML 페이지에서 **Run** 또는 **Teleport**를 누를 때마다:

1. Chrome은 비밀번호를 헤더에, JavaScript를 본문에 담아 HTTP POST를 만듭니다.
2. 요청은 `127.0.0.1:7765` — 즉 자신의 휴대폰 루프백 주소로 갑니다. 기기를 떠나지 않습니다.
3. Survivalcraft의 내장 서버가 이를 받아 비밀번호를 확인하고 JS를 Jint 엔진에 넘깁니다.
4. Jint는 게임 프로세스 안에서 C# 객체(`findSubsystem`, `ComponentBody` 등)에 접근하여 코드를 실행합니다.
5. 서버가 JSON 응답을 보내고 Chrome이 출력 상자에 표시합니다.

`--disable-web-security` 플래그는 Chrome이 보통 `file://` 또는 `content://` 페이지가 `http://127.0.0.1`을 호출하는 것을 거부하기 때문에 존재합니다. 플래그를 제거하면 그 차단이 다시 활성화되어, 다시 적용할 때까지 도구가 작동하지 않습니다.

알려진 제한 사항

이것들은 도구가 아닌 게임 API의 한계입니다:

· 일회성 스크립트만 지원. 이 빌드에서는 프레임 핸들러가 등록되지만 절대 실행되지 않으므로 백그라운드에서 자동으로 실행되는 것은 없습니다. 모든 작업은 Run을 눌러야 합니다.
· 일부 속성 읽기는 게임을 교착 상태에 빠뜨립니다. 특정 속성(체력, 시간, 하늘, 날씨, 일부 컴포넌트)을 읽으면 스크립트 엔진이 멈출 수 있습니다. 이 경우 게임을 강제 종료해야 복구됩니다.
· 큐나 타임아웃 없음. 스크립트가 멈추면 게임을 재시작할 때까지 서버가 "There is already a script running"을 반환합니다.
· 렌더링 없음. JavaScript로 HUD, 오버레이, 미니맵을 그릴 수 없습니다.
· 사용자 지정 키 바인딩 없음. 위와 같은 이유 — 핸들러가 실행되지 않습니다.

안정적으로 작동하는 것

· 플레이어 위치, 속도, 이름, 레벨, 체력, 스폰 지점 읽기
· 위치(텔레포트)와 속도(발사, 정지) 쓰기
· 스폰 지점 쓰기
· 서브시스템, 엔티티, API 객체 열거

---

문제 해결

"Invalid password"
헤더 이름은 대소문자를 구분합니다. password, Password, PASSWORD를 시도해 보세요. 게임 메뉴에 표시된 비밀번호가 바뀌지 않았는지도 확인하세요.

"There is already a script running"
이전 스크립트가 엔진을 교착 상태에 빠뜨렸습니다. 게임을 강제 종료하고 다시 열고, 세계를 로드하고, 원격 제어를 다시 켜세요. 페이지 새로고침이나 Chrome 재시작으로는 해결되지 않습니다.

출력 상자에 "FETCH FAILED"
보통 게임이 분할 화면이 아니어서 일시 중지되었음을 의미합니다. Chrome과 다시 분할 화면에 두고 시도하세요.

텔레포트는 되지만 속성 읽기가 멈춤
일부 속성은 읽기에 안전하지 않습니다. Position, Velocity, Name, Level, Health만 사용하세요. 그것들 중 하나라도 멈추면 스크립트에서 빼세요.

게임이 완전히 멈춤
설정 → 앱 → Survivalcraft → 강제 중지.

---

공유

이 도구는 GitHub Pages(또는 어떤 HTTPS 사이트)에도 호스팅할 수 없습니다. 브라우저는 HTTPS 페이지가 http://127.0.0.1을 호출하는 것을 차단하며, 그렇지 않더라도 CORS가 막습니다.

의도된 사용법:

1. HTML 파일을 다운로드합니다.
2. 웹 보안이 비활성화된 Chrome에서 로컬로 엽니다.
3. 같은 기기에서 실행 중인 게임에 사용합니다. (분할 화면이어야 함)

그래서 호스팅 페이지가 아닌 단일 파일로 배포됩니다.

---
