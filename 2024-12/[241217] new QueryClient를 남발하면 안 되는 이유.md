# [241217] new QueryClient를 남발하면 안 되는 이유

</br>

## 🤔 문제 상황

![image](https://github.com/user-attachments/assets/cf66f63f-5690-4085-87bd-f7294cd6330a)

[예매자 관리 페이지 기능 구현 PR](https://github.com/TEAM-BEAT/BEAT-Client/pull/454/files/77a69ff895394217b0ba019b88318bbc1bf8c234#diff-913a066974f483af8c05232d4a4e16f7bfeecb291a4424a351b62a1f7672f7d5)에서 위와 같은 코드 리뷰를 채현 언니에게 받았다.

나는 모든 query에서 `const queryClient = new QueryClient();`를 이용하여 새로운 QueryClient를 생성하고 있었고, 이에 대해 코드리뷰를 받았다.

이때까지만 해도 왜 새로운 QueryClient를 생성하면 안 되는지에 대해 잘 몰랐고, 급하게 코드 리뷰를 처리하며 `useQueryClient()`를 이용하면 새로 생성하지 않아도 되는 거구나~ 정도만 이해하고 넘어갔는데 오늘 자세한 이유를 파헤쳐보고자 한다!

</br>

## 🍀 Today I Learn

### QueryClient

우선 QueryClient가 하는 일을 먼저 알아보자. QueryClient는 간단하게 말하면 캐시와 상호작용 할 때 사용된다.

단순하게 표현하면, QueryCache와 MutationCache를 담는 그릇이 QueryClient인 셈이다. 또한 QueryCache에 접근하기 위해서는 직접 접근하는 방식보다 QueryClient를 이용하는 방식을 사용한다.

```javascript
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
...

const queryClient = new QueryClient();

function Root() {
  return (
        <QueryClientProvider client={queryClient}>
            <App />
        </QueryClientProvider>
  );
}
```

일반적으로 앱의 최상단에서 `QueryClientProvider`를 이용하여 감싸준다. 이는 앱 전체에서 우리가 최상단에서 생성한 queryClient에 접근할 수 있도록 하기 위함이다.

BEAT도 위와 같은 방식으로 `App.tsx`에서 QueryClient를 생성해 주고, 하단 컴포넌트들에서 불러오는 방식으로 진행되고 있다.

</br>

### useQueryClient

그렇다면 왜 new QueryClient가 아닌 useQueryClient를 사용해야 할까?

- 컴포넌트 내부에서 매번 new QueryClient를 사용하여 새로운 인스턴스를 생성하면 **데이터의 일관성과 관리가 어려워**진다. 때문에 컴포넌트가 리렌더링 될 때마다 새로운 인스턴스가 생성되어 데이터의 불일치 문제를 초래할 수 있다.

- 계속해서 새 인스턴스를 생성하면 **비효율적이며 메모리 누수 가능성을 초래**할 수 있다. 대부분의 개발이 그렇지만... 같은 것을 계속 사용할 수 있음에도 새로 생성하면 메모리 누수를 일으킨다.

때문에 useQueryClient를 사용하면 전역적인 데이터 일관성을 유지하고, 중복 인스턴스 생성을 방지하여 애플리케이션의 성능과 데이터 관리를 향상시키는데 도움이 된다. 단지 도움이 될 뿐만 아니라 계속해서 새로운 QueryClient를 생성하는 것은 데이터 불일치 문제를 초래할 수 있다!!

</br>

## ⚡마무리

ReactQuery의 경우 데이터를 효율적으로 통신하고 캐싱하기 위해서 사용하는데... 사용법이 어렵기도 하고 더더 효율적으로 통신하기 위해서 공부해야 할 게 많다 ㅠㅠ 사실 지금도 제대로 알고 사용하고 있는 건가 싶을 때가 많아서 앞으로 더 공부해야겠다!!

</br>

참고 자료
</br>
[Tanstack Query Dosc - QueryClient](https://tanstack.com/query/v5/docs/reference/QueryClient)
</br>
[리액트쿼리(v3) - QueryClient 클래스](https://velog.io/@hwisaac/%EB%A6%AC%EC%95%A1%ED%8A%B8%EC%BF%BC%EB%A6%ACv3-QueryClient-%ED%81%B4%EB%9E%98%EC%8A%A4)
</br>
[왜 컴포넌트 안에서 new QueryClient 사용을 지양해야 할까?](https://toby2009.tistory.com/54)
