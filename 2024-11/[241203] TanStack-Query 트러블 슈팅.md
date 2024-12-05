# [241203] TanStack-Query 트러블 슈팅

</br>

## 🤔 문제 상황

![](https://github.com/user-attachments/assets/ce05b0a3-9cf7-4d08-abd9-9a1d0a571660)

어제 새벽에 오류를 해결하면서... 레전드 오류가 발생했다 ㅠㅠ
바로 처음에는 잘 불러오는데 뒤로 돌아갔다 다시 들어오면 Data를 못 받아오는 오류가 있었다!! ㅠㅠ

처음에는 뭐지 싶었는데 다시 요청할 때만 못 불러오는 걸 보니까 query 문제라는 생각이 들었고, 도영핑... ㅠㅠ 을 불러 query가 어떻게 세팅되어 있는지를 확인했다!!

</br>

## 🍀 Today I Learn

### 문제 원인과 해결법

문제의 원인은 바로 레전드 바보 이슈...

```JavaScript
<S.ManageCardList>
                {paymentData.map((item) => {
                  const date = item.createdAt.split("T")[0];
                  const formattedDate = `${date.replace(/-/g, ". ")}`;
                  const bookingStatus = convertingBookingStatus(item.bookingStatus as PaymentType);

... 이후 코드
```

문제의 원인은 `{paymentData.map((item) => {`의 `paymentData`에 옵셔널 체이닝을 사용하지 않았기 때문이었다... 레전드 바보 이슈 ㅠㅠ

그렇다면 옵셔널 체이닝이 없는 경우 실행되지 않는 이유가 뭘까?

```JavaScript
export const useTicketRetrive = (formData: getTicketReq, filterList) => {
  return useQuery({
    queryKey: [QUERY_KEY.LIST, BOOKING_QUERY_KEY.BOOKING_LIST],
    queryFn: () => getTicketRetrieve(formData, filterList),
    gcTime: 1000 * 60 * 60 * 24,
  });
};
```

위 코드가 위의 paymentData를 가져오는 api 쿼리 코드이다. 위를 보면 gcTime이 설정된 걸 알 수 있는데, `?`를 붙이기 전에 찾은 해결책은 `gcTime`을 0초로 설정하는 것이었다.

![](https://github.com/user-attachments/assets/ce05b0a3-9cf7-4d08-abd9-9a1d0a571660)

위 사진을 보면, 데이터가 Inactive에 위치한 걸 볼 수 있다. 그렇다면 처음에 요청했을 때는 어떤지 확인해보자!

그렇다면 왜 처음에는 잘 불러와졌을까?

![](https://github.com/user-attachments/assets/2c5c97b2-b8b2-4186-8036-b15e90e70b8f)

처음에 요청했을 때 데이터의 상태이다. 도영 오빠와 열심히 디버깅해본 결과, 처음 요청 시에는 `isLoading=true` 상태이다가 데이터가 들어오며 `isLoading=false` 상태가 되는 것을 확인할 수 있었다.

그리고 재시도 했을 경우에는 `isLoading=false` 상태로 시작되는 것을 확인할 수 있다.

때문에 gcTime이 끝나지 않은 상태에서 데이터를 재요청했을 때, `isLoading`이 `false`로 설정되며 화면에 바로 데이터를 보여주고자 한 것이 문제였다.

</br>

### ?(Optional Chaining)의 역할

위를 통해 알 수 있는 문제 원인은 `paymentData`가 초기값 undefined에서 업데이트되지 않는다는 것이다. `paymentData`는 컴포넌트 로드 시 API 호출 결과를 기반으로 설정된다.

하지만 gcTime이 만료되면 React Query는 데이터를 삭제하고 isLoading을 true로 설정된다. 데이터가 삭제된 상태에서 다시 컴포넌트를 로드하면 `paymentData`는 `undefined` 상태가 되고, 이후 로직에서 데이터 로드가 멈추게 된다.

 </br>

이때 **?(Optional Chaining)** 은 무슨 역할을 할까?

`paymentData` 뒤에 ?를 사용하게 되면 `paymentData`가 `undefined`여도 코드 실행이 멈추지 않고 안전하게 처리된다.

이는 옵셔널 체이닝이 없다면 `paymentData`가 `undefined` 상태에서 map 호출 시 에러를 바로 반환하게 되지만, 옵셔널 체이닝이 있다면 map이 실행되지 않고 `undefined`로 설정되며 data가 다시 로드될 기회를 얻는다는 의미이다.

결론적으로 옵셔널 체이닝 덕분에 데이터가 없는 상태 `undefined`에서도 코드가 중단되지 않아 React Query의 로드 과정이 정상적으로 완료되었으며, 데이터가 다시 setPaymentData로 설정되며 화면에 업데이트되었다.

</br>

## ⚡마무리

TanStack-Query 공부를 솔직히!! 도영 오빠가 해오라는 부분이랑 이것저것 하면서 익힌 거 말고는 깊게 공부해 본 적이 없는데..... 앱잼 staleTime 이슈 이후로 TanStack-Query를 좀 공부해야겠다는 생각이 드는 밤이었다...

그리고 옵셔널체이닝 빼먹은 거 진짜 레전드 바보 이슈... 도영띠예 죄송해요

</br>
