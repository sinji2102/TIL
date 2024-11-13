# [241123] React resize 이벤트

</br>

## 🤔 문제 상황

<table align="center">
        <tr align="center">
            <th>하단바 있을 때</th>
            <th>하단바 없을 때</th>
        </tr>
        <tr>
            <td><img width="277" alt="image" src="https://github.com/user-attachments/assets/eae8e73d-da98-4581-8c96-6cc70319251b"></td>
            <td><img width="308" alt="image" src="https://github.com/user-attachments/assets/8128410e-f227-4944-a165-ed3c1e11883c"></td>
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

이따 수정하겠습미당... 
