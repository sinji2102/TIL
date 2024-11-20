# [241120] react-csv

</br>

## 🤔 문제 상황

예매자 관리하기 뷰가 나에게 넘어오면서, 기존에 예매자 관리하기 뷰에서 제공되던 react-csv에 대해 공부하게 되었다.

![image](https://github.com/user-attachments/assets/ded1d7d7-ed1a-40b3-b1ca-962a4f0ee59b)

원래 하단에 존재하던 csv 다운이 위로 올라오면서, 헤더 공통 컴포넌트로 옮겨져야만 하는데 헤더에는 텍스트 따로, 함수 따로 들어가야 했기 때문에 자연스레 CSVLink 컴포넌트도 수정해야 했다.

</br>

## 🍀 Today I Learn

### react-csv

react-csv는 데이터를 csv 파일로 다운로드 받을 수 있게 해 주는 react 라이브러리이다.

- [yarn react-csv link](https://yarnpkg.com/package/react-csv)

### 다운로드

```
// npm
npm install react-csv --save;

// yarn
yarn add react-csv

// typescript
npm install --save @types/react-csv
```

### 사용법

간단하게 설명하면, data props에 배열 데이터를 주면 해당 데이터를 csv 형태로 다운받을 수 있다.

```
import { CSVLink, CSVDownload } from "react-csv";

const csvData = [
  ["firstname", "lastname", "email"],
  ["Ahmed", "Tomi", "ah@smthing.co.com"],
  ["Raed", "Labes", "rl@smthing.co.com"],
  ["Yezzi", "Min l3b", "ymin@cocococo.com"]
];
<CSVLink data={csvData}>Download me</CSVLink>;
// or
<CSVDownload data={csvData} target="_blank" />;
```

#### 헤더 선언

CSV 파일 내 헤더에 들어갈 header 변수를 선언한다.

```
const headers = [
  { label: "예매일시", key: "createdAt" },
  { label: "회차", key: "scheduleNumber" },
  { label: "예매자 이름", key: "bookerName" },
  { label: "매수", key: "purchaseTicketCount" },
  { label: "연락처", key: "bookerPhoneNumber" },
  { label: "예매상태", key: "bookingStatus" },
];
```

#### 데이터 헤더에 맞게 매핑

```
const [CSVDataArr, setCSVDataArr] = useState<CSVDataType[]>([]);

useEffect(() => {
    if (data?.bookingList) {
      const tempCSVDataArr: CSVDataType[] = [];

      data.bookingList.map((item) => {
        const date = item.createdAt.split("T")[0];
        const time = item.createdAt.split("T")[1].slice(0, 5);
        const formattedDate = date?.replace(/-/g, ".");
        const formattedCreateTime = `${formattedDate} ${time}`;

        tempCSVDataArr.push({
          createdAt: formattedCreateTime,
          scheduleNumber: `${convertingNumber(item.scheduleNumber)}회차`,
          bookerName: item.bookerName,
          purchaseTicketCount: `${item.purchaseTicketCount}매`,
          bookerPhoneNumber: item.bookerPhoneNumber,
          bookingStatus: convertingBookingStatus(item.bookingStatus),
        });
      });

      tempCSVDataArr.sort(
        (obj1, obj2) => new Date(obj1.createdAt).getTime() - new Date(obj2.createdAt).getTime()
      );
      setCSVDataArr(tempCSVDataArr);
    }
  }, [data]);

```

### CSVLink 컴포넌트 적용

앞서 선언한 data, header를 CSVLink 컴포넌트에 적용해준다.

```
<Button>
    <CSVLink
        data={CSVDataArr}
        headers={headers}
        filename={`${data.performanceTitle}_예매자 목록.csv`}
    >
        예매자 목록 다운받기
    </CSVLink>
</Button>
```

</br>

## ⚡ 마무리

저번 회의 때 지우가 회차별로 시트를 분리하고 싶다고 했는데, 찾아보니 csv의 경우 시트 분리가 불가능한 것 같다. [How to export multiple sheets to Excel in React](https://stackoverflow.com/questions/68745506/how-to-export-multiple-sheets-to-excel-in-react)
</br>

xlsx의 경우 시트 분리가 가능한 것 같은데, 이번 스프린트가 끝나고 시간 여유있을 때 xlsx로 변경해보고 싶다. 지우야내가노력해볼게..........

---

참고 자료
</br>
[React-csv로 csv 파일 다운로드하기](https://velog.io/@khy226/React-csv%EB%A1%9C-csv-%ED%8C%8C%EC%9D%BC-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%ED%95%98%EA%B8%B0)
