# React v16.0
translated by Hapumee

React v16.0의 릴리즈를 알릴 수 있게 되어 무척 기쁘다. 오랜 요구사항이었던 fragments, error, boundaries, portals를 포함하여
, 사용자 정의 DOM attrobutes를 지원하고, server-side 렌더링을 개선하며, 파일 사이즈로 감소하였다.

**render의 새로운 반환 타입: fragments와 strings**

컴포넌트의 `render` 메서드에서 엘리먼트로 구성된 배열 타입도 반환할 수 있게 되었다. 다른 배열과 마찬가지로, key 경고를 피하기 위해서 각 엘리먼트에 key를 추가할 필요는 있다.

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

또한, 문자열 반환에 대한 지원을 추가했다.

```javascript
render() {
    return 'Look ma, no spans!';
}
```

[여기에서 지원하는 모든 반환 형태를 확인할 수 있다.](https://reactjs.org/docs/react-component.html#render)

**개선된 오류 처리**

기존에는 렌더링 시에 런타임 오류가 발생할 경우, React가 정상 작동을 하지 않고 암호화된 오류 메시지가 표시되면서 페이지를 새로고침해야만 페이지를 복구할 수 있었다. 이 문제를 명확히 하기 위해 React 16에서는 조금 더 탄력적인 오류 처리 전략을 사용한다. 기본적으로 만약 컴포넌트의 render나 lifecycle에 관한 메서드 안에서 오류가 발생한다면, 모든 컴포넌트 트리가 루트(root)에서 분리되게 된다. 이것은 붕괴된 데이터들이 화면에 노출되는 것을 방지한다. 그렇지만 이것 역시 이상적인 사용자 경험은 아니다.

오류가 발생할 때마다 모든 앱을 분리하는 대신에, 우리는 오류 범위(error boundaries)를 지정할 수 있다. 오류 범위란 하위 트리 안에서 오류를 감지하고 그 위치에 fallback UI를 노출할 수 있는 특별한 컴포넌트이다. try-catch와 같은 구문과 같은 오류 범위를 생각해 볼 수 있는데, 이것이 Reacr 컴포넌트에 적용되었다고 생각할 수 있다.  

조금 더 자세한 사항은 [React 16에서의 오류 처리](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)에 관한 문서에서 확인할 수 있다.

**Portals**

Portals는 부모 컴포넌트의 DOM 계층구조 밖에 존재하는 DOM 노드에서 자식 요소들을 렌더링 하는 최고 수준의 방법을 제공한다.

```javascript
render() {
    // Reac는 새로운 <div>를 *생성하지 않는다*. `domNode`안에서 자식을 렌더링할 뿐이다.
    // `domNode`는 DOM 안에서의 위치에 관계없이 유효한 DOM 노드이다.
    return ReactDOM.createPortal(
        this.props.children,
        domNode
    );
}
```

전체 예제는 [protals 문서에서 확인할 수 있다].(https://reactjs.org/docs/portals.html)

**개선된 server-side 렌더링**

React 16은 완전하게 새로 작성된 서버 render가 포함되었다. 또한 많이 빨라졌다. *streaming*을 지원함으로써 조금 더 빠르게 client에 바이트를 전달할 수 있다.
