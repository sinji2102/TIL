# [241113] React resize 이벤트

</br>

## 🤔 문제 상황

<table align="center">
        <tr align="center">
            <th>하단바 있을 때</th>
            <th>하단바 없을 때</th>
        </tr>
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/38fa1f33-68b8-4980-838a-818327d26d71"></td>
            <td><img width="308" alt="image" src="https://github.com/user-attachments/assets/922e27f5-4e6e-4bb8-a7b6-7a9b7f02bd38"></td>
        </tr>
    </table>



위 이미지처럼, 사파리 자체 하단바까지 뷰포트로 계산해 사이드바 호출 시 하단이 잘리는 문제가 발견됐다.

사실 기본 세팅은 하단바는 뷰포트로 계산하지 않는 게 기본인데, 이로 인해서 로그아웃 버튼이 하단바 아래로 사라지는 이슈가 있어서 [100vh로 맞추기](https://velog.io/@eunddodi/React-%EB%AA%A8%EB%B0%94%EC%9D%BC-%EC%9B%B9-%EC%95%B1-100vh-%EC%8B%A4%EC%A0%9C-%ED%99%94%EB%A9%B4-%ED%81%AC%EA%B8%B0%EB%A1%9C-%EB%A7%9E%EC%B6%94%EA%B8%B0) 아티클을 참고해 수정했다. 

이랬더니 아예 하단바를 뷰포트에 포함하지 않아 사이드바가 잘려서 나와버리는 이슈가 발생했고... 

![image](https://github.com/user-attachments/assets/d1f3cfca-43d4-4eb5-8e03-96e235887a7a)

이런 댓글을 발견했고 react resize 이벤트에 대해 찾아봤다!! 

</br>

## 🍀 Today I Learn
[[ Bug ] 사이드바 하단 잘리는 버그 수정 #436](https://github.com/TEAM-BEAT/BEAT-Client/issues/436)
일단 BEAT의 해당 이슈를 해결하기 위해 공부했으며, [[React] resize 이벤트로 브라우저 크기에 따라 javascript 적용하기](https://divheer.tistory.com/247)를 토대로 공부했다.

### resize 이벤트
```
const [width, setWidth] = useState(window.innerWidth);

const handleResize = () => {
  setWidth(window.innerWidth);
};

useEffect(() => {
    window.addEventListener("resize", handleResize);
    return () => {
        // cleanup
        window.removeEventListener("resize", handleResize);
    };
}, []);
```

- resize 이벤트는 **document view**의 크기가 변경될 때 발생한다.
- **state**를 생성해서 resize 이벤트가 발생할 때마다 `window.innerWidth`로 setstate를 한다.
- `useEffect`의 `deps`에 빈 배열을 전달하여, 컴포넌트가 언마운트 될 때 **cleanup** 함수를 실행한다.

### debounce
위처럼 resize 이벤트를 이용해도 되지만, 브라우저 크기는 연속적으로 브라우저가 1px라도 변할 때마다 이벤트가 호출되는 걸 방지하기 위해 가장 마지막으로 호출된 것만 실행시켜야 한다. 

이를 이위해 연속해서 호출되는 함수 중 가장 마지막으로 호출된 것만 실행시키는 debounce를 이용한다.

```
const handleResize = debounce(() => {
    setWidth(window.innerWidth);
}, 200);
```

debounce는 검색창에 한 글자 한 글자 입력할 때마다 api 요청을 보내는 것을 막을 때, 사용자가 실수로 요청을 보내는 버튼을 두세번 눌렀을 때 등에 유용하게 사용할 수 있다.
-> 어디 개발자도구 열었는데 한 글자 입력할 때마다 api 요청 보내는 거 보고 기겁한 적 있는데... 이런 해결 방법이 있었다는 것도 오늘 알았다! 이번에 BEAT 스프린트에 검색 들어가는 거로 아는데 유용하게 사용해봐야겠다... 🫶

</br>

## ⚡ 마무리
오늘 BEAT 코드에 적용해보진 못했는데, 이슈 파두었으니 내일 내로 적용해야겠다! 내일 TIL은 적용하면서 이슈 생기면 관련 내용 쓰고, 오늘 Debounce 공부하면서 Throttle도 살짝 알게 되어서 공부해보고 적어보려고 한다. [Debounce/Throttle](https://velog.io/@sjoleee_/debounce-throttle-%EC%A0%95%EB%A6%AC)

</br>

--- 
참고 자료
[[React] 브라우저 크기 감지, throttle, debounce](https://9yujin.tistory.com/20)
[MDN resize](https://developer.mozilla.org/ko/docs/Web/API/Window/resize_event)
