## 26-1 데이터마이닝 프로젝트
---
## 프로젝트 초기 설정
 이 프로젝트는 `uv`를 사용하여 의존성을 관리합니다.

### uv 설치
macOS/Linux를 사용하는 경우
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```
또는 Homebrew를 사용하는 경우
```bash
brew install uv
```

Windows를 사용하는 경우, 아래의 명령어 중 실행되는 명령어로 설치해주세요.
```powershell
# powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# winget
winget install --id=astral-sh.uv -e
```

설치 후, 터미널/cmd를 재시작하여 설치가 성공했는지 확인합니다.
```bash
uv --version
```

### 파이썬 버전 설치
파이썬은 3.10 latest 버전을 사용합니다.
```bash
uv python install 3.10
```
---
### Jupyter Notebook(.ipynb) 실행
```bash
uv run jupyter execute {FILE_NAME}.ipynb
```
