---
title: "Cursor Talk to Figma MCP 연결하기: 실전 해결 가이드"
excerpt: "공식 문서만으로는 해결되지 않는 MCP 설정 문제들과 실제 작동하는 해결법을 정리했다."
date: 2025-05-31 11:46:00 +0900
last_modified_at: 2025-05-31 12:03:00 +0900
toc: true
toc_sticky: true
---

# Cursor Talk to Figma MCP 연결하기: 실전 해결 가이드

Cursor와 Figma를 연결하는 MCP(Model Context Protocol) 설정은 공식 문서만으로는 해결되지 않는 여러 문제가 있다. 실제 설정 과정에서 마주치는 문제들과 해결법을 정리했다.

## 1. MCP 기본 설정: Node.js 권한 문제 해결

### Node.js 설치
개발 환경이 처음이라면 기본부터 설정한다.

```bash
# Node.js 공식 사이트에서 LTS 버전 다운로드
https://nodejs.org/
```

### MCP 기본 테스트
설정이 제대로 되는지 [MCP 공식 퀵스타트](https://modelcontextprotocol.io/quickstart/user)의 filesystem 예제로 먼저 확인한다.

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/path/to/allowed/directory"
      ]
    }
  }
}
```

**중요**: 여기서 EACCESS 에러가 나면 Node.js 권한 문제다. 두 가지 해결법이 있다.

```bash
# 방법 1: npm 글로벌 디렉토리 소유권 변경 (chown 방식)
sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}

# 방법 2: npx 전체 경로 사용
which npx
# 그 다음 설정에서 전체 경로 사용
```

개인적으로는 chown 방식이 가장 간단하고 확실했다.

## 2. cursor-talk-to-figma MCP: 실제 설정법

[공식 README](https://github.com/sonnylazuardi/cursor-talk-to-figma-mcp?tab=readme-ov-file#get-started)를 그대로 따라하면 제대로 작동하지 않는다. 실제로는 다음과 같이 해야 한다.

### Bun 설치
```bash
curl -fsSL https://bun.sh/install | bash
```

### 로컬 서버 설정
```bash
# 1. 레포지토리 클론
git clone https://github.com/sonnylazuardi/cursor-talk-to-figma-mcp.git
cd cursor-talk-to-figma-mcp

# 2. 의존성 설치
bun install

# 3. 서버 실행 (핵심 단계)
bun socket
```

**주의**: README에 나오는 `bunx cursor-talk-to-figma-mcp`는 클라이언트에서 하는 명령어다. 서버는 `bun socket`으로 직접 실행해야 한다.

### Figma 플러그인 설정
[공식 플러그인 가이드](https://github.com/sonnylazuardi/cursor-talk-to-figma-mcp?tab=readme-ov-file#figma-plugin)와 다르게 설정한다.

1. Figma Desktop 앱 열기
2. Plugins > Development > Import plugin from manifest
3. 클론받은 폴더의 `figma-plugin/manifest.json` 선택

### MCP 설정 파일 수정
처음에는 이렇게 설정하지만:

```json
{
  "mcpServers": {
    "TalkToFigma": {
      "command": "bunx",
      "args": [
        "cursor-talk-to-figma-mcp@latest"
      ]
    }
  }
}
```

EACCESS 에러가 나면 bun 전체 경로를 사용한다:

```bash
# bun 위치 확인
which bun
```

```json
{
  "mcpServers": {
    "TalkToFigma": {
      "command": "/Users/heesu/.bun/bin/bunx",
      "args": [
        "cursor-talk-to-figma-mcp@latest"
      ]
    }
  }
}
```

## 3. 최종 점검

1. `bun socket` 명령어로 로컬 서버가 실행 중인지 확인
2. Figma에서 플러그인이 제대로 import 되었는지 확인
3. Cursor에서 MCP 연결 상태 확인

이렇게 설정하면 Cursor에서 Figma 디자인을 직접 읽고 코드로 변환할 수 있다. 공식 문서만으로는 해결되지 않는 권한 문제와 서버 실행 방식을 정확히 이해하는 것이 핵심이다.
