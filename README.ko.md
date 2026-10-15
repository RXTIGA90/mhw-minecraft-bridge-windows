# Minecraft × Monster Hunter: World — Windows 패스스루 모드

[English](README.md) | **한국어**

> ⚠️ **개발 중인 테스트 버전입니다 (0.1.0-dev).** 다른 PC에서 시험해 보기 위한 공개이며, 문제가 생길 수 있습니다.
> - **설치 전에 몬헌 세이브를 꼭 백업하세요.** (Steam `userdata\<계정>\582010\remote` 폴더 전체)
> - 오프라인 전용입니다. 온라인·구조 신호에서 쓰지 마세요.
> - 개발자 PC 한 대(Windows 11, 4K 모니터)에서만 검증했습니다. 다른 환경에서는 동작하지 않을 수 있습니다.
> - 문제가 생기면 `Uninstall.ps1`로 원래대로 되돌리고, 로그(`%TEMP%\mhwmc\mhw-bridge.log`, 마크 `logs\latest.log`)와 함께 GitHub Issues에 알려 주세요.

## 빠른 시작 (요약)
1. **[Releases](../../releases)** 에서 `mhw-minecraft-bridge-windows-0.1.0-dev.zip`을 받아 압축을 풉니다.
2. 몬헌 세이브 폴더를 백업합니다.
3. 준비물 설치: Minecraft Java 1.21.1 + Fabric Loader + Fabric API, (키보드로 몬헌 상호작용을 하려면) ViGEmBus 드라이버 + Python + `pip install vgamepad`.
4. 몬헌을 끈 상태에서 압축 푼 폴더에서 `powershell -ExecutionPolicy Bypass -File Install.ps1` 실행 (4K 전체 화면은 끝에 `-Borderless`).
5. `bin\minecraft-mod\` 의 jar를 Fabric 프로필 `mods` 폴더에 넣습니다.
6. Steam을 **오프라인 모드**로 → 몬헌 실행 → `Start-Session.bat` 실행 → 오프라인으로 거점 입장 → Minecraft(Fabric) 실행.
7. 끝낼 때는 거점에서 마크의 **F12** (두 게임 저장 후 종료). 제거는 `Uninstall.ps1`.

자세한 내용은 아래에 있습니다.

실제 Minecraft 1.21.1(Fabric)을 Monster Hunter: World(PC, 오프라인) 안에서 플레이하는 모드입니다.
Minecraft 화면이 몬헌 화면 안에 합성되고(몬헌 지형·캐릭터 뒤의 블록은 가려짐), 스티브가 몬헌 지형 위를
걷고 건축하며 몬헌 몬스터와 싸웁니다.

원본: [justbustin/minecraft-crossover-bridge](https://github.com/justbustin/minecraft-crossover-bridge)
(macOS/CrossOver용, MIT). 이 패키지는 그 코드를 Windows에서 직접 실행하도록 고치고 기능을 더한 버전입니다.

## 요구 사항
- Windows 10/11, Steam판 Monster Hunter: World **15.23.00 (build 421810)** + Iceborne
- Minecraft Java 1.21.1, [Fabric Loader](https://fabricmc.net/use/) 0.16 이상, [Fabric API](https://modrinth.com/mod/fabric-api) 1.21.1용
- 몬헌 창 모드(기본 2560x1440) 또는 테두리 없는 전체 화면(4K 포함). 2560x1600보다 큰 화면은 마크 화면을 줄여 보낸 뒤 몬헌이 늘려 그립니다
- 게임 안 문구는 마인크래프트 언어 설정을 따릅니다(영어·한국어 포함)
- 선택: 컨트롤러가 없을 때 R/V/M 키(아래)를 쓰려면 [ViGEmBus](https://github.com/nefarius/ViGEmBus/releases) 드라이버와
  Python + `pip install vgamepad`

## 설치
1. 몬헌을 끄고, 이 폴더에서 실행:
   ```
   powershell -ExecutionPolicy Bypass -File Install.ps1
   ```
   몬헌 폴더를 못 찾으면 `-GameDir "D:\...\Monster Hunter World"`, 해상도는 `-Resolution 1920x1080`처럼 지정.
   4K 모니터 전체 화면으로 하려면 `-Borderless` (바탕화면 해상도의 테두리 없는 창).
   설치기는 `dinput8.dll`, `mhwbridge\`를 복사하고, `graphics_option.ini`를 백업한 뒤 DirectX 11 · 창 모드 · 지정 해상도로 바꿉니다.
   다른 `dinput8.dll` 모드 로더(예: Stracker's Loader)가 있으면 설치를 멈춥니다.
2. Minecraft 쪽: Fabric 프로필의 `mods` 폴더에 `bin\minecraft-mod\mhw-bridge-0.1.0-windows.jar`와 Fabric API를 넣습니다.

## 플레이
0. Steam을 **오프라인 모드**로 둡니다(Steam 메뉴 → 오프라인으로 전환). 온라인이면 캐릭터 선택 뒤 "집회구역 이동" 메뉴가 뜹니다.
1. 몬헌을 실행하고 `Start-Session.bat`을 켭니다(가상 패드 + F12 종료 대기, 창은 열어 두기).
2. 몬헌에서 **오프라인**으로 거점이나 필드에 들어갑니다. (온라인/구조 신호 사용 금지: 몬스터 체력을 바꾸는 모드입니다)
3. Minecraft(Fabric 1.21.1)를 실행하면 자동으로 "MHW Bridge" 월드가 열리고, 마크 창이 몬헌 창 위에 붙어 조작을 받습니다.
4. 필드로 가기: M(지도) → F(전역 맵) → Q/E(하위·상위) → WASD(지역) → Enter.
5. 끝낼 때는 마크에서 **F12**(또는 `Save-And-Quit.bat`): 마크 월드 저장 → 몬헌 시스템 메뉴로 저장 → 두 게임 종료.
   모드는 게임 폴더에 그대로 남아 있어서 다음에는 1번부터 다시 하면 됩니다. 세이브를 되돌리는 동작은 없습니다.
   - 몬헌은 거점에서만 수동 저장이 됩니다. 필드(탐색·퀘스트)나 로딩 중에 F12를 누르면 메뉴 저장을 건너뛰고 종료하며, 마지막 자동 저장(필드 출발·귀환 시점)까지 남습니다. 진행을 남기려면 거점에서 F12를 누르세요.
   - 강제 종료나 PC 꺼짐 시: 마크는 마지막 자동 저장(약 5분 간격), 몬헌은 마지막 자동/수동 저장까지 남습니다.

| 키 | 동작 |
|---|---|
| R | 헌터 상호작용(대화·채집·해체, 패드 A). 마크 창이 앞에 있어야 합니다 |
| V | 몬헌 메뉴 모드 켜기/끄기: WASD 커서, 방향키 선택, Enter 결정, Esc 취소, Q/E 탭, F=X, C=Y, Tab 지도, P 시작메뉴 |
| M | 몬헌 지도 열기 + 메뉴 모드 |
| F6 | 몬헌 안 합성 ⇄ 별도 창 |
| F7 | 카메라: 마크가 몬헌 카메라 조종 ⇄ 마크가 몬헌 카메라를 따라감 |
| F8 | 키보드·마우스를 헌터에게 넘김(몬헌 창에서 F8로 복귀) |
| F9 | 디버그 표시(상태, 판정 상자, 체력) |
| F10 | 헌터 대역 켜기/끄기(스티브 발밑에 숨은 헌터가 몬헌 공격을 대신 받음) |
| F12 | 두 게임 저장 후 종료(`Start-Session.bat`이 켜져 있을 때) |
| F4 | 설정: 낭떠러지 보호, 화살 보정, 폭발 보정, 몹 vs 몬스터, 폭발 구덩이, [실험] 몬스터가 건축물에 막힘(기본 끔) (각각 켜기/끄기, `config\mhwbridge.properties`에 저장) |

메뉴 모드(V/M)는 V·M을 다시 누르거나 **마우스 클릭**으로 끝납니다. 창을 바꾸면(Alt+Tab) 누르던 키는 자동으로 풀립니다.

## 저장 위치
- 마크 월드: Fabric 프로필의 `saves\mhw-bridge` (구덩이 목록 `mhwbridge-craters.txt`, 지역 기준점 포함)
- 몬헌 세이브: Steam `userdata\<계정>\582010\remote` (Steam 클라우드 동기화)
- 설치 전 그래픽 설정 백업: 몬헌 폴더 `mhwbridge-backup\`
- 로그: `%TEMP%\mhwmc\mhw-bridge.log`(몬헌 쪽), `exit.log`(종료 과정), 마크 `logs\latest.log`

그 외는 일반 마인크래프트 조작입니다. 지역 이동은 V/M 메뉴로 몬헌 지도에서 하면 됩니다.

## 기능
- 몬헌 지형 충돌(보이지 않는 블록으로 근사), 벽·8칸 넘는 낭떠러지 보호
- 블록 설치, TNT·크리퍼 폭발 시 몬헌 땅에 구덩이(흙·돌 그릇 + 몬헌 땅을 화면에서 지움)
- 검·활·TNT·크리퍼로 몬헌 몬스터 피해, 몬스터 공격이 스티브 피해로 전달
- 마크 적대 몹이 근처 몬헌 몬스터를 공격. 몬헌 몬스터는 돌진·몸통박치기(빠르게 움직일 때)와 투사체로 마크 몹에 피해를 줌. 그냥 옆에 서 있으면 피해 없음. 제자리 물기·꼬리 휘두르기는 아직 몹에 전달되지 않음

## 제거
```
powershell -ExecutionPolicy Bypass -File Uninstall.ps1
```
게임 폴더의 모드 파일을 지우고 그래픽 설정을 되돌립니다. Minecraft `mods`의 jar는 직접 지우세요.

## 알려진 한계
- 몬헌 몬스터와 헌터는 구덩이를 인식하지 못하고 원래 땅 위를 걷습니다(엔진 한계).
- 지형은 블록 단위 근사라 경사·턱이 1블록 단위로 맞춰집니다.
- 4K(3840x2160, `-Borderless`)에서는 마크 화면이 2560x1440으로 전송된 뒤 늘어나 그려집니다(몬헌 자체는 4K 그대로). 측정(셀리아나): 4K 약 85-90fps, 1440p 창 약 120fps, 합성 2.2-2.4ms/프레임.
- [실험, 불안정] 몬스터가 건축물에 막힘(기본 끔): 몬헌의 충돌·길찾기는 마크 블록을 몰라서, 블록에 파고드는 순간 몬스터를 뒤로 되돌리는 방식입니다. 시험에서 벽에 잠깐씩 막히지만 결국 벽을 뚫고 나가는 경우가 많았습니다. 지역 이동 직후 5초와 마크 재시작 중에는 동작하지 않습니다.
- 몬헌 AI는 마크 몹을 보지 못합니다(몸 접촉 피해로 대신함).
- **세이브는 미리 백업하세요.** 모드가 몬스터 체력 등 게임 상태를 바꿉니다.

## 폴더 구성
- `bin\` 실행 파일(몬헌용 DLL, 마크 모드 jar)
- `source\` 전체 소스(mhw-bridge: C++/MinHook, mc-bridge: Fabric 모드)
- `tools\` 가상 패드, 세션/종료 스크립트, 코어 빌드 스크립트
- `docs\CHANGES.md` 원본 대비 변경 내역


## 라이선스
MIT (`LICENSE` 참고). 원작 © justbustin, Windows 이식 및 추가 기능 © RXTIGA90. 서드파티 고지는 `THIRD_PARTY_NOTICES.md`.
