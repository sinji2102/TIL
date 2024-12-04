# [241204] useEffect의 실행 시점

</br>

## 🤔 문제 상황

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/1adb286c-1718-400b-9ec9-036adfc0854d"></td>
        </tr>
</table>

채현 언니 [예매 취소 PR](https://github.com/TEAM-BEAT/BEAT-Client/pull/449)을 코드리뷰 하는 과정에서, 도영 오빠의 코드리뷰를 봤다. 문제가 된 부분은 아래 코드이다.

```JavaScript
... 상단 코드 생략

  const { state } = useLocation();
  const { confirmCancelAction } = useCancelBooking(state.bookerName, state.number, state.password);

  const performanceDateArray = state.bookingDetails.performanceDate.split("-");
  const performanceDataDate = performanceDateArray[2].split("T");

  useEffect(() => {
    if (!state) {
      openAlert({
        title: "잘못된 접근입니다.",
        subTitle: "이전 페이지로 이동합니다.",
        okText: "확인",
        okCallback: () => navigate(-1),
      });
    }
  }, [state, openAlert, navigate]);

... 하단 코드 생략
```

사실 도영 오빠의 코드 리뷰를 보고 `useEffect`가 사용되고 있지 않다는 사실을 알았고, 나도 이유를 모르겠어서 이번 기회에 공부해보고자 했다.

그리고 코드만 보고 실행 안 될 것 같다고 판단하고 url로 접속해 볼 생각한 레개도....... 우리리드머싯따

</br>

## 🍀 Today I Learn

일단 useEffect가 뭔지에 대해 알아봐야 하는데, 이전에 관련해서 정리해둔 벨로그 글이 있어 참고하면 될 것 같다.

[\[React\] useEffect, cleanup, useLayoutEffect, useRef](https://velog.io/@dawnww/React-useEffect-useRef)

### useEffect의 실행 시점

그렇다면 useEffect의 실행 시점은 어떻게 될까? 결론부터 말하자면, useEffect는 컴포넌트가 렌더링된 이후에 실행된다.

사실 헷갈릴 수도 있다고 생각한 게, 많은 글이

> useEffect는 컴포넌트가 mount/unmount/update 됐을 때 실행된다.

라고 정리되어 있었다. 물론 과거형이긴 하지만, 글을 대충 읽었다면 컴포넌트가 렌더링 되면서 실행된다고 오해할 수도 있다고 생각했다. 물론 대충 읽은 사람의 잘못이지만!!

아무튼 useEffect는 **컴포넌트의 렌더링과 동시에 일어나는 게 아니라 렌더링 된 후에 실행된다.**

</br>

```JavaScript
const Test = () => {
  console.log("렌더링 시작!");

  useEffect(() => {
    console.log("useEffect");
  }, []);

  return (
    <Container>
      {console.log("컴포넌트 안!")}
    </Container>
  )
}
```

위와 같은 코드가 있다면, 실행 결과는 아래와 같다.

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/df7977c9-f559-45d4-a8b6-f3f6caf453a1"></td>
        </tr>
</table>

return 문 상단의 콘솔이 실행되고, 컴포넌트가 렌더링 되는 return문 내의 콘솔이 실행된 후에야 useEffect가 실행되는 것을 확인할 수 있다.

</br>

### React Life-Cycle

~~[라이프서클](https://github.com/2024-WINK-HACKATHON-TEAM2/WINK-HACKATHON-TEAM2-Client)이 생각나는 이름......... 해커톤은 하루엿지만 라이프서클 팀 하루동안 사랑햇따.~~

uesEffect의 실행 시기는 리액트 라이프사이클을 이해하고 있으면 더 쉽게 이해할 수 있다. 리액트 라이프사이클이란?

> **생명주기 메서드**로, 컴포넌트가 생성, 업데이트, 소멸되는 과정을 단계적으로 관리하고 제어하는 방법

컴포넌트가 브라우저에서 초기 렌더링, 재렌더링, 제거 이렇게 각 시점에서 실행하고 싶은 행위를 설정하기 위해서 이용한다.

간단하게 말하면, 브라우저 화면에서 보여지는 것을 기준으로, 특정작업을 실행할 시점을 결정하는 것이다.

</br>

React는 컴포넌트들에 대한 생명주기 메서드가 존재하는데 이는 클래스형 컴포넌트에만 사용할 수 있다.

-> 생명주기 메서드를 함수형 컴포넌트에서도 다룰 수 있도록 하는 것이 useEffect Hook이다.

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://velog.velcdn.com/images/my_suwan/post/9d81101e-c9c9-469a-82b4-bb2feacc170f/image.png"></td>
        </tr>
</table>

클래스 생명주기 메서드는 위 이미지와 같다.

</br>

#### componentDidMount

- 마운트 (Mounting): 컴포넌트가 생성되고 DOM에 추가되는 단계

이는 컴포넌트가 첫 렌더링을 완료한 후를 의미한다. 이 시점에서 컴포넌트가 이미 화면에 나타나고, 사용자와 상호작용할 준비가 된 상태에 이른다.

> - 외부 데이터를 불러와 컴포넌트의 상태를 업데이트
> - 타이머를 설정하고 주기적으로 작업을 수행
> - 이벤트 리스너를 등록하고 사용자 입력 또는 이벤트에 대응
> - 외부 API 호출과 같은 비동기 작업을 수행

#### componentDidUpdate

컴포넌트의 state나 props가 업데이트 된 상태를 의미한다. componentDidMount와 다른 점은, 처음 렌더링 된 이후가 아닌 업데이트 이후에 특정 작업을 수행하게 하는 것으로 재렌더링 된 이후에도 작업을 다시 수행하도록 한다.

> - 클래스 기반 컴포넌트에서 사용
> - 컴포넌트가 업데이트된 후에 실행되는 라이프사이클 메서드
> - 업데이트 이후에 발생한 작업을 처리하는 데 사용
> - 주로 외부 데이터의 동기화, DOM 조작, 다른 컴포넌트와의 상호 작용에 활용

#### componentWillUnMount

컴포넌트를 마운트 해제하기 직전에 호출, 즉 컴포넌트가 제거되기 직전의 상태를 의미한다.

</br>

useEffect는 componentDidMount + ComponentDidUpdate + componentWillUnMount의 역할을 수행하기 때문에 **componentDidMount**에 의해 컴포넌트가 첫번째 렌더링 된 이후에 실행되는 것이다.

</br>

### 문제 해결법

그렇다면 도영 오빠가 코드리뷰 남긴 부분은 어떻게 해결할 수 있을까?

일단 찾아본 결과, url로 접속하였을 때 useEffect가 실행되지 않는 이유는

```JavaScript
const handleCancel = (bookingId: number, totalPaymentAmount: number) => {
    if (totalPaymentAmount === 0) {
      confirmCancelAction({ bookingId });
      return;
    }

    const bookingDetails = (memberData ?? cachedData)?.find((item) => item.bookingId === bookingId);
    if (bookingDetails) {
      navigate("/cancel", { state: { ...state, bookingDetails } });
    }
  };
```

위 코드에서 cancel로 넘어오는 과정에 state를 navigate로 넘겨주었기 때문이다. url로 직접 넘겨주는 경우에는 state가 초기화되기 때문에 state가 undefined로 나타난다.

state가 undefined 또는 null인 경우에만 실행하여 url 접근 시 state가 없는 상황에서 알림창이 뜨고 이전 페이지로 이동하려는 로직이지만, URL로 접근했을 때는 state 자체가 존재하지 않으므로, 이 useEffect는 실행되지 않는 것이다. 때문에 내가 생각한 해결책은 아래와 같다

```JavaScript
const initialState = {
  bookerName: "",
  number: "",
  password: "",
  ...
};

const currentState = useLocation().state || initialState;

useEffect(() => {
  const isInitialState = JSON.stringify(currentState) === JSON.stringify(initialState);

  if (!currentState || isInitialState) {
    openAlert({
      title: "잘못된 접근입니다.",
      subTitle: "이전 페이지로 이동합니다.",
      okText: "확인",
      okCallback: () => navigate(-1),
    });
  }
}, [currentState, openAlert, navigate]);
```

위와 같이 state값이 넘어오지 않았을 경우 빈 상태로 지정하고, 빈 상태와 동일한지 확인하는 로직을 통해 검사할 수 있을 것 같다.

## ⚡마무리

도영 오빠가 준 링크를 보면서 공부했는데, 이 사람도 TIL로 쓴 글이라 뭔가 반가웠다!!

그리고 공부하다가 뭔가 이상한 기분이 들어서 찾아봤는데, 분명 예전에 React 스터디 할 때 정리한 적 있는 내용이었다... 저땐 React 완전 레전드 아기 시절이라 잘 기억 못했는지 모르겠지만... 예전에 공부해놓고 그대로 까먹어버린 게 레전드다... 벨로그에 해당 내용 있는 거 보고 진짜 깜짝 놀랐다 ㅠㅠ

<table align="center">
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/45f95ff1-d74a-4431-91b8-e2bd6767c828"></td>
        </tr>
</table>

근데 사실 알고 있었어도 외부 url로 들어오는 경우는 생각 못했을 듯!! 남의 코리 읽기는 항상 재밌다...

</br>

---

참고 자료
</br>
[\[React\] useEffect의 실행 시점](https://i-ten.tistory.com/308)
</br>
[리액트 라이프사이클과 useEffect 실행순서](https://hihiha2.tistory.com/169)
</br>
[\[React\] useEffect와 라이프사이클](https://velog.io/@my_suwan/React-useEffect%EC%99%80-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4)
