# [241127] css grid

</br>

## 🤔 문제 상황

![image](https://github.com/user-attachments/assets/d913df71-82a6-43ef-b7ad-b195d67d6d00)

이전에 업로드한 [TIL](https://github.com/sinji2102/TIL/blob/main/2024-11/%5B241126%5D%20React%EC%97%90%EC%84%9C%20%EC%97%AC%EB%9F%AC%20%EA%B0%9C%EC%9D%98%20%EC%B2%B4%ED%81%AC%EB%B0%95%EC%8A%A4%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0.md)에서 지원 님이 위와 같은 댓글을 남겨 주셨다. 문제가 된 부분은 내가 여러 개의 체크 박스를 구현하는 부분이었는데, 해당 부분에서 2열로 된 체크 박스를 구현하며 flex만을 이용하기 위해 한 개의 배열을 이중 배열으로 설정해 주었다. (예를 들어 6회차면 `[[1,2], [3,4], [5,6]]` 이런 식으로)

지원 님의 의견처럼 1차원 배열을 유지하고, 특히 `데이터를 가공하지 않아도 된다는 점`, 스타일 제어가 쉽다는 점에서 무조건 이중 배열로 해결하기보단 grid를 적용하는 게 이득이라고 생각했다.

댓글에도 남겼지만, 3열, 4열의 경우 자연스레 grid를 사용하는데... 자꾸만 2열에는 사용하지 않아버릇하는 게 내가 아직 grid에 익숙하지 않아 뇌에서 회피하는 것 같아서 이번 기회에 grid를 알아보고자 한다!

</br>

## 🍀 Today I Learn

### flex vs grid

아이템을 자유롭게 배치할 수 있는 대표적인 css layout에는 flex와 grid가 있다.

- flex: **1차원적**으로 배치할 수 있게 도와줌
  - 가로 또는 세로 줄로 연결해 배치
- grid: **2차원적**으로 배치할 수 있게 도와줌
  - 가로 축 & 세로 축

![image](https://github.com/user-attachments/assets/924baaa5-c653-402b-8988-b401f166895a)

### grid의 요소

부모 컨테이너에서 `display: grid;`를 지정해주면 부모는 `grid container`,자식 요소는 `grid cell`이 된다.

#### grid container

- `grid-template-columns`: 가로 축에 총 몇 개의 column을 둘지 또 각 column의 사이즈를 정의
- `grid-template-rows`: 세로 축에 총 몇 개의 row를 둘지 또 각 row의 사이즈를 정의

</br>

- `grid-auto-columns`: 셀의 너비를 잡아줌(이를 정하지 않으면 column이 콘텐츠에 따라 늘어남)
- `grid-auto-rows`: 셀의 높이를 잡아줌(이를 정하지 않으면 row가 콘텐츠에 따라 늘어남)

</br>

- `grid-gap`: 각 그리드 셀마다 얼마의 gap을 가질지 정의
- `grid-column-gap` : 그리드의 가로 축 gap 지정
- `grid-row-gap` : 그리드의 세로 축 gap 지정

</br>

#### grid cell

- `grid-column-start`, `grid-column-end` : 가로 몇 번째 셀부터 몇 번째 셀까지 보여질지 정의
- `grid-row-start`, `grid-row-end` : 세로 몇 번째 셀부터 몇 번째 셀까지 보여질지 정의
- `grid-area`: 어떤 영역에 표시되길 원하는지 정의

### 사용 예시

![image](https://velog.velcdn.com/images/ejaman/post/33708526-b686-408b-89cf-d6f60552632a/image.png)

```css
display: grid;
```

당연한 이야기지만, `display: grid`만 적용하면 아무 일도 일어나지 않는다... 그냥 그리드를 적용해 준 사람이 될 뿐이다.

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://velog.velcdn.com/images/ejaman/post/c72bef02-288f-4a6f-8052-de17f1ee24b5/image.png"></td>
        </tr>
</table>

```css
display: grid;
grid-template-columns: 100px 100px 100px;
```

colunm을 지정해 주면, 어떤 크기로 몇 개를 반복해 줄 건지 지정해 줄 수 있다.

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://velog.velcdn.com/images/ejaman/post/9c1e49ed-3a4f-4bc6-a2c7-4927ca56308e/image.png"></td>
        </tr>
</table>

```css
display: grid;
grid-template-columns: 100px 100px 100px;
grid-template-rows: 100px 200px 100px;
```

row도 함께 지정해 줄 수 있다. 위의 경우, 4줄이 들어가야 하는데 row에 3줄까지만 지정해 주어 4번째 줄의 값인 10은 기본값으로 나타난다.

#### repeat

내가 개인적으로 grid에서 가장... 쩐다고 생각하는 부분이다. 밑에 후술하겠지만 특히 `fr`!!

위와 같은 모양을 만들기 위해서는,

```css
display: grid;
grid-template-columns: repeat(3, 100px);
grid-template-rows: 100px 200px 100px;
```

위와 같은 코드로 대체해 줄 수 있다. `columns`가 의미하는 건 **100px을 3번 반복하겠다는 뜻**이다.

그리고 무려 아래 세 코드는 같은 결과물을 의미한다. 물론 컨벤션에 맞추어 타인이 이해하기 쉽게 사용하는 것도 중요하겠지만, 나는 일단 선택지가 많다는 점에서 좋아한다.

```css
grid-template-columns: 100px 100px 100px;
grid-template-columns: repeat(3, 100px);
grid-template-columns: repeat(2, 100px) 100px;
```

#### gap

당연히 grid에도 gap을 줄 수 있다.

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://velog.velcdn.com/images/ejaman/post/85b14b33-daea-4cf3-8f91-191faf91fb3c/image.png"></td>
        </tr>
</table>

```css
grid-gap: 10px;
```

위처럼 행/열 모두 같은 gap을 줄 수도 있지만,

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://velog.velcdn.com/images/ejaman/post/08d576c7-b42a-4acc-8c40-70f9f0798426/image.png"></td>
            <td><img width="308" alt="image" src="https://velog.velcdn.com/images/ejaman/post/2275a006-00b1-4323-8915-902c995c5129/image.png"></td>
        </tr>
</table>

```css
/* 좌측 */
grid-column-gap: 10px;

/* 우측 */
grid-row-gap: 10px;
```

위와 같이 행/열에 다른 gap값을 줄 수도 있다.

</br>

#### 반응형

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://velog.velcdn.com/images/ejaman/post/d78f34e7-e675-4877-b51e-4d9c4ee3f52a/image.png"></td>
        </tr>
</table>

```css
/* 5개씩 나누어 20%를 할당하기 */
grid-template-columns: repeat(5, 20%);
/* 5개씩 나누어 한 화면을 자동으로 나눠 할당하기 */
grid-template-columns: repeat(5, 1fr);
```

개인적으로 grid에서 레전드라고 생각하는 기능... 반응형을 위해서 화면을 나눠서 분할할 수 있다. 특히 fr(fractional unit)의 경우 나눈 값을 1씩 할당하라는 의미이다. 주의해야 할 점은, **fr은 퍼센트 값과 달리 길이가 아니기 때문에 calc()로 다른 값과 함께 처리할 수 없다.**

길이가 몇 개가 될 지 모를 때 지정하는 방법도 있다.

```css
grid-auto-rows: 150px;
```

위 코드는 column에 따라 row가 몇 줄이 될지 모르지만, 한 줄의 높이를 150px로 만들고 싶을 때 사용한다. 그런데 이때, 내부 텍스트가 많아서 150px을 넘칠 것 같다면

```css
grid-auto-rows: minmax(150px, auto);
```

위와 같이 최소 150px를 보장하지만, 넘치면 자동으로 길이를 조정할 수 있게끔도 할 수 있다.
**첫 번째 인수는 최솟값**이고, **두 번째 인수는 최댓값**으로 자유롭게 최대/최소값을 조정할 수 있다.

</br>

## ⚡마무리

개인적으로 `개발엔 정답이 없다`는 말을 좋아하는 편인데, 내가 저번에 구현한 건 오답이었다고 생각한다... ㅠㅠ 정답은 없어도 오답은 있다는 점이 개발의 어려운 점인 것 같다... 😢 트러블슈팅도 좋지만, 종종 css처럼 기본적인 내용을 공부하는 것도 좋은 것 같다!

코드까지 꼼꼼하게 읽어주시고 놓친 부분 지적해 주신 지원 님께 감사드립니다... 🫶🍀

그리고 아무도 안 궁금하겠지만... 변경된 commit 링크 남기고 갑니다... 확실히 깔끔해진 코드를 확인해볼 수 있어요!
[style: 필터바텀시트 style flex -> grid 변경](https://github.com/TEAM-BEAT/BEAT-Client/pull/452/commits/78c31226eb4175030879fe8d29bcc0d8d598bb2f)
</br>

---

참고 자료
</br>
[MDN Gird](https://developer.mozilla.org/en-US/docs/Web/CSS/grid)
</br>
[\[CSS\] 📚 그리드(Grid) 💯 총정리](https://inpa.tistory.com/entry/CSS-%F0%9F%93%9A-%EA%B7%B8%EB%A6%AC%EB%93%9CGrid-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
</br>
[\[CSS\] Grid 그리드 사용 아카이브](https://velog.io/@ejaman/CSS-Grid-%EA%B7%B8%EB%A6%AC%EB%93%9C-%EC%82%AC%EC%9A%A9-%EC%95%84%EC%B9%B4%EC%9D%B4%EB%B8%8C)
