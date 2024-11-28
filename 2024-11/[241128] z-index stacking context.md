# [241128] z-index stacking context

</br>

## 🤔 문제 상황

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/381b2ae3-e9d9-4670-9f04-d72f49633a8e"></td>
        </tr>
</table>

어제 밤에 TIL 쓸 거리를 웨비둥이 톡방에 추천 받고 있었는데, 채현 언니가 `z-index`를 써달라고 했다. 사실 처음에는 '뭔 z-index를 써달라고 하지?...' 싶었는데 한 번 검색이나 해보자 하고 찾아봤다가 너무 흥미로운 문제를 발견해서 오늘 TIL로 쓰고자 한다. 그리고 채현 언니가 보내준 링크는... 대충만 읽었다. 언니미아내ㅋㅋ

</br>

## 🍀 Today I Learn

### z-index란?

먼저 z-index에 대해 알고 있어야 한다.

z-index는 HTML이 화면에 그려질 때 어떤 요소가 위에 올라가는지를 설정하는 속성이다. **z-index의 값이 높을 수록 위에 올라가며 z축 기준으로 순서를 가진다.**

z-index는 양수, 음수, auto 값을 가질 수 있으며 따로 지정하지 않으면 auto다. auto는 z-index를 부모 요소와 동일하게 설정한다. 음수 값을 설정하면, 아래로 깔리게 된다.

반드시 **position 속성이 static(기본값)이 아닌 것으로 설정**되어 있어야 한다. z-index의 숫자가 높을 수록 상위에 올라온다.

</br>

예를 들어 아래와 같은 코드가 있을 경우, (편의를 위해 상단에 html 코드, 하단에 css 코드를 넣었다.)

```
<div>
  <div>
    <div class="box red">red</div>
  </div>
    <div>
    <div class="box blue">blue</div>
  </div>
  <div>
    <div class="box green">green</div>
  </div>
</div>

.box {
  width: 100px;
  height: 100px;
  text-align: center;
  line-height: 100px;
  color: #fff;
}
.red {
  background: red;
  position: absolute;
  top: 30px;
  left: 30px;
  z-index: 1;
}
.blue {
  background: blue;
  position: absolute;
  top: 60px;
  left: 60px;
  z-index: -1;
}
.green {
  background: green;
}
```

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/2a52a029-141b-4d54-be16-9aaceb882df7"></td>
        </tr>
</table>

위 이미지와 같은 결과물을 볼 수 있다. HTML 상으로 red는 green 보다 먼저 선언 되었기 때문에, 원래 대로라면 green의 뒤에 깔려야 하는게 정상이다. 하지만 z-index값을 1로 주었기 때문에 z-index가 적용되지 않은 green의 위로 올라와 있다.

이처럼 요소를 쌓듯이 가상의 z축을 기준으로 순서를 결정하는 것이 `z-index`이다. z-index의 순서를 정리하면 아래와 같다.

![](https://velog.velcdn.com/images/rlwjd31/post/1ffda778-f23f-4a30-acb4-645e1eed7ab3/image.png)

> 1. root 요소(최상위 요소 - `<html>`)
> 2. position 속성이 설정되고 z-index가 음수인 값
> 3. position 속성과 floated 속성이 설정되지 않은 block level 값
> 4. position 속성이 설정되지 않으며, floated 속성이 설정된 floated 값
> 5. position 속성이 설정되지 않은 inline 값
> 6. position 속성이 설정되고 z-index가 0이거나 auto인 값
> 7. position 속성이 설정되고 z-index가 양수인 값

</br>

### 문제

해당 문제는 [이 아티클](https://philipwalton.com/articles/what-no-one-told-you-about-z-index/?source=post_page-----172f9bd1af8b--------------------------------)에 첨부된 문제를 한국어로 번역한 것이다.
</br>

3개의 `div` 에 `span` 으로 각각 `background-color`를 적용하였고, `position`은 `absolute`이다. red 는 `z-index`가 1이며 나머지는 없다. 여기서 아래 조건을 지키면서 red를 green 과 blue 뒤로 보내는 방법은?

- HTML markup 변경 금지
- z-index 추가하거나 변경 금지
- position 속성을 추가하거나 변경 금지

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/5bc42599-cafd-4d9d-afbe-26cc7aa06dcb"></td>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/de06514a-8572-41d0-af45-879581bb42f2"></td>
        </tr>
</table>

이미지로 보면, 좌측 이미지를 우측 이미지처럼 수정하면 된다.

</br>

사실 나는 이 문제를 z-index을 변경하지 않고서 풀이하는 방법을 몰랐다. 정답을 먼저 공개하면,

> 답은 red를 감싸는 div에 opacity를 1보다 작은 값(ex. 0.99)로 적용한다.

이유는 red를 감싸고 있는 div에 **stacking context**가 생성되었기 때문이다. span을 감싸고 있는 각 div들은 서로 형제 요소이며、 position이 설정 되어 있지 않았고, z-index가 적용되어 있지 않았다. 때문에 서로는 순서대로 나타나며 HTML상 가장 하단에 있는 blue를 감싸고 있는 div(3번째)가 가장 높이 올라와 보일 것이다.

즉, 다시 말하면 3개의 div 모두 stacking context가 root 요소인 `<html>` 이다. 그렇다면 span도 전부 root 요소의 stacking context 그룹에 속해있을 것이다. 그렇다면, red, green, blue(span 요소들) 모두 여전히 root 요소의 stacking context 안에 속해있다. 그렇기 때문에 계층과 상관 없이 z-index가 적용이 된 것이다.

여기서 red의 div에 opacity: 0.99를 주었을 경우 red의 div에 **새로운 stacking context가 생성**되었다. 기존 root의 stacking context에 자식 요소 하나가 새로운 stacking context 그룹을 생성했다.

red는 이제 **root의 stacking context의 영향을 벗어나 red를 감싸는 div의 stacking context에 영향을 받을 것**이다. 새롭게 만들어진 stacking context는 그 안에 있는 요소끼리의 z-index가 적용될 것이다.

### stacking context란?

그렇다면 stacking context가 무엇이길래 어느 요소는 생성되고, 어느 요소는 생성되지 않는 것일까?

stacking context(쌓임 맥락)란 가상의 Z 축을 사용한 HTML 요소의 3차원 개념화이다.

위에서 알 수 있듯 특정 요소의 렌더링 순서는 자신의 z-index 속성 값에 의해 결정된다. 이는 그 요소들이 가진 속성으로 인해 쌓임 맥락이 생성되기 때문이다.

쌓임 맥락은 대표적으로 아래 조건을 만족할 경우 생성된다.

> - opacity 속성이 1보다 작은 요소
> - position 속성이 fixed 또는 sticky인 요소 (이 경우에 z-index가 따로 필요하지 않음)
> - position 속성이 relative 또는 absolute 이면서, z-index 값이 auto가 아닌 요소
> - 값이 none이 아닌 아래의 css 요소
>   - transform
>   - filter
>   - backdrop-filter
>   - perspective
>   - clip-path
>   - mask, mask-image, mask-border
> - z-index 속성이 auto가 아니면서 flex 컨테이너의 자식 요소
> - z-index 속성이 auto가 아니면서 grid 컨테이너의 자식 요소

대표적인 조건이며, [MDN](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context#%EC%84%A4%EB%AA%85)에서 더 자세한 내용을 확인할 수 있다.

특히 이 중에서도 2,3번째가 눈에 띄었다. z-index는 position 속성이 static이 아닌 것으로 설정되어야만 하는데, flex나 grid가 적용된 자식들은 `position: static`이므로 z-index를 준다고 적용되는 것이 아니어야 한다. 하지만, mdn에서 정의한 위의 조건들을 기반으로 하면 flex container내의 자식 요소에게 z-index를 주면 stacking context가 생성되어 z-order를 적용할 수 있다는 것을 확인할 수 있다.

</br>

### 쌓임 맥락 내부의 자식 요소들의 특징

- 쌓임 맥락이 생성된 요소의 자식 요소는 쌓임 맥락 생성 규칙 및 z-index 규칙에 의해 동일하게 쌓인다.
- **쌓임 맥락 내부에 또 쌓임 맥락이 포함 될 수 있다.** 따라서 쌓임 맥락은 계층 구조를 이룬다.
- HTML의 모든 요소가 쌓임 맥락을 가지는 것은 아니므로, 쌓임 맥락 계층구조는 HTML 요소 계층구조의 부분집합이다.
- **자식의 z-index 값은 부모 요소 내부에서만 의미 있다.**
- 자신의 쌓임 맥락을 만들지 않을 경우, 부모의 쌓임 맥락에 의해 동화 된다.

특히 **자식의 z-index 값은 부모 요소 내부에서만 의미 있다.** 가 개발할 때 가장 유의해야 하는 내용인 것 같다. 물론 미리 DOM 구조 설계부터 탄탄하게 만들고나서 구현하는 것이 가장 좋은 방법이겠지만, 나처럼 감자 개발자의 경우 `z-index : 100`을 줘도 요소가 앞으로 이동하지 않는다면 상위 요소가 stacking context를 형성한 건 아닌지 살펴봐야겠다.

</br>

## ⚡마무리

사실 앱잼할 때 `z-index`가 적용 안 된 경험이 있던 것 같기도 하다. 그때는 이런 게 있는 줄 모르고, 어찌저찌 해결했는데 이런 상세한 내용이 있다는 게 신기했다. 나한테 z-index는 그냥 1000 때리고 코드리뷰 받을 때 혼나는 친구... 정도였는데 css 속성도 알수록 신기한 것들이 많아서 확실히 깊이 아는 게 중요하다는 걸 느꼈다.

</br>

---

참고 자료
</br>
[MDN 쌓임 맥락](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_positioned_layout/Understanding_z-index/Stacking_context)
</br>
[z-index & stacking context](https://velog.io/@rlwjd31/z-index-stacking-context)
</br>
[\[CSS\] Stacking context](https://dongmin-jang.medium.com/css-stacking-context-172f9bd1af8b)
</br>
[\[CSS\] z-index와 쌓임 맥락 (stacking context)](https://velog.io/@moonheekim0118/CSSz-index%EC%99%80-%EC%8C%93%EC%9E%84-%EB%A7%A5%EB%9D%BD-stacking-context)
</br>
[왜 내 z-index는 먹히지 않는가?](https://pozafly.github.io/css/stacking-context/)
