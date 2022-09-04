---
layout: post
toc: false
title: "Visual Studio Code Branch Protection"
categories: ["Programming"]
tags: [vscode, git]
author:
  - 이현재
---

소스 코드 관리 시스템으로 가장 많이 쓰이는 Git에서는 여러 브랜치를 통해
다른 작업을 구분하면서 동시에 수행할 수 있는데, master나 develop 브랜치와 같이
여러 사람이 작업을 시작하는 베이스 브랜치, 릴리즈의 주가 되는 역할을 하는 브랜치는
일반적으로 바로 커밋을 넣지 않습니다. <!--more--> 다른 브랜치에서 작업을 하고
Pull Request를 넣는 방식으로 작업이 이루어지는데요. 이러한 브랜치들을
Github나 Gitlab에서는 `protected branch`라 명명하고
이런 브랜치들의 삭제, 수정을 제한하는 기능을 제공하고 있습니다.

그러나 develop 브랜치에서 시작해서 작업을 진행해야 하다 보니, 가끔
다른 브랜치를 따야 함을 잊어버리고 그대로 커밋을 해서 심지어는
PR까지 날려버리는 참사가 발생하곤 합니다.

커밋이나 PR을 날리기 전 다시 한 번 확인하는 습관을 들이는게
쉽지만은 않습니다. 이러한 주요 브랜치들에 대한 커밋과 PR을 막는 방법은
Git Hook 등을 포함한 방법들이 있지만, vscode [v1.68 버전][vscode-release-note-1.68]에서
Branch Protection 기능이 추가되어 간단하게 커밋을 방지할 수 있습니다.

vscode 설정에 들어가 `Git: Branch Protection`을 검색합니다.
`Add Item` 버튼으로 보호하고 싶은 branch 이름을 추가합니다.

![branch-protection-1](/img/2022-07-02-vscode-branch-protection/branch-protection-1.png)

`Git: Branch Protection Prompt`에서 `alwaysPrompt`를 선택한 뒤 (기본 값)
vscode의 Source Control 탭을 통해 커밋을 시도하면 아래와 같은
프롬프트 창이 뜨며 커밋을 방지합니다. 또는 `alwaysCommitToNewBranch`를 선택하면
브랜치에 커밋을 시도하면 새 브랜치 생성 프롬프트가 뜨며 만들어진 브랜치에
커밋이 완료됩니다.

![branch-protection-2](/img/2022-07-02-vscode-branch-protection/branch-protection-2.png)

안타깝게도 vscode 터미널에서 커밋하는 것까지는 방지하지 못합니다.
추후에 업데이트로 vscode 터미널에서도 해당 기능이 동작했으면 좋겠네요.

추가로 [v1.69 버전][vscode-release-note-1.69] 업데이트로 보호되고 있는 브랜치는 이름 옆에 작은 자물쇠 아이콘이 보입니다.

![v1.69 lock icon](https://code.visualstudio.com/assets/updates/1_69/scm-branch-protection-statusbar.png)

[vscode-release-note-1.68]: https://code.visualstudio.com/updates/v1_68#_git-branch-protection
[vscode-release-note-1.69]: https://code.visualstudio.com/updates/v1_69#_branch-protection-indicators
