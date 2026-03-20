# 🐙 Git 업무 워크플로우 가이드

> Git 초보자를 위한 상황별 명령어 흐름도 + 비상 매뉴얼

---

## 📌 목차

1. [시작하기 — init vs clone](#1-시작하기--init-vs-clone)
2. [서브모듈 — Submodule](#2-서브모듈--submodule)
3. [환경 설정](#3-환경-설정)
4. [기본 작업 흐름 — 스테이징 & 커밋](#4-기본-작업-흐름--스테이징--커밋)
5. [브랜치 작업 흐름](#5-브랜치-작업-흐름)
6. [원격 저장소 동기화](#6-원격-저장소-동기화)
7. [임시 저장 — Stash](#7-임시-저장--stash)
8. [충돌(Conflict) 해결](#8-충돌conflict-해결)
9. [로그 & 검색](#9-로그--검색)
10. [태그 & 릴리즈](#10-태그--릴리즈)
11. [Pull Request 흐름](#11-pull-request-흐름)
12. [🚨 비상 매뉴얼](#12--비상-매뉴얼)

---

## 1. 시작하기 — init vs clone

> 모든 Git 작업은 여기서 시작합니다

```mermaid
flowchart TB
    A([🚀 Git 시작!]) --> B{상황은?}

    B -->|내 컴퓨터에서\n새 프로젝트 시작| C["mkdir 프로젝트명\ncd 프로젝트명\n폴더 생성 & 이동"]
    B -->|GitHub에 있는\n프로젝트 가져오기| G

    C --> D["git init\nGit 저장소 초기화"]
    D --> E["GitHub에서 빈 저장소 생성\n(New Repository)"]
    E --> F["git remote add origin URL\nGitHub 저장소 연결"]
    F --> F2["git branch -M main\n기본 브랜치를 main으로 설정"]
    F2 --> Z

    G["GitHub 저장소 페이지 접속\nCode 버튼 → URL 복사"] --> H{복사 방식}
    H -->|HTTPS| I["git clone https://github.com/..."]
    H -->|SSH 🔑| J["git clone git@github.com:..."]
    H -->|얕은 복사\n히스토리 불필요| J2["git clone --depth 1 URL\n최신 커밋만 복사 (속도 빠름)"]
    I --> K["cd 프로젝트명\n폴더로 이동"]
    J --> K
    J2 --> K
    K --> Z(["✅ 준비 완료!\n2번 환경 설정으로 →"])

    style A fill:#4A90D9,color:#fff
    style Z fill:#27AE60,color:#fff
    style D fill:#8E44AD,color:#fff
    style F fill:#F39C12,color:#fff
    style I fill:#F39C12,color:#fff
    style J fill:#F39C12,color:#fff
```

### 💡 init vs clone 한눈에 비교

| | `git init` | `git clone` |
|---|---|---|
| **언제?** | 새 프로젝트를 처음 시작할 때 | 이미 있는 원격 저장소를 받아올 때 |
| **결과** | 빈 `.git` 폴더 생성 | 코드 + 전체 히스토리 복사 |
| **추가 작업** | `git remote add`로 GitHub 연결 필요 | 자동으로 origin 연결됨 |

### 🚨 init/clone 비상 매뉴얼

```bash
# ❌ 잘못된 폴더에서 git init 해버렸다!
rm -rf .git                              # .git 폴더 삭제로 Git 제거 ⚠️ 복구 불가

# ❌ git remote를 잘못된 URL로 연결했다!
git remote -v                            # 현재 연결된 URL 확인
git remote set-url origin 새URL         # URL 수정

# ❌ clone 했는데 특정 브랜치가 없다!
git branch -r                            # 원격 브랜치 목록 확인
git checkout -b 브랜치명 origin/브랜치명  # 원격 브랜치를 로컬로 가져오기
```

---

## 2. 서브모듈 — Submodule

> 하나의 Git 저장소 안에 다른 Git 저장소를 포함시키는 기능

### 💡 서브모듈이 필요한 대표 상황

| 상황 | 예시 |
|------|------|
| **공통 라이브러리 공유** | 여러 프로젝트에서 쓰는 UI 컴포넌트, 유틸 함수 |
| **외부 의존성 직접 관리** | npm/pip 대신 특정 버전의 오픈소스를 소스째로 포함 |
| **모노레포 대신 멀티레포** | 백엔드·프론트엔드를 각자 저장소로 관리하되 하나로 묶기 |
| **테마 / 플러그인 분리** | Hugo 테마, Vim 플러그인처럼 독립 배포되는 모듈 |
| **설정 파일 공유** | dotfiles, ESLint 규칙 등 팀 공통 설정 저장소 |

---

### 2-1. 서브모듈이 있는 프로젝트 clone

```mermaid
flowchart TB
    A(["📦 서브모듈 포함 프로젝트\nclone 시작"]) --> B{clone 방식}

    B -->|한 번에 다 받기\n권장 ✅| C["git clone --recurse-submodules URL\n메인 + 모든 서브모듈 동시 clone"]

    B -->|이미 clone 했는데\n서브모듈 폴더가 비어있다| D["git submodule init\n서브모듈 등록\n.gitmodules 기반"]
    D --> E["git submodule update\n서브모듈 코드 실제로 받기"]

    B -->|중첩 서브모듈까지\n전부 받기| F["git clone --recurse-submodules\n--remote-submodules URL"]

    C --> G(["✅ 서브모듈 포함\n전체 코드 준비 완료!"])
    E --> G
    F --> G

    style A fill:#4A90D9,color:#fff
    style G fill:#27AE60,color:#fff
    style C fill:#F39C12,color:#fff
    style D fill:#8E44AD,color:#fff
```

---

### 2-2. 기존 프로젝트에 서브모듈 추가

```mermaid
flowchart TB
    A(["🔧 기존 프로젝트에\n서브모듈 추가"]) --> B["git submodule add URL 경로\n예: git submodule add\nhttps://github.com/org/lib.git libs/lib"]

    B --> C["자동으로 생성되는 파일 확인\n① .gitmodules  — 서브모듈 목록 설정\n② libs/lib/     — 서브모듈 코드"]

    C --> D["git status\n변경사항 확인\n.gitmodules와 libs/lib 표시"]

    D --> E["git add .gitmodules libs/lib\ngit commit -m 'chore: lib 서브모듈 추가'"]

    E --> F["git push origin main\n원격에 반영"]

    F --> G(["✅ 서브모듈 추가 완료!\n팀원들은 git submodule update --init 실행"])

    style A fill:#4A90D9,color:#fff
    style G fill:#27AE60,color:#fff
    style B fill:#F39C12,color:#fff
    style E fill:#8E44AD,color:#fff
```

---

### 2-3. 서브모듈 업데이트 & 일상 작업 흐름

```mermaid
flowchart TB
    A([🔄 서브모듈 작업]) --> B{어떤 상황?}

    B -->|서브모듈을\n최신으로 업데이트| C["git submodule update --remote\n서브모듈 원격의 최신 커밋으로 갱신"]

    B -->|메인 프로젝트 pull 후\n서브모듈도 맞춰야 함| D["git pull origin main\ngit submodule update --init --recursive\n서브모듈 버전 동기화"]

    B -->|서브모듈 안에서\n직접 코드 수정| E["cd libs/lib\n서브모듈 폴더로 이동"]
    E --> F["코드 수정 후\ngit add . & git commit\n서브모듈 자체 저장소에 커밋"]
    F --> G["git push origin main\n서브모듈 원격에 push"]
    G --> H["cd ..  부모 프로젝트로 이동\ngit add libs/lib\ngit commit -m 'chore: lib 서브모듈 버전 업데이트'\n부모 프로젝트에도 커밋 필요!"]

    B -->|모든 서브모듈\n한꺼번에 pull| I["git submodule foreach git pull origin main\n각 서브모듈에 명령어 일괄 실행"]

    C --> J(["✅ 완료"])
    D --> J
    H --> J
    I --> J

    style A fill:#4A90D9,color:#fff
    style J fill:#27AE60,color:#fff
    style C fill:#F39C12,color:#fff
    style E fill:#8E44AD,color:#fff
    style H fill:#E74C3C,color:#fff
```

### ⚠️ 서브모듈의 핵심 개념

```
부모 저장소는 서브모듈의 "특정 커밋 해시"만 기록합니다.
서브모듈 코드 자체를 저장하는 게 아닙니다!

부모 저장소
└── .gitmodules          ← 서브모듈 URL, 경로 설정
└── libs/lib/            ← 서브모듈 폴더 (특정 커밋 해시 포인터)
    └── .git/            ← 서브모듈 자체의 Git 저장소

⚠️ 서브모듈 코드를 수정하면 반드시:
  1. 서브모듈 자체 저장소에 commit + push
  2. 부모 저장소에도 commit + push (버전 포인터 업데이트)
```

### 💡 서브모듈 주요 명령어 요약

```bash
# ➕ 추가
git submodule add URL                        # 서브모듈 추가 (루트에)
git submodule add URL 경로/이름             # 경로 지정해서 추가

# 📥 받기
git clone --recurse-submodules URL           # clone 시 서브모듈 포함
git submodule init                           # 서브모듈 등록 (.gitmodules 기반)
git submodule update                         # 서브모듈 코드 다운로드
git submodule update --init --recursive      # 중첩 서브모듈까지 전부

# 🔄 업데이트
git submodule update --remote               # 서브모듈 최신 커밋으로 갱신
git submodule foreach git pull origin main  # 전체 서브모듈 일괄 pull

# 📋 확인
git submodule status                         # 서브모듈 상태 확인
git submodule                                # 간단 목록

# 🗑️ 제거 (4단계 필요!)
git submodule deinit -f 경로               # 서브모듈 등록 해제
git rm -f 경로                              # 서브모듈 폴더 제거
rm -rf .git/modules/경로                   # .git 내부 캐시 삭제
git commit -m "chore: 서브모듈 제거"       # 변경사항 커밋
```

### 🚨 서브모듈 비상 매뉴얼

```bash
# ❌ clone 후 서브모듈 폴더가 텅 비어있다!
git submodule update --init --recursive    # 서브모듈 초기화 + 코드 받기

# ❌ 팀원이 서브모듈을 추가했는데 나한테는 안 보인다!
git pull origin main
git submodule update --init --recursive   # 새로 추가된 서브모듈 포함해서 초기화

# ❌ 서브모듈이 "detached HEAD" 상태다!
# 서브모듈은 기본적으로 detached HEAD가 정상입니다
# 직접 수정하려면:
cd 서브모듈경로
git checkout main                         # 브랜치로 이동 후 작업

# ❌ 서브모듈 URL이 바뀌었다!
# .gitmodules 파일에서 URL 직접 수정 후:
git submodule sync                        # .gitmodules → .git/config 동기화
git submodule update --init              # 새 URL로 다시 받기

# ❌ 서브모듈 변경사항을 push 했는데 부모 프로젝트가 없는 커밋을 가리킨다!
# (서브모듈 push 전에 부모를 push해서 생기는 문제 방지)
git push --recurse-submodules=check      # 서브모듈 push 안 됐으면 경고
git push --recurse-submodules=on-demand  # 서브모듈 자동으로 먼저 push ✅
```

---

## 3. 환경 설정

> 처음 한 번만 설정하면 됩니다

```mermaid
flowchart TB
    A([⚙️ 환경 설정]) --> B["git config --global user.name '이름'\ngit config --global user.email '이메일'\n커밋 작성자 정보 등록"]

    B --> C[".gitignore 파일 생성\n추적하지 않을 파일 패턴 지정"]
    C --> D["git config --global alias.명령어\n자주 쓰는 명령어 단축키 등록"]
    D --> E["git config --list\n설정 내용 확인"]
    E --> F(["✅ 설정 완료!\n3번 작업 흐름으로 →"])

    style A fill:#4A90D9,color:#fff
    style F fill:#27AE60,color:#fff
    style B fill:#8E44AD,color:#fff
    style C fill:#F39C12,color:#fff
```

### 💡 주요 설정 명령어

```bash
# 👤 사용자 정보 (커밋에 표시됨 — 필수!)
git config --global user.name "홍길동"
git config --global user.email "gildong@email.com"
git config --list                            # 전체 설정 확인

# ⚡ 자주 쓰는 alias 단축키 등록
git config --global alias.st status         # git st → git status
git config --global alias.co checkout       # git co → git checkout
git config --global alias.lg "log --oneline --graph --all"  # git lg

# 🔧 기타 유용한 설정
git config --global core.editor "code --wait"  # VSCode를 기본 에디터로
git config --global pull.rebase false           # pull 시 merge 방식 사용
```

### 📄 .gitignore 주요 패턴

```gitignore
# 의존성 폴더
node_modules/
vendor/

# 환경 변수 / 비밀 파일 (⚠️ 절대 올리면 안 됨!)
.env
.env.local
*.pem

# 빌드 결과물
dist/
build/
*.log

# OS 파일
.DS_Store
Thumbs.db
```

> 💡 **[gitignore.io](https://gitignore.io)** 에서 언어/프레임워크별 .gitignore를 자동 생성할 수 있습니다.

### 🚨 설정 비상 매뉴얼

```bash
# ❌ .gitignore를 나중에 추가했는데 이미 추적 중인 파일이 있다!
git rm --cached 파일명               # 추적에서만 제거 (파일은 유지)
git rm --cached -r 폴더명/          # 폴더째로 추적 해제
git commit -m "chore: gitignore 적용"

# ❌ 민감한 파일(.env 등)을 실수로 push해버렸다!
# 1단계: 즉시 API키/비밀번호 재발급! (보안 조치 먼저)
# 2단계: BFG Repo Cleaner로 히스토리에서 완전 삭제
# 3단계: .gitignore에 추가 후 재발생 방지
```

---

## 4. 기본 작업 흐름 — 스테이징 & 커밋

> 파일을 수정하고 GitHub에 올리는 가장 기본적인 흐름

```mermaid
flowchart TB
    A([🖥️ 작업 시작]) --> B[파일 수정 / 생성]
    B --> C["git status\n현재 상태 확인 (습관처럼!)"]
    C --> D{어떤 파일을\n올릴까?}

    D -->|전체 파일| E["git add .\n모든 변경사항 추가"]
    D -->|특정 파일만| F["git add 파일명\n선택한 파일만 추가"]
    D -->|변경사항을\n부분적으로 선택| F2["git add -p\n청크 단위 대화형 선택"]

    E --> G["git status\n스테이지 상태 재확인 ✅"]
    F --> G
    F2 --> G

    G --> H{커밋 방식}
    H -->|메시지와 함께| I["git commit -m '메시지'"]
    H -->|add + commit 동시에\n이미 추적 중인 파일만| I2["git commit -am '메시지'"]
    H -->|에디터로 길게 작성| I3["git commit\n에디터 열림"]

    I --> J{원격에 올릴까?}
    I2 --> J
    I3 --> J

    J -->|Yes| K["git push origin 브랜치명\nGitHub에 업로드"]
    J -->|나중에 더 작업| B

    K --> L([🎉 완료!])

    style A fill:#4A90D9,color:#fff
    style L fill:#27AE60,color:#fff
    style E fill:#F39C12,color:#fff
    style F fill:#F39C12,color:#fff
    style F2 fill:#F39C12,color:#fff
    style I fill:#8E44AD,color:#fff
    style K fill:#E74C3C,color:#fff
```

### 💡 커밋 메시지 컨벤션

| 타입 | 의미 | 예시 |
|------|------|------|
| `feat:` | 새 기능 | `feat: 로그인 기능 추가` |
| `fix:` | 버그 수정 | `fix: 비밀번호 검증 오류 수정` |
| `docs:` | 문서 수정 | `docs: README 업데이트` |
| `style:` | 코드 포맷 변경 | `style: 들여쓰기 정리` |
| `refactor:` | 코드 리팩토링 | `refactor: 인증 로직 분리` |
| `chore:` | 설정, 빌드 등 기타 | `chore: 패키지 버전 업데이트` |

### 🚨 스테이징 & 커밋 비상 매뉴얼

```mermaid
flowchart TB
    A(["😱 커밋 실수!"]) --> B{어느 상태?}

    B -->|add 했는데\n빼고 싶다| C["git restore --staged 파일명\n스테이지에서 내리기\n작업 내용은 유지됨"]

    B -->|파일 수정을\n통째로 되돌리고 싶다| D["git restore 파일명\n⚠️ 수정 전으로 복구\n복구 불가!"]

    B -->|커밋 메시지를\n잘못 썼다| E{push 했나?}
    E -->|No| F["git commit --amend -m '새 메시지'"]
    E -->|Yes| G["git revert HEAD\n되돌리는 새 커밋 생성"]

    B -->|파일을 빠뜨리고\n커밋했다| H{push 했나?}
    H -->|No| I["git add 빠뜨린파일\ngit commit --amend --no-edit"]
    H -->|Yes| J["git add 빠뜨린파일\ngit commit -m 'fix: 누락 파일 추가'"]

    B -->|커밋 자체를\n되돌리고 싶다| K{어떻게?}
    K -->|staged 상태 유지| L["git reset --soft HEAD~1\n커밋 취소, 스테이지 유지"]
    K -->|unstaged 상태 유지\n기본값| M["git reset HEAD~1\n또는 --mixed\n커밋 취소, 파일 유지"]
    K -->|파일까지 완전 삭제| N["git reset --hard HEAD~1\n⚠️ 작업 내용 영구 삭제!"]
    K -->|이미 push 후\n안전하게| O["git revert HEAD\n협업 시 필수 사용!"]

    style A fill:#E74C3C,color:#fff
    style C fill:#27AE60,color:#fff
    style F fill:#27AE60,color:#fff
    style I fill:#27AE60,color:#fff
    style L fill:#27AE60,color:#fff
    style M fill:#27AE60,color:#fff
    style N fill:#E74C3C,color:#fff
    style O fill:#F39C12,color:#fff
```

### 💡 reset 세 가지 비교

| 옵션 | 커밋 취소 | 스테이지 | 파일 내용 | 언제? |
|------|-----------|----------|-----------|-------|
| `--soft` | ✅ | 유지 (staged) | 유지 | 메시지만 바꿔서 재커밋 |
| `--mixed` (기본) | ✅ | 초기화 | 유지 | 파일 다시 수정 후 재커밋 |
| `--hard` | ✅ | 초기화 | **삭제** ⚠️ | 완전히 없애고 싶을 때 |

---

## 5. 브랜치 작업 흐름

> 새로운 기능 개발 시 브랜치를 나눠서 작업하는 흐름

```mermaid
flowchart TB
    A([🌱 새 기능 개발 시작]) --> B["git checkout main\n메인 브랜치로 이동"]
    B --> C["git pull origin main\n최신 코드 받기"]
    C --> D{브랜치 생성 방법}
    D -->|기존 문법| D1["git checkout -b feature/기능명"]
    D -->|최신 문법| D2["git switch -c feature/기능명"]

    D1 --> E[코드 작업]
    D2 --> E
    E --> F["git add .\ngit commit -m 'feat: ...'"]
    F --> G{더 작업할 것이\n있나요?}

    G -->|Yes| E
    G -->|No| H["git push origin feature/기능명\n브랜치 GitHub에 올리기"]

    H --> I["GitHub에서 PR 생성"]
    I --> J{리뷰 결과}

    J -->|수정 요청| E
    J -->|승인 ✅| K{Merge 방식}

    K -->|모든 커밋 유지| K1["Merge commit"]
    K -->|커밋 하나로 합치기| K2["Squash and merge"]
    K -->|선형 히스토리| K3["Rebase and merge"]

    K1 --> L["git checkout main\ngit pull origin main\n로컬 동기화"]
    K2 --> L
    K3 --> L
    L --> M["git branch -d feature/기능명\n로컬 브랜치 삭제"]
    M --> N([✅ 기능 완성!])

    style A fill:#4A90D9,color:#fff
    style D1 fill:#F39C12,color:#fff
    style D2 fill:#F39C12,color:#fff
    style N fill:#27AE60,color:#fff
    style I fill:#8E44AD,color:#fff
```

### 💡 브랜치 명령어 요약

| 명령어 | 설명 |
|--------|------|
| `git branch` | 브랜치 목록 확인 |
| `git branch -v` | 브랜치 + 마지막 커밋 확인 |
| `git checkout -b 브랜치명` | 새 브랜치 만들고 이동 (기존) |
| `git switch -c 브랜치명` | 새 브랜치 만들고 이동 (최신) |
| `git branch -m 새이름` | 현재 브랜치 이름 변경 |
| `git branch -d 브랜치명` | 브랜치 삭제 (병합된 것만) |
| `git branch -D 브랜치명` | 브랜치 강제 삭제 |
| `git push origin --delete 브랜치명` | 원격 브랜치 삭제 |
| `git rebase -i HEAD~3` | 최근 3개 커밋 대화형 편집 |

### 🚨 브랜치 비상 매뉴얼

```bash
# ❌ 브랜치를 main에서 안 따고 엉뚱한 브랜치에서 땄다!
git log --oneline                          # 현재 커밋 해시 확인
git checkout main
git cherry-pick 커밋해시                    # 원하는 커밋만 가져오기
git checkout 잘못된브랜치
git reset HEAD~1                           # 잘못된 브랜치에서 커밋 제거

# ❌ 브랜치를 삭제했는데 복구하고 싶다!
git reflog                                 # 삭제된 브랜치의 마지막 커밋 해시 찾기
git checkout -b 브랜치명 커밋해시           # 해당 커밋으로 브랜치 복구

# ❌ rebase 도중 충돌이 나거나 취소하고 싶다!
git rebase --abort                         # rebase 완전 취소
git rebase --continue                      # 충돌 해결 후 rebase 재개
git rebase --skip                          # 현재 커밋 건너뛰기

# ❌ 공유 브랜치에서 rebase 했더니 팀원이 혼란스러워한다!
# 공유 브랜치(main, develop 등)에서는 rebase 절대 금지!
# 본인 feature 브랜치에서만 rebase 사용
```

---

## 6. 원격 저장소 동기화

> GitHub와 내 로컬 컴퓨터 코드를 맞추는 흐름

```mermaid
flowchart TB
    A([🔄 동기화 필요]) --> B{상황은?}

    B -->|원격 변경사항\n먼저 확인하고 싶다| C["git fetch origin\n변경사항만 가져오기\n자동 합치기 ❌"]
    B -->|바로 합쳐서\n최신 상태로| D{pull 방식}
    B -->|로컬을 올려야 함| P

    C --> E["git diff main origin/main\n변경사항 비교"]
    E --> F{합칠까?}
    F -->|Yes| G["git merge origin/main"]
    F -->|No| H["그냥 유지"]

    D -->|merge 방식\n기본| D1["git pull origin main"]
    D -->|rebase 방식\n히스토리 선형| D2["git pull --rebase origin main"]

    D1 --> I{충돌 발생?}
    D2 --> I
    G --> I

    I -->|No| J([✅ 동기화 완료!])
    I -->|Yes ⚠️| K["7번 충돌 해결 섹션 참고"]

    P["git push origin 브랜치명"] --> Q{push 거부?}
    Q -->|No| J
    Q -->|Yes — rejected| R["git pull --rebase origin main\n원격 변경사항 먼저 적용"]
    R --> P2["git push origin 브랜치명\n재push"]
    P2 --> J

    style A fill:#4A90D9,color:#fff
    style J fill:#27AE60,color:#fff
    style K fill:#E74C3C,color:#fff
    style D1 fill:#8E44AD,color:#fff
    style D2 fill:#8E44AD,color:#fff
    style R fill:#F39C12,color:#fff
```

### 💡 fetch vs pull 차이

| | `git fetch` | `git pull` |
|---|---|---|
| **하는 일** | 원격 변경사항 다운로드만 | 다운로드 + 자동 merge |
| **내 코드** | 건드리지 않음 ✅ | 자동으로 합쳐짐 |
| **안전도** | 더 안전 | 충돌 날 수 있음 |
| **추천 상황** | 변경사항 먼저 확인하고 싶을 때 | 빠르게 최신화할 때 |

### 🚨 동기화 비상 매뉴얼

```bash
# ❌ pull 했는데 이상해졌다! 되돌리고 싶다!
git reset --hard ORIG_HEAD              # pull 직전 상태로 복구 (가장 빠른 방법!)
# 또는
git reflog                              # pull 이전 커밋 해시 확인
git reset --hard HEAD@{1}              # 한 단계 이전으로

# ❌ 내 작업 중인데 pull해야 한다!
git stash                               # 내 변경사항 임시 저장 (6번 섹션 참고)
git pull origin main
git stash pop                           # 내 변경사항 복원

# ❌ 원격 브랜치가 삭제됐는데 로컬에 계속 뜬다!
git fetch --prune                       # 삭제된 원격 브랜치 정보 정리

# ❌ push --force를 써야 할 것 같다!
git push --force-with-lease 브랜치명   # --force보다 안전한 강제 push
# ⚠️ --force는 팀 협업 브랜치에서 절대 금지!
```

---

## 7. 임시 저장 — Stash

> 커밋하지 않은 작업을 잠깐 서랍에 넣어두는 기능

```mermaid
flowchart TB
    A(["💼 갑자기 다른 작업이 생겼다!\n(커밋하기엔 아직 미완성)"])
    A --> B["git stash\n현재 변경사항 임시 저장\n워킹 디렉토리 깨끗해짐"]

    B --> C{다른 작업}
    C --> D["다른 브랜치로 이동\n긴급 버그 수정\n등..."]
    D --> E["작업 완료"]
    E --> F["원래 브랜치로 복귀"]
    F --> G{stash 복원 방법}

    G -->|꺼내고 스택에서 제거| H["git stash pop\n가장 최근 stash 복원"]
    G -->|꺼내고 스택에 유지| I["git stash apply\n여러 번 적용 가능"]
    G -->|특정 stash 선택| J["git stash apply stash@{2}"]

    H --> K{충돌 발생?}
    I --> K
    J --> K
    K -->|No| L([✅ 작업 재개!])
    K -->|Yes| M["7번 충돌 해결 섹션 참고"]

    style A fill:#E74C3C,color:#fff
    style B fill:#F39C12,color:#fff
    style L fill:#27AE60,color:#fff
    style H fill:#27AE60,color:#fff
```

### 💡 stash 명령어 요약

```bash
# 📦 저장
git stash                             # 현재 변경사항 저장 (tracked 파일만)
git stash push -m "설명"             # 이름 붙여서 저장
git stash -u                          # untracked(새 파일)도 포함해서 저장

# 📋 확인
git stash list                        # 저장된 stash 목록 확인
# 출력 예: stash@{0}: WIP on main: 작업중

# 🔄 복원
git stash pop                         # 가장 최근 stash 복원 + 스택에서 제거
git stash apply                       # 복원하되 스택에 유지
git stash apply stash@{2}            # 특정 stash 복원

# 🗑️ 삭제
git stash drop stash@{0}             # 특정 stash 삭제
git stash clear                       # 전체 stash 삭제 ⚠️
```

---

## 8. 충돌(Conflict) 해결

> 같은 파일을 여러 명이 수정했을 때 충돌을 해결하는 흐름

```mermaid
flowchart TB
    A(["⚠️ CONFLICT 발생!"]) --> B["git status\n충돌 파일 목록 확인\n(both modified 표시)"]
    B --> C["충돌 파일을\n에디터로 열기"]

    C --> D["충돌 마커 확인\n<<<<<<< HEAD\n내 코드\n=======\n상대방 코드\n>>>>>>> branch"]

    D --> E{어떤 코드를\n남길까?}

    E -->|내 코드 유지| F["내 코드만 남기고\n마커 3줄 모두 삭제"]
    E -->|상대방 코드| G["상대방 코드만 남기고\n마커 3줄 모두 삭제"]
    E -->|둘 다 합치기| H["두 코드를 합친 후\n마커 3줄 모두 삭제"]

    F --> I["git add 충돌파일명\n해결 완료 표시"]
    G --> I
    H --> I

    I --> J{"충돌 파일이\n더 있나요?"}
    J -->|Yes| C
    J -->|No| K["git commit\n충돌 해결 커밋\n메시지 자동 생성됨"]
    K --> L(["✅ 충돌 해결 완료!"])

    style A fill:#E74C3C,color:#fff
    style L fill:#27AE60,color:#fff
    style D fill:#F39C12,color:#fff
    style K fill:#8E44AD,color:#fff
```

### 💡 충돌 마커 설명

```
<<<<<<< HEAD           ← 여기서부터 내 코드 (현재 브랜치)
console.log("Hello");
=======                ← 구분선
console.log("Hi");
>>>>>>> feature/abc    ← 여기까지 상대방 코드 (병합하려는 브랜치)
```

> ✅ 마커 3줄(`<<<<<<<`, `=======`, `>>>>>>>`)은 반드시 **모두** 삭제해야 합니다!

### 🚨 충돌 비상 매뉴얼

```bash
# ❌ 충돌 해결이 너무 복잡해서 merge 자체를 취소하고 싶다!
git merge --abort                     # merge 이전 상태로 완전히 복구

# ❌ rebase 중 충돌이 났다!
git rebase --abort                    # rebase 완전 취소
# 또는 충돌 해결 후
git add 충돌파일명
git rebase --continue                 # 충돌 해결하고 rebase 재개

# ❌ 충돌 해결하다 파일을 망쳤다!
git checkout HEAD -- 파일명           # 충돌 전 내 코드로 되돌리기
git checkout MERGE_HEAD -- 파일명    # 상대방 버전으로 되돌리기

# ❌ 어떤 커밋에서 충돌이 시작됐는지 모르겠다!
git log --merge                       # 충돌 관련 커밋 목록 확인
git diff ORIG_HEAD                    # merge 전후 변경사항 비교
```

---

## 9. 로그 & 검색

> 히스토리를 확인하고 문제의 원인을 추적하는 흐름

```mermaid
flowchart TB
    A([🔍 히스토리 & 검색]) --> B{목적은?}

    B -->|커밋 기록 확인| C["git log\n다양한 옵션으로 조회"]
    B -->|변경사항 비교| D["git diff\n파일 변경사항 확인"]
    B -->|누가 이 줄 건드렸어?| E["git blame 파일명\n라인별 작성자 확인"]
    B -->|버그 찾기| F["git bisect\n이진 탐색으로 버그 커밋 찾기"]
    B -->|삭제한 커밋 복구| G["git reflog\n모든 HEAD 이동 기록"]

    C --> C1["git log --oneline --graph --all\n브랜치 분기 시각화"]
    D --> D1["git diff --staged\n스테이지된 변경사항 확인"]
    F --> F1["git bisect start\ngit bisect bad   현재=버그있음\ngit bisect good 해시   정상시점\n→ Git이 자동으로 중간 커밋 체크아웃"]
    G --> G1["git reset --hard HEAD@{n}\nn번 이전 상태로 복구"]

    style A fill:#4A90D9,color:#fff
    style E fill:#F39C12,color:#fff
    style F fill:#E74C3C,color:#fff
    style G fill:#8E44AD,color:#fff
```

### 💡 로그 & 검색 명령어 모음

```bash
# 📋 git log — 커밋 히스토리 확인
git log                              # 전체 로그
git log --oneline                    # 한 줄 요약
git log --oneline --graph --all      # 브랜치 분기 그래프 ⭐
git log -p                           # 변경 내용(diff) 포함
git log --author="홍길동"            # 특정 작성자 필터
git log --since="2024-01-01"        # 날짜 이후 필터
git log -- 파일명                    # 특정 파일의 커밋만
git show 커밋해시:파일경로           # 특정 커밋 시점의 파일 내용

# 🔎 git diff — 변경사항 비교
git diff                             # unstaged 변경사항
git diff --staged                    # staged 변경사항
git diff 브랜치1 브랜치2            # 브랜치 간 비교

# 👤 git blame — 라인별 작성자 추적
git blame 파일명                     # 각 줄이 누가/언제 수정했는지

# 🐛 git bisect — 이진 탐색으로 버그 커밋 찾기
git bisect start
git bisect bad                       # 현재 커밋 = 버그 있음
git bisect good 정상커밋해시        # 버그 없던 시점 지정
# → Git이 중간 커밋을 자동으로 체크아웃 → 테스트 후 good/bad 입력 반복
git bisect reset                     # 탐색 종료

# 🆘 git reflog — 최후의 복구 수단
git reflog                           # 30일간 모든 HEAD 이동 기록
git reset --hard HEAD@{3}           # 3번 이전 상태로 복구
git checkout -b 복구브랜치 HEAD@{2} # 특정 시점으로 브랜치 생성
```

---

## 10. 태그 & 릴리즈

> 배포 시점에 버전 이름을 붙이는 흐름

```mermaid
flowchart TB
    A([🏷️ 버전 릴리즈]) --> B["배포할 커밋 확정\ngit log --oneline으로 확인"]
    B --> C{태그 종류}

    C -->|간단하게| D["git tag v1.0.0\n경량 태그 (Lightweight)"]
    C -->|설명 포함\n권장 ✅| E["git tag -a v1.0.0 -m '릴리즈 설명'\n주석 태그 (Annotated)"]

    D --> F["git push origin v1.0.0\n특정 태그 push"]
    E --> F
    F --> G["git push origin --tags\n모든 태그 push"]
    G --> H["GitHub → Releases 탭에서\n릴리즈 노트 작성"]
    H --> I(["🎉 릴리즈 완료!"])

    style A fill:#4A90D9,color:#fff
    style I fill:#27AE60,color:#fff
    style E fill:#F39C12,color:#fff
```

### 💡 태그 명령어 요약

```bash
# 🏷️ 태그 생성
git tag v1.0.0                            # 경량 태그
git tag -a v1.0.0 -m "첫 번째 릴리즈"   # 주석 태그 (권장)
git tag -a v1.0.0 커밋해시              # 특정 커밋에 태그 달기

# 📋 태그 확인
git tag                                    # 태그 목록
git show v1.0.0                           # 태그 상세 정보

# 🚀 태그 push
git push origin v1.0.0                    # 특정 태그 push
git push origin --tags                    # 모든 태그 push

# 🗑️ 태그 삭제
git tag -d v1.0.0                         # 로컬 태그 삭제
git push origin --delete v1.0.0          # 원격 태그 삭제
```

> 💡 **Semantic Versioning**: `v메이저.마이너.패치` (예: v1.2.3)
> - `메이저`: 하위 호환 불가능한 변경
> - `마이너`: 하위 호환 새 기능 추가
> - `패치`: 버그 수정

---

## 11. Pull Request 흐름

> GitHub에서 코드 리뷰를 받고 main에 합치는 흐름

```mermaid
flowchart TB
    A([🚀 기능 개발 완료]) --> B["git push origin feature/기능명\n브랜치 GitHub에 올리기"]
    B --> C["GitHub 웹사이트 접속\nCompare & Pull Request 클릭"]
    C --> D["PR 제목 & 설명 작성\n- 무엇을 바꿨는지\n- 왜 바꿨는지\n- 테스트 방법"]
    D --> E["Reviewer 지정\n리뷰어 선택"]
    E --> F{코드 리뷰}

    F -->|Changes requested\n변경 요청 💬| G["요청사항 로컬에서 수정"]
    G --> H["git add .\ngit commit -m 'fix: 리뷰 반영'"]
    H --> I["git push origin feature/기능명\n자동으로 PR에 반영됨 ✅"]
    I --> F

    F -->|Approved ✅| J{main에 뒤처졌나?}
    J -->|Yes| K["git fetch origin\ngit rebase origin/main\ngit push --force-with-lease"]
    K --> J2["PR 재확인"]
    J -->|No| L["Merge Pull Request 클릭"]
    J2 --> L
    L --> M["브랜치 Delete 클릭"]
    M --> N["git checkout main\ngit pull origin main\n로컬 동기화"]
    N --> O(["🎉 PR 완료!"])

    style A fill:#4A90D9,color:#fff
    style O fill:#27AE60,color:#fff
    style L fill:#27AE60,color:#fff
    style F fill:#F39C12,color:#fff
    style G fill:#E74C3C,color:#fff
    style K fill:#8E44AD,color:#fff
```

### 🚨 PR 비상 매뉴얼

```bash
# ❌ PR을 잘못된 브랜치로 날렸다!
# GitHub 웹에서 PR 페이지 → Edit → base 브랜치 변경 가능

# ❌ PR 올린 후 main에 새 커밋이 쌓여 브랜치가 뒤처졌다!
git checkout feature/기능명
git fetch origin
git rebase origin/main
git push origin feature/기능명 --force-with-lease

# ❌ Merge했는데 버그가 생겼다! 빨리 되돌려야 한다!
git log --oneline                        # 머지 커밋 해시 확인
git revert -m 1 머지커밋해시            # 머지 커밋 자체를 revert
git push origin main
```

---

## 12. 🚨 비상 매뉴얼

> 상황별로 바로 찾아서 쓸 수 있는 긴급 처방전

### 🔥 파일 실수 처방

```mermaid
flowchart TB
    A(["😱 파일 실수!"]) --> B{무슨 상황?}

    B -->|실수로 파일을\n삭제했다| C{commit 했나?}
    C -->|No| D["git restore 파일명\n삭제 전으로 복구"]
    C -->|Yes| E["git checkout HEAD~1 -- 파일명\n이전 커밋에서 복구"]

    B -->|민감한 파일을\npush해버렸다\n예: 비밀번호 API키| F["⚠️ 즉시 API키 재발급!\n보안 조치 먼저!"]
    F --> G["BFG Repo Cleaner 사용\n히스토리에서 파일 완전 삭제"]
    G --> H[".gitignore에 해당 파일 추가\n다시는 올라가지 않도록"]

    B -->|.gitignore를\n나중에 추가했는데\n파일이 계속 추적된다| I["git rm --cached 파일명\n추적에서만 제거 파일은 유지"]
    I --> J["git commit -m 'chore: gitignore 적용'"]

    style A fill:#E74C3C,color:#fff
    style D fill:#27AE60,color:#fff
    style E fill:#27AE60,color:#fff
    style F fill:#E74C3C,color:#fff
    style H fill:#F39C12,color:#fff
```

### 🔥 기타 긴급 처방 모음

```bash
# 🆘 방금 git reset --hard 실수! 다 날아갔다!
git reflog                             # 모든 작업 기록 확인 (30일 보존)
git reset --hard HEAD@{n}             # n번 이전 상태로 복구

# 🆘 로컬 브랜치를 원격이랑 강제로 똑같이 맞추고 싶다!
git fetch origin
git reset --hard origin/main          # ⚠️ 로컬 변경사항 모두 사라짐

# 🆘 엉뚱한 브랜치에 커밋했다!
git log --oneline                     # 커밋 해시 복사
git checkout 올바른브랜치
git cherry-pick 커밋해시              # 원하는 커밋 가져오기
git checkout 잘못된브랜치
git reset HEAD~1                      # 잘못된 브랜치에서 제거

# 🆘 작업 중인데 급하게 다른 브랜치 가야 한다!
git stash                             # 현재 작업 임시 저장
git checkout 다른브랜치
# ... 다른 작업 후 원래 브랜치로 돌아와서
git stash pop                         # 임시 작업 복원

# 🆘 push 후 커밋을 통째로 없애야 한다! (혼자 작업하는 브랜치)
git reset --hard HEAD~1
git push --force-with-lease          # 협업 브랜치에서는 절대 금지!
```

---

## 🔖 전체 명령어 Quick Reference

```bash
# ═══════════════ 시작 ═══════════════
git init                               # 새 저장소 초기화
git clone URL                          # 저장소 복제
git remote add origin URL              # 원격 저장소 연결
git remote -v                          # 원격 저장소 확인

# ═══════════════ 상태 확인 ═══════════
git status                             # 현재 상태
git log --oneline --graph --all        # 전체 브랜치 그래프
git diff                               # unstaged 변경사항
git diff --staged                      # staged 변경사항

# ═══════════════ 스테이징 & 커밋 ═════
git add .                              # 전체 스테이징
git add 파일명                         # 개별 스테이징
git add -p                             # 대화형 스테이징
git commit -m "feat: 내용"            # 커밋
git commit -am "fix: 내용"            # add + commit (tracked만)
git commit --amend -m "새 메시지"     # 직전 커밋 수정

# ═══════════════ 브랜치 ══════════════
git branch                             # 목록 확인
git checkout -b 브랜치명              # 생성 + 이동
git checkout 브랜치명                  # 이동
git merge 브랜치명                     # 현재 브랜치에 병합
git rebase main                        # 리베이스
git branch -d 브랜치명                # 삭제

# ═══════════════ 원격 동기화 ══════════
git fetch origin                       # 가져오기 (merge 안 함)
git pull origin main                   # 가져오고 병합
git push origin 브랜치명              # 업로드

# ═══════════════ 되돌리기 ════════════
git restore 파일명                     # 수정 취소 (add 전)
git restore --staged 파일명           # 스테이지에서 내리기
git reset --soft HEAD~1               # 커밋 취소, staged 유지
git reset HEAD~1                       # 커밋 취소, 파일 유지
git reset --hard HEAD~1               # 커밋 + 파일 모두 취소 ⚠️
git revert HEAD                        # 안전한 되돌리기 (push 후)

# ═══════════════ Stash ═══════════════
git stash                              # 임시 저장
git stash pop                          # 복원 + 스택 제거
git stash list                         # 목록 확인

# ═══════════════ 태그 ════════════════
git tag -a v1.0.0 -m "설명"          # 주석 태그 생성
git push origin --tags                 # 태그 push
```