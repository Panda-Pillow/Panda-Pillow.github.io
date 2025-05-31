---
title: "GitHub Pages + Jekyll + MMistakes 설치 가이드: 모바일로도 가능한 초간단 설정"
excerpt: "MMistakes 공식 문서의 Remote Theme 방식으로 GitHub Pages 블로그를 설치하고 설정하는 과정을 정리했다. 모바일에서도 완료할 수 있을 정도로 간단한 방법을 소개한다."
date: 2025-05-28 09:56:00 +0900
last_modified_at: 2025-05-31 15:00:00 +0900
published: true
---

[이전 글](./기술블로그-스택선정-github-pages-jekyll)에서 GitHub Pages + Jekyll + MMistakes 조합을 선택한 이유를 다뤘다. 이번에는 실제 설치 과정을 정리한다. 예상보다 훨씬 간단해서 모바일로도 완료할 수 있었다.

## MMistakes 설치 방법 선택

[MMistakes 공식 문서](https://github.com/mmistakes/minimal-mistakes?tab=readme-ov-file#installation)를 보면 설치 방법이 세 가지 나온다:

1. **Gem-based method**: 로컬에 Jekyll 설치 필요
2. **Remote theme method**: GitHub Pages 호환 (👈 선택)
3. **Fork/Copy method**: 모든 테마 파일을 직접 복사

**Remote theme method**를 선택한 이유:
- 로컬에 Jekyll 설치 불필요
- GitHub Pages에서 바로 작동
- 커스터마이징은 제한적이지만 광고 달기, 사이드바 설정 등 필요한 기능은 모두 지원
- 테마 업데이트 자동 반영

### Remote Theme의 장점과 제약

**장점:**
- 광고 달기 ✅
- 기본 커스터마이징 ✅ (색상, 폰트 변경, 레이아웃 수정, 네비게이션, 사이드바 등)
- CSS 추가 (assets/css/main.scss)

**제약:**
- 구조적 변경 ❌ (테마 핵심 파일들이 gem에 숨어있어서 직접 수정 불가)
- 복잡한 플러그인 추가하려면 Fork 필요

**결론:** 광고 + 기본 커스터마이징 충분히 가능

## 설치 과정

**정말 간단해서 실제로 이동하면서 모바일로 완성했다.** 이 정도면 진입 장벽이 거의 없다고 봐도 된다.

### 1. 템플릿에서 저장소 생성

1. [MMistakes 스타터 템플릿](https://github.com/mmistakes/mm-github-pages-starter) 접속
2. **"Use this template"** 버튼 클릭
3. 저장소 이름을 `username.github.io` 형식으로 설정
4. **Public**으로 설정하고 생성 (GitHub Pages 무료 계정에서는 Private 저장소 지원 안 함)

### 2. GitHub Pages 활성화

저장소 생성 후 자동으로 GitHub Pages가 활성화된다. 몇 분 후 `https://username.github.io`에서 블로그 확인 가능.

### 3. 기본 설정 (_config.yml 수정)

아래 _config.yml 설정 변경사항을 직접 확인하고 싶다면 [GitHub 커밋](https://github.com/Panda-Pillow/Panda-Pillow.github.io/pull/1/commits/3429db80b72c59018fb39e7c06bffe9f1c436aa6)에서 확인할 수 있다.

#### 기본 정보 설정

가장 먼저 블로그 제목과 이메일을 설정했다. 기본 템플릿에서 실제 내용으로 바꿨던 모습:

![_config.yml 기본 설정 변경사항](/assets/images/posts/2025-05-28-설치가이드/config-blog.png)

```yaml
title: Panda Pillow
email: heesu.jung.developer@gmail.com
github_username: Panda-Pillow
minimal_mistakes_skin: default
search: true
```

#### 저자 정보 설정

사이드바에 내 정보와 링크들이 나오도록 설정했다. 만들고 있는 앱 링크를 넣었다. font-awesome 아이콘을 넣을 수도 있다.   
bio image는 아직 쓰고 싶은 게 없어서 아예 삭제했다.

![author 설정 변경사항](/assets/images/posts/2025-05-28-설치가이드/config-author.png)

```yaml
author:
  name: "Heesu Jung"
  links:
    - label: "플레이스토어"
      icon: "fab fa-fw fa-google-play"
      url: "https://play.google.com/store/apps/details?id=com.heesu.photomenu"
    - label: "앱스토어"
      icon: "fab fa-fw fa-app-store-ios"
      url: "https://apps.apple.com/kr/app/사진-메뉴판/id6746056598"
```

아이콘 참고:
- `fas`=채워진 아이콘, `fab`=브랜드 아이콘, `far`=테두리 아이콘.   
- 개발중인 앱 랜딩페이지를 노션으로 쓰고 있어서 링크하고 싶었는데, 노션 아이콘은 없어서 아쉬웠다.

#### Footer 설정

기본 템플릿에는 Footer에 소셜 링크들이 있었는데, 딱히 넣고 싶은 내용이 없어서 모두 삭제했다.

![footer 설정 변경사항](/assets/images/posts/2025-05-28-설치가이드/config-footer.png)

```yaml
footer:
  # 모든 links 삭제
```

#### 한국어 설정
```yaml
locale: "ko-KR"
timezone: Asia/Seoul
```

#### `liquid`?

```yaml
category_archive:
  type: liquid
  path: /categories/
tag_archive:
  type: liquid
  path: /tags/
```

**Liquid**는 Jekyll에서 사용하는 템플릿 언어다:
- HTML 안에 동적 콘텐츠 넣을 수 있게 해줌
- `{% raw %}{{ }}{% endraw %}`, `{% raw %}{% %}{% endraw %}` 문법 사용
- 반복문, 조건문, 변수 등 프로그래밍 로직 구현

**Archive 페이지에서의 역할**:
- `/categories/` 페이지를 Liquid로 동적 생성
- 블로그 글들의 카테고리/태그별로 자동 목록 페이지 생성
- 사용자가 카테고리 클릭하면 해당 글들만 보여줌

### 4. 첫 번째 글 작성

`_posts` 폴더에 `2025-05-27-블로그-시작.md` 파일 생성:

```markdown
---
title: "블로그 시작"
date: 2025-05-27 19:30:00 +0900
published: true
---

사진 메뉴판 앱을 개발/운영하며 느끼는 점들을 공유하기 위해 블로그를 개설했다.   
```

#### 임시저장?

임시 저장을 할 수도 있다.

1. **Draft 기능**: `published: false` 추가하면 사이트에 안 보임
2. **_drafts 폴더**: 임시 파일 보관
3. **로컬 편집**: Notion 활용하거나 GitHub에서 직접 작성


## 마무리: 이제 진짜 시작

Remote Theme 방식으로 블로그를 만들어보니 **기본 커스터마이징이 충분히 가능**하다는 걸 확인했다. 복잡한 구조 변경은 안 되지만, 개인 블로그로는 부족함이 없다. **광고 수익화도 가능**하다고 하니, 글을 써나가며 천천히 붙여보겠다.

앞으로 **사진 메뉴판 앱 개발 과정과 운영 경험**을 정리해서 올릴 예정이다. 특히 1인 개발자로서 겪는 시행착오들과 해결 과정을 공유하고 싶다.

다음 글에서는 Cursor 환경에서 더 효율적으로 블로그를 운영하는 팁들을 다룰 예정이다.

