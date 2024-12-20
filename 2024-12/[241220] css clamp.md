# [241220] css clamp 함수

</br>

## 🤔 문제 상황

최근에 깃허브 피드에 뜨는 PR을 염탐하다가... 페이지를 반응형 구현하는데 clamp를 사용하는 것을 봤다. 처음 보는 css 속성이라 신기해서 찾아보았다!

</br>

## 🍀 Today I Learn

기본적으로 반응형 작업을 할 때 min, max를 사용해왔다. 대신 css의 clamp 함수를 사용하면 기준값과 최대값, 최솟값을 정의해 줄 수 있다.

> **clamp(최소값, 가변값, 최대값)**

예를 들어,
`font-size: clamp(12px, 2.5vw, 18px);`의 경우 기본적으로 폰트사이즈는 2.5vw를 따라가되,
12px 보다 작아지지 않고 18px 보다 커지지 않는다. 12~18px 내의 값만 가지게 되는 것이다.

사실 이건 그냥 min, max를 사용해도 되는 거 아닌가? 싶긴 하다. 그런데 내가 신기했던 기능은 바로

```css
/* width 제외한 내용은 제거 */

.parent {
  width: 300px;
}

.target {
  width: clamp(50%, 300px, 70%);
}
```

위처럼 사용되는 경우이다.

<img width="386" alt="image" src="https://github.com/user-attachments/assets/101a0b56-fd25-4abc-936c-818e3c63d895" />

만약 parent의 너비가 300인 경우에, `clamp(150px, 300px, 210px)`로 적용되어 210으로 계산된다.

<img width="372" alt="image" src="https://github.com/user-attachments/assets/ef8287be-761c-47f8-839c-e856af7856e1" />

parent의 너비가 650인 경우에, `clamp(325px, 300px, 455px)`로 적용되어 325으 로 계산된다.

이처럼 일정 크기에서는 내부 요소의 크기를 유지하고, 더 커지거나 작아졌을 때 그에 맞추에 크기를 유지할 때 유용하게 사용할 수 있을 것 같다.

위의 코드는 [codepen](https://codepen.io/kumjungmin/pen/QWQxPqj)에서 확인해볼 수 있다.

</br>

## ⚡마무리

사실 반응형 처리를 제대로 해본 경험이 없는데... 반응형 처리를 위해서는 디자인 측에서도 레이아웃을 잡아줘야 해서 쉬운 일은 아닌 것 같다 ㅠㅠ 비트 데스크탑 버전을 작업하게 된다면 반응형 작업을 해보는 것도 좋을 것 같다!

</br>

참고 자료
</br>
[\[css\] clamp() 함수를 아시나요](https://velog.io/@sh0521/css-clamp)
</br>
[\[CSS\] 반응형 웹 구현에 좋은 min(), max(), clamp()](https://mong-blog.tistory.com/entry/CSS-%EB%B0%98%EC%9D%91%ED%98%95-%EC%9B%B9-%EA%B5%AC%ED%98%84%EC%97%90-%EC%A2%8B%EC%9D%80-min-max-clamp)
