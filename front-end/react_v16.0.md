# React v16.0
translated by Hapumee

React v16.0의 릴리즈를 알릴 수 있게 되어 무척 기쁘다. 오랜 요구사항이었던 fragments, error, boundaries, portals를 포함하여
, 사용자 정의 DOM attrobutes를 지원하고, server-side 렌더링을 개선하며, 파일 사이즈로 감소하였다.

```javascript
render() {
    // 더 이상 추가적인 엘리먼트로 리스트 아이템들을 감쌀 필요가 없다!
   return [
       // key를 추가하는 것을 잊지 말기 :)
       <li key="A">First item</li>,
       <li key="B">Second item</li>,
       <li key="C">Third item</li>,
    ];
}
```

향후는 key를 필요로 하지 않는 JSX에 특별한 fragment 구문을 추가 할 것이다.
