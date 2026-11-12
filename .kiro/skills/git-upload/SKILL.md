---
name: git-upload
description: 이 저장소의 변경 사항을 GitHub에 올릴 때 사용한다. "깃에 올려줘", "커밋해줘", "푸시해줘", "업로드해줘", "최신 내용 받아줘(pull)" 같은 요청에 맞춘다. pull로 최신화 → 비밀 정보 점검 → 한글 1줄 커밋 → push 순서로 진행한다.
---

# Git 업로드

이 저장소의 변경을 안전하게 커밋하고 GitHub로 push한다. 브랜치는 `main` 하나만 쓴다. 사용자가 요청하지 않으면 브랜치를 만들지 않는다.

## 1. 현재 상태 확인

```powershell
git status
git branch --show-current
git remote -v
```

- 현재 브랜치가 `main`이 아니면 멈추고 사용자에게 알린다.
- 바뀐 파일이 없으면 "올릴 변경이 없습니다"라고 알리고 끝낸다.

## 2. 최신 내용 받기 (pull)

```powershell
git pull --ff-only
```

- 충돌이나 `Not possible to fast-forward`가 나면 **임의로 해결하지 말고** 멈춘다. 어떤 파일이 겹쳤는지 사용자에게 알린다.

## 3. 올릴 파일 고르기와 비밀 정보 점검

`git add .`를 바로 쓰지 않는다. 바뀐 파일 목록을 보여주고 이번 목적에 맞는 파일만 담는다.

```powershell
git add <파일1> <파일2>
git diff --cached --stat
git diff --cached
```

아래가 담겨 있으면 **커밋하지 말고** 사용자에게 알린다.
- `.env`, `*.pem`, `*.key`, `credentials`, `*.tfstate` 같은 파일
- `AKIA`로 시작하는 AWS 액세스 키, `password=`, `secret=`, `token=`, `-----BEGIN ... PRIVATE KEY-----`
- 개인 이메일, 계정 번호, 내부 URL(Start URL 등)
- 50MB가 넘는 파일, `node_modules/` 같은 설치 폴더

필요하면 `.gitignore`에 추가하는 것을 제안한다. 이 경우 `.gitignore` 변경은 별도 커밋으로 한다.

## 4. 커밋

메시지는 **한글 1줄**, `타입: 간결한 설명` 형식으로 쓴다. 본문은 쓰지 않는다.

| 타입 | 용도 |
|------|------|
| `feat` | 새 기능·새 콘텐츠 추가 |
| `fix` | 오류 수정 |
| `docs` | 문서 수정 |
| `tidy` | 정리 (동작 변화 없음) |
| `style` | 서식만 변경 |
| `chore` | 설정·빌드 변경 |
| `test` | 테스트 추가·수정 |

```powershell
git commit -m "feat: 실습 1 결과 추가"
```

- 한 커밋에는 한 가지 목적만 담는다. 정리(`tidy`)와 기능(`feat`)이 섞여 있으면 커밋을 나눈다.
- `git config user.name`이 비어 있어서 커밋이 실패하면, Git 초기 설정을 먼저 하라고 안내한다.

## 5. push

push하기 전에 올라갈 커밋을 보여주고 사용자 확인을 받는다.

```powershell
git log --oneline origin/main..HEAD
git push
```

- `rejected (fetch first)`가 나면 2단계 pull을 다시 하고 push를 다시 시도한다.
- `git push --force`는 **절대 쓰지 않는다.**
- 인증 오류가 나면 GitHub 로그인(`Sign in with your browser`)을 안내한다.

## 6. 결과 보고

- 커밋 해시와 메시지
- push 성공 여부
- GitHub에서 확인할 주소: `git remote get-url origin`
