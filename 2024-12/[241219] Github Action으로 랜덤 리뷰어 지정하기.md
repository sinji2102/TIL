# [241219] Github Action으로 랜덤 리뷰어 지정하기

</br>

## 🤔 문제 상황

사실 문제 상황은 아니긴 한데...

![image](https://github.com/user-attachments/assets/5500cc86-e615-48e9-8cfa-97aa292ae5ef)

비둥이들이 너무 바빠서 2명 어푸를 기다리기도 너무 힘들고!! 세 명 중에 두 명이니 자꾸 다른 사람이 하겠거니... 하는 마음에 어푸가 늦어지는 감이 있어서 도영 오빠가 무작위 한 명을 리뷰어를 지정하는 걸 제안했다!!

하지만 나랑 채현 언니는 1어푸가 너무 무서웠고... ㅠㅠ 그리고 예를 들어서 도영 오빠 PR에 랜덤 리뷰어로 내가 지정된다면 도영 오빠는 의미있는 리뷰를 못 받고 어푸하는 것 같아서 마음이 쓰였다!

![image](https://github.com/user-attachments/assets/7f45d907-18c3-4de1-9514-9aab5468369d)

그래서 지금처럼 리뷰어는 3명 다 걸되, 현재 거의 무의미하게 이용되고 있는 PR 코멘트를 제거하고 코멘트로 랜덤 리뷰어를 한 명 태그하는 방식으로 변경하는 걸 제안했다!! 그리고 제안한 사람이 구현하게 되어버렸다... 키키

</br>

## 🍀 Today I Learn

현재 오토라벨러 기능을 이용하면서, 라벨링과 동시에 코멘트도 남겨주고 있다.

```yml
# enable labeler on issues, prs, or both.
enable:
  issues: true
  prs: true

# comments object allows you to specify a different message for issues and prs
comments:
  issues: |
    이슈 작성하느라 고생 많았어요!! 새 기능도 아좌좟!! 라벨 잘 지정되었는지 확인 한 번 해 주기 🎇
  prs: |
    PR 작성하느라 고생 많았어요!! 라벨 잘 지정되었는지 확인 한 번 해 주기 🫶

labels:
  ":bug: BUG":
    include: ['\bBug\b', '\bbug\b', '\bFix\b', '\bfix\b']
  ":sparkles: FEAT":
    include: ['\bFeat\b', '\bfeat\b', '\bFeature\b', '\bfeature\b']
  ":memo: DOCS":
    include: ['\bDocs\b', '\bdocs\b', '\bREADME\b']
  ":package: CHORE":
    include: ['\bChore\b', '\bchore\b']
  ":lipstick: DESIGN":
    include: ['\bDesign\b', '\bdesign\b']
  ":rocket: API":
    include: ['\bAPI\b', '\bapi\b']
  ":recycle: REFACTOR":
    include: ['\bRefactor\b', '\brefactor\b']
  ":wrench: INIT":
    include: ['\bINIT\b', '\bInit\b', '\binit\b']
  ":twisted_rightwards_arrows: MERGE":
    include: ['\bMERGE\b', '\bMerge\b', '\bmerge\b']
  ":ambulance: !HOTFIX":
    include: ['\bHOTFIX\b', '\bhotfix\b']
  ":bookmark: RELEASE":
    include: ['\bRelease\b', '\brelease\b']
```

현재는 모든 PR과 ISSUE에 같은 코멘트를 남겨주고 있는데, PR 코멘트를 없애고 한 명을 태그하여 무조건 리뷰를 남기도록 할 예정이다.

```yml
name: "Pick Random Reviwer"

on:
  pull_request:
    types:
      - opened
    branches:
      - develop

jobs:
  pick-random-reviwer:
    runs-on: ubuntu-latest
    steps:
      // 1️⃣ 체크아웃
      - uses: actions/checkout@v2

      // 2️⃣ js 로 랜덤 1명 추출
      - name: pick_random_reviwer
        id: pick_random_reviwer // 다음 step 에 output 을 넘기기 위해 추가
        uses: actions/github-script@v3 // js 를 사용하기 위해 사용
        with:
          github-token: ${{secrets.GITHUB_TOKEN}}
          script: |
            const candidate = ['reviewer1', 'reviewer2', 'reviewer3'];
            const randomReviewer = candidate[Math.floor(Math.random() * candidate.length)];
            const comment = `🎉 @${randomReviewer} 님 필수 리뷰어로 당첨되셨습니다! 리뷰를 부탁드립니다. 🙏`
            core.setOutput('comment', comment) // output 세팅

      // 3️⃣ 댓글 추가
      - name: comment PR
        uses: mshick/add-pr-comment@v1
        with:
          message: |
            ${{ steps.pick_random_reviwer.outputs.comment }} // output 을 가져오기
          repo-token: ${{ secrets.GITHUB_TOKEN }}
          repo-token-user-login: 'github-actions[bot]'
```

위 코드는 다름 사람들의 코드를 참고하여 제작한 코드이다. 우리는 리뷰어는 세 명 모두 할당되어야 하므로 리뷰어에 태그하는 부분을 제거하고, 코멘트만 살릴 수 있도록 세팅하였다.

</br>

## ⚡마무리

BEAT 시작할 때도 오토라벨러, 오토리뷰어, 슬랙봇 등을 내가 세팅했는데 Github Action을 이용했었다. 이미 만들어진 Github Action들이 많아서 간단하게 사용할 때 좋기도 하고, 조금만 읽어봐도 어떤 내용인지 알 수 있어서 커스텀하기에도 좋은 것 같다.

</br>

참고 자료
</br>

[Github Actions 과 CICD (랜덤 리뷰어 뽑기)](https://velog.io/@parkjisu6239/Github-Actions-%EA%B3%BC-CICD-%EB%9E%9C%EB%8D%A4-%EB%A6%AC%EB%B7%B0%EC%96%B4-%EB%BD%91%EA%B8%B0#pick-random-reviewer)

[github actions로 PR 랜덤 리뷰어 지정하기](https://bluemiv.tistory.com/122)
