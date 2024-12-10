# [241209] openapi-typescript

</br>

## 🤔 문제 상황

![image](https://github.com/user-attachments/assets/5980dfea-3cc0-4ff3-90c6-d27ffad04898)
~~공연등록500에러는무시해주세요ㅠㅠ~~

최근 2차 스프린트를 진행하며, 스웨거 하단의 schema response들이 사라지는 문제가 발생했다!!

사실 스키마 없이도 타입을 직접 입력해도 되지만, 이미 스키마와 맞춰서 타입을 지정해둔 상태였기도 하고 api가 워낙 많아서 이미 지정해둔 타입들을 일일이 수정해 줄 자신이 없었따... 그리고 openapi-typescript가 워낙 편하기도 했고!! 게다가 나중에 response들이 등록될 경우에 다시 수정해야 해서 일을 두 번 하는 셈이 되기도 했다... 🥺

그래서 스키마를 기다리던 중 윙방에서 수민 오빠가 그건 뭐냐고 물어봤고......... 이렇게 스키마를 제공해 주는 게 당연한 일이 아니라는 걸 깨달았다....... 😢

나도 직접 설정한 것이 아닌 도영 오빠가 설정해준대로 사용하고 있었기 때문에, 이번 기회에 다음 프로젝트들을 위해 공부해보고자 한다!
</br>

## 🍀 Today I Learn

### openapi-typescript란?

[openapi-typescript npm](https://www.npmjs.com/package/openapi-typescript)
</br>
[openapi-typescript github](https://github.com/openapi-ts/openapi-typescript)

</br>

그래서 openapi-typescript란 무엇이냐면,

> Node.js를 사용하여 OpenAPI 3.0 및 3.1 스키마를 빠르게 TypeScript로 변환해주는 라이브러리이다.

즉, swagger에 등록되어 있는 schema를 기반으로 타입을 직접 정의하지 않고 자동으로 생성되도록 해 주는 라이브러리이다.

위 라이브러리를 사용하면 수동으로 타입을 선언하지 않아도 되어서 개발 리소스가 절약될 뿐만 아니라 **협업 시 같은 타입을 비슷한 이름으로 중복 선언해 나는 사고**를 줄일 수 있다.

</br>

### openapi-typescript 설치 방법

**설치**

```bash
npm i -D openapi-typescript typescript
```

**설정**

```
// tsconfig.json

{
  "compilerOptions": {
    "module": "ESNext", // or "NodeNext"
    "moduleResolution": "Bundler" // or "NodeNext"
    "noUncheckedIndexedAccess": true
  }
}
```

</br>

### openai-typescript 사용법

![image](https://github.com/user-attachments/assets/69dc4073-dca1-407d-8215-ce7d21a14e27)

검색창에 `서버주소/v3/api-docs`로 접속한 후 JSON 형태의 내용들을 복사한다. 우리 서버의 경우 위와 같은 형태로 나오는 걸 확인할 수 있었다.

위 형태로는 사용할 수 없기 때문에, JSON을 YAML로 변환해준다. 크롬에 검색하면 수두룩하다!!

변환해준 YAML을 복사하여 openapi.yaml 파일 내에 넣어준다. 원하는 경로에 넣어주되, 나는 types 폴더 아래에 넣어주었다.

```
npx openapi-typescript {yaml 경로} --output {원하는 type 파일 생성 경로}

npx openapi-typescript src/types/openapi.yaml --output src/types/index.ts
```

위와 같이 type 파일을 생성해 주는 명령어를 입력한다.

우리 팀의 경우 [index.ts 파일](https://github.com/TEAM-BEAT/BEAT-Client/blob/develop/src/typings/api/schema/index.ts)과 같이 생성되었다.

```
// api.ts
import { components } from "@typings/api/schema";
```

api 파일에 위와 같이 스키마를 import 해 주면,

![image](https://github.com/user-attachments/assets/5d420d12-a182-4405-ae02-4563708b18c7)
이제 위와 같이 schema 이름을 이용하면 IDLE에서 자동으로 타입을 추론해 주는 것을 확인할 수 있다!!

</br>

### 더 쉬운 방법

근데 아무리 찾아봐도 우리 팀은 `tsconfig.json` 파일에 해당 내용도 없고, YAML 파일도 없었다. 그리고 명령어도 조금 다른 느낌!!

찾아보니 레개도가 더 편한 방법으로 적용하고 있었다... 바로

```
npx openapi-typescript {서버 api 주소}/v3/api-docs --output src/typings/api/schema/index.ts
```

터미널에 해당 내용을 입력하면 명령어 한 줄로도 받아올 수 있다!

앞쪽에 `서버 api 주소/v3/api-docs`를 입력한 후, `--output` 다음에 오는 경로는 타입 스키마 파일을 어느 경로에 설정할 것인지 정해주는 경로이다.

</br>

## ⚡마무리

사실 완전... 진짜 완전 편한 방법이라고 생각하고 사용하고 있었는데 이번에 스키마 response가 안 오는 상황에 처하면서 여태 서버가 많은 노력을 해주었기에 우리가 사용할 수 있었다는 것을 알았다...

그리고 수민 오빠가 완전 관심 많아 보였는데 ㅋㅋ 빌릴게 때도 스키마 response 스웨거에 같이 보내줄 거라고 믿는다!!

BEAT의 경우 어드민 api들을 제외하고도 20개가 넘는 api가 있기 때문에 타입을 하나하나 지정해 주는 데에 어려움이 있었는데, 레개도가 짱 편한 라이브러리를 찾아와서 편하게 개발할 수 있었다!

</br>

참고 자료
</br>
[Swagger(Open API) 문서를 기반으로 TypeScript 타입 정의를 자동으로 생성하기](https://velog.io/@woogur29/SwaggerOpen-API-%EB%AC%B8%EC%84%9C%EB%A5%BC-%EA%B8%B0%EB%B0%98%EC%9C%BC%EB%A1%9C-TypeScript-%ED%83%80%EC%9E%85-%EC%A0%95%EC%9D%98%EB%A5%BC-%EC%9E%90%EB%8F%99%EC%9C%BC%EB%A1%9C-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)
