# [241205] git rebase란?

</br>

## 🤔 문제 상황

![image](https://github.com/user-attachments/assets/4d55d7f2-d103-41dd-9af8-f572220f62ad)

이번 스프린트를 진행하면서, 디자인이 너무 많이 바뀌어 거의 새로 짜야 하는 페이지가 있었다. 때문에 퍼블리싱 / 기능 구현 Issue를 분리해서 작업했는데, 퍼블리싱 branch가 develop에 merge 되기 전에 퍼블리싱 branch에서 작업한 내용을 받아와서 작업해야 하다 보니 기능 구현 branch에 엄청나게 많은 양의 commit이 쌓였다...

큰 문제가 있는 건 아니지만, 코드 리뷰 하는 과정에서 이미 본 코드를 다시 봐야 하고 해당 branch에서 수정하지 않은 파일이나 내역도 섞여 들어간다는 점이 리뷰어에게 너무... 번거로운 것 같아서 이번 기회에 rebase를 공부해보고 싶었다.
</br>

## 🍀 Today I Learn

### git rebase란?

**Re-Base**, 말 그대로 base commit을 재설정하는 명령어이다.

이미지를 통해 알아보면,

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FRLT5D%2FbtrDaTV7LAl%2FFvHk1uIQetiv9066Y2vlgk%2Fimg.png)

feature브랜치에서 a3 커밋을 베이스로 작업을 진행하고 있고, master 브랜치는 이미 상당부분 진행되었다. 이때, 최신 master 브랜치의 작업 내용을 본인이 현재 작업 중인 feature branch에도 적용하고 싶을 때 rebase 명령어를 사용하면 된다.

기존 나의 경우 master 혹은 적용하고 싶은 branch를 pull 받는 방식으로 사용하였는데, master를 pull 받는 경우에는 (충돌이 있는 경우) 충돌을 해결하고 push하면 master의 commit이 섞이지 않았지만 타 branch를 pull 받아왔을 때 문제가 생겼다.

위와 같은 작업을 위해서

```
# master 브랜치로 이동
git checkout master
# 최신 master 받기
git pull origin master

# feature브랜치로 이동
git checkout feature
# master를 base로 rebase 명령어 실행
git rebase master
```

작업을 이용하면 아래 사진과 같이 commit 히스토리를 깔끔하게 관리할 수 있다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FVvxdY%2FbtrC8nqv3FF%2FaXIKOSiLduvkaiuLuyGzTK%2Fimg.png)

위의 예시가 master branch가 아니라 퍼블리싱 branch였고, feature branch가 기능 구현 branch라고 생각하면 내가 rebase를 사용하고 싶은 이유를 알 수 있다.

### rebase를 안 쓰는 이유

이렇게 편리한데, 사람들이 rebase를 안 쓰는 이유는 무엇일까?

위에서 설명한대로만 되면 정말 좋겠지만... rebase는 사실 협업에서 굉장히 위험한 방법이다.

> "Do not rebase commits that exist outside your repository and that people may have based work on.</br>
> ”다른 동료가 작업 중인 외부에 공개 된 저장소 브랜치를 대상으로 리베이스하면 안됩니다."

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FtfOFZ%2FbtsEQPxE7Ef%2FGWhBeDFTkpkJTQQ5EF52L1%2Fimg.png)

위와 같은 상태라고 하자, G에서 작업을 한 후 push를 한 상태이며 위와 같이 G-C1, G-C2, G-C3 commit이 존재한다.

그런데 G branch에서 Main branch에 대해 Rebase, 정확히 말하면 로컬 G branch에서 Main branch에 대해 Rebase를 한다. 그럼 로컬 G branch의 깃 히스토리는 아래와 같이 변경된다. 이때 push를 하면 어떻게 될까?

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbYXFEC%2FbtsEOTU0pfA%2FLMXmlk9JZ7Bj1noKUk7xeK%2Fimg.png)

이런 경우에는 충돌이 발생한다. Rebase 에 의해 로컬 branch의 깃 히스토리가 변경되었고 원격 branch로 Push 하려 하니 원격 branch 입장에서 깃 히스토리가 일치하지 않아 충돌이 발생하는 것이다. 원격 branch 입장에서는 로컬 branch가 자신과 이름만 같은 branch인 것이다. 결국 아래 그림과 같은 상황이 생긴다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FMY0eV%2FbtsEIO7XrBL%2Fb7rKvf3ROeRlO3Z1iVK28K%2Fimg.png)

결과적으로 매 branch마다 충돌을 해결해 줘야 하고, 동일 branch간 merge 작업도 따로 해 주어야 한다. 그럼 결국 merge 히스토리가 남게 되어 로그는 로그대로 남고, 일은 두 배로 하는 셈이다. 그렇다면 협업에서 rebase를 사용하는 방법은 무엇일까?

바로 **강제 push**를 하는 방법이다. 강제 Push 를 통해 로컬과 원격 브랜치 간 히스토리를 맞춰줌으로써 해결할 수 있다.즉, Rebase를 하면 강제 Push 작업이 동반된다.

이러한 강제 Push는 해당 브랜치에 협업자가 있을 경우 문제가 생길 수 있다. 특히 이러한 사실을 몰랐을 때 예기치 못한 상황에 당황할 수 있고, 자신이 Push 했던 작업 내역을 잃어버릴 수도 있다.

1번 개발자가 G 브랜치를 pull 받은 후 Rebase 한 이후 시점에 2번 개발자가 G 브랜치에 작업 내역을 push 한다면, 1번 개발자의 로컬 G 브랜치에는 2번 개발자가 작업한 내용이 반영되지 않은 상태이다. 이때 1번 개발자가 Rebase 한 내역을 원격 브랜치에 덮어쓰기 위해 강제 push를 하게 된다면 2번 개발자가 작업한 내용과 히스토리 모두 날아가게 된다. 생각만해도레전드끔찍... 때문에 협업 시 rebase는 신중해야 하고, 모든 팀원들이 rebase를 사용하고 있음을 인지하고 있어야 한다.

</br>

## ⚡마무리

SOPT 데모데이 날, 관련 질문을 준혁 오빠가 멘토분께 했는데 rebase를 이용하면 해결할 수 있다는 답변을 받은 기억이 있어 rebase에 대해 찾아봤다.

물론 해제할 수는 있지만... 우리 팀은 내가 repo에 강제 push를 막아뒀기 때문에 병렬 작업이 많은 우리 팀은 rebase를 사용할 수 없을 것 같다... 난 develop에서 작업하는 것두 싫은데 강제 push를 용납할 수 업써!!! ㅠㅠ 물론 팀에서 하자구 하면 해야겠지만 아무도 rebase에 관심이 없는 것 같다.

그리고 찾아볼수록 rebase는 신중해야 한다는 내용이 많아서 오히려 rebase에 겁만 먹게 되는 TIL이었다...

---

참고 자료
</br>
[\[Git\] git rebase 란??](https://myung-ho.tistory.com/91)
</br>
[\[Git\] Git Rebase 란? / 쉽게 이해하기 / 예시](https://tlatmsrud.tistory.com/156)
