---
title: "Jekyll 로컬 개발 환경 구축과 Cursor 활용법"
excerpt: "GitHub Pages의 임시저장 한계를 해결하고 Cursor에서 Jekyll을 효율적으로 사용하는 방법을 정리했다. rbenv 설치부터 단축키로 바로 실행하는 방법까지 다룬다."
date: 2025-05-31 20:45:00 +0900
published: false
toc: true
---

[이전 글](./github-pages-jekyll-mmistakes-설치가이드)에서 GitHub Pages와 Jekyll을 설치했다. GitHub Actions로 자동 배포되는 건 좋지만, `published: false`로 설정한 임시저장 글은 실제 사이트에 전혀 나타나지 않는다. 

글이 어떻게 보일지 미리 확인할 방법이 없어서 결국 로컬에서 Jekyll을 직접 실행해야 했다. rbenv로 Ruby 환경을 구축하고 Cursor에서 단축키로 바로 실행하는 방법까지 정리한다.

## 왜 로컬 개발환경이 필요한가?

### GitHub Pages의 한계

GitHub Pages는 편리하지만 몇 가지 제약이 있다:

- **임시저장 미리보기 불가**: `published: false` 설정한 글은 사이트에 아예 나오지 않음
- **실시간 수정 확인 어려움**: 커밋 → 빌드 → 배포 과정이 몇 분 소요
- **디버깅 한계**: 빌드 에러 원인 파악이 어려움

### 로컬 개발의 장점

- **Draft 미리보기**: `--drafts` 옵션으로 임시저장 글도 확인 가능
- **라이브 리로드**: 파일 수정하면 브라우저가 자동 새로고침
- **빠른 피드백**: 수정사항을 즉시 확인
- **플러그인 테스트**: GitHub Pages에서 지원하지 않는 플러그인도 테스트 가능

## Ruby 환경 구축 (macOS 기준)

### rbenv 설치

Ruby 버전 관리를 위해 rbenv를 사용한다. [rbenv 공식 저장소](https://github.com/rbenv/rbenv)에서 권장하는 Homebrew 방식으로 설치했다.

```bash
# Homebrew로 rbenv 설치
brew install rbenv ruby-build

# rbenv 초기화 설정 추가
echo 'eval "$(rbenv init - zsh)"' >> ~/.zshrc
source ~/.zshrc

# 설치 확인
rbenv versions
```

### Ruby 설치

GitHub Pages에서 사용하는 Ruby 버전에 맞춰 설치한다. [GitHub Pages 의존성 페이지](https://pages.github.com/versions/)에서 현재 지원하는 Ruby 버전을 확인할 수 있다.

```bash
# 사용 가능한 Ruby 버전 확인
rbenv install -l

# Ruby 3.1.4 설치 (GitHub Pages 지원 버전)
rbenv install 3.1.4

# 프로젝트에서 사용할 Ruby 버전 설정
cd ~/Documents/Panda-Pillow.github.io
rbenv local 3.1.4

# 버전 확인
ruby -v
```

rbenv가 제대로 설정되면 프로젝트 폴더에 `.ruby-version` 파일이 생성된다.

### Jekyll과 의존성 설치

```bash
# Bundler 설치
gem install bundler

# 프로젝트 의존성 설치
bundle install
```

`bundle install` 명령어는 `Gemfile`에 정의된 모든 gem을 설치한다. GitHub Pages와 호환되는 Jekyll 버전과 플러그인들이 자동으로 설치된다.

## Jekyll 로컬 서버 실행

### 기본 실행

```bash
# 기본 서버 실행
bundle exec jekyll serve

# 임시저장 글도 함께 보기
bundle exec jekyll serve --drafts

# 라이브 리로드 + 임시저장
bundle exec jekyll serve --livereload --drafts
```

- `--livereload`: 파일 수정 시 브라우저 자동 새로고침
- `--drafts`: `published: false` 설정된 글들도 표시
- `bundle exec`: 프로젝트의 Gemfile에 정의된 정확한 gem 버전 사용

서버가 실행되면 `http://localhost:4000`에서 블로그를 확인할 수 있다.

### 포트 변경

다른 포트를 사용하고 싶다면:

```bash
bundle exec jekyll serve --port 4001 --livereload --drafts
```

## Cursor에서 효율적으로 사용하기

### launch.json 설정

Cursor(VS Code 기반)에서 단축키로 Jekyll 서버를 실행할 수 있도록 `.vscode/launch.json` 파일을 설정했다.

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Jekyll Serve",
            "type": "node-terminal",
            "request": "launch",
            "command": "bundle exec jekyll serve --livereload --drafts",
            "cwd": "${workspaceFolder}"
        }
    ]
}
```

이제 Cursor에서 **F5 키**를 누르거나 **Run and Debug** 패널에서 "Jekyll Serve"를 실행하면 바로 서버가 시작된다.

### tasks.json으로 더 편리하게

빌드 작업으로 설정하면 **Cmd+Shift+P** → "Tasks: Run Task"에서 바로 실행할 수 있다. `.vscode/tasks.json` 파일을 생성한다:

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Jekyll Serve",
            "type": "shell",
            "command": "bundle exec jekyll serve --livereload --drafts",
            "group": "build",
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "new"
            },
            "problemMatcher": []
        },
        {
            "label": "Jekyll Build",
            "type": "shell",
            "command": "bundle exec jekyll build",
            "group": "build",
            "presentation": {
                "echo": true,
                "reveal": "always",
                "focus": false,
                "panel": "new"
            },
            "problemMatcher": []
        }
    ]
}
```

### Cursor 키보드 단축키 설정

더 편리하게 사용하려면 키보드 단축키를 직접 지정할 수 있다. **Cmd+Shift+P** → "Preferences: Open Keyboard Shortcuts (JSON)"에서 다음을 추가한다:

```json
[
    {
        "key": "cmd+shift+j",
        "command": "workbench.action.tasks.runTask",
        "args": "Jekyll Serve"
    }
]
```

이제 **Cmd+Shift+J**만 누르면 Jekyll 서버가 실행된다.

## 워크플로우 최적화

### 개발 워크플로우

1. **Cursor에서 글 작성**: `published: false`로 임시저장
2. **단축키로 서버 실행**: F5 또는 Cmd+Shift+J
3. **실시간 미리보기**: 수정사항이 자동으로 브라우저에 반영
4. **완성 후 배포**: `published: true`로 변경하고 커밋

### 파일 관리 팁

```bash
# _drafts 폴더 활용
mkdir _drafts
mv _posts/2025-05-31-working-draft.md _drafts/

# 완성 후 _posts로 이동
mv _drafts/working-draft.md _posts/2025-05-31-jekyll-guide.md
```

`_drafts` 폴더에 있는 파일들은 `--drafts` 옵션 없이는 보이지 않는다. 작업 중인 글과 완성된 글을 명확히 구분할 수 있다.

## 자주 발생하는 문제와 해결법

### 포트 충돌

```bash
# 실행 중인 Jekyll 프로세스 확인
lsof -i :4000

# 프로세스 종료
kill -9 [PID]

# 또는 다른 포트 사용
bundle exec jekyll serve --port 4001
```

### Gem 버전 충돌

```bash
# Gemfile.lock 삭제 후 재설치
rm Gemfile.lock
bundle install

# Bundler 업데이트
bundle update
```

### 라이브 리로드가 작동하지 않을 때

브라우저에서 **Cmd+Shift+R** (강제 새로고침)을 시도하거나, 터미널에서 서버를 재시작한다.

## GitHub Pages와 로컬 환경 동기화

### 의존성 버전 일치

로컬과 GitHub Pages의 Jekyll 버전을 맞추는 것이 중요하다. `Gemfile`에서 `github-pages` gem을 사용하면 자동으로 동일한 버전이 설치된다:

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
```

### 설정 파일 통일

`_config.yml` 설정이 로컬과 GitHub Pages에서 동일하게 작동하는지 확인한다. 일부 플러그인은 로컬에서만 작동할 수 있다.

## 마무리: 효율적인 블로그 운영

로컬 개발환경을 구축하니 글 작성이 훨씬 편해졌다. 특히 임시저장 글을 미리 볼 수 있게 된 점이 가장 큰 장점이다. Cursor의 단축키와 조합하면 터미널 명령어를 외울 필요도 없다.

다음에는 Jekyll의 다양한 기능들과 Minimal Mistakes 테마 커스터마이징 방법을 다룰 예정이다. 특히 광고 달기와 SEO 최적화 과정을 공유하겠다.
