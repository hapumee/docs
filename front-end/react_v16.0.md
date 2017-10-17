# React v16.0
*translated* *by* *Hapumee*

React v16.0의 릴리즈를 알릴 수 있게 되어 무척 기쁘다. 오랜 요구사항이었던 fragments, error, boundaries, portals를 포함하여
, 사용자 정의 DOM 속성을 지원하고, server-side 렌더링을 개선하며, 파일 사이즈로 감소하였다.

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

React 16은 완전하게 새로 작성된 서버 renderer가 포함되었다. 또한 많이 빨라졌다. *streaming*을 지원함으로써 바이트를 조금 더 빠르게 client에 전달할 수 있다. 그리고 `process.env` 체크를 컴파일 하는 [새로운 패키징 전략] 덕분으로 (Node에서 `process.env`를 읽는 것은 정말 느리다!) 더 좋은 서버 렌더링 성능을 얻기 위한 React bundling의 필요가 없어졌다.  

코어팀 멤버인 Sasha Aickin이 [React 16의 SSR 향상에 대한 아티클]을 작성하였다. Sasha의 종합적인 벤치마킹에 의하면, React 16에서의 서버 렌더링이 React 15에서의 서버 렌더링에 비해 약 *3배 더 빠르다*고 한다. "`process.env` 컴파일과 React 15를 비교해 볼 떄, Node 4 기준으로 2.4배, Node 6 기준으로 3배, Node 8.4 릴리즈 기준으로는 3.8배의 성능 향상을 보인다. 그리고 컴파일 없이 React 15와 비교해 볼 때는 React 16은 Node 최신 버전에서 SSR 전체의 큰 이득을 얻을 수 있다." (Sasha의 지적과 같이, 이 수치들은 종합적인 벤치마킹을 기반으로 하는 수치이며, 실제 성능을 반영하지 않을 수도 있음을 알고 있자.) 

더불어, React 16은 서버 렌더링된 HTML을 client로 전달할 때 사용하는 것이 좋다. 더 이상 초기 렌더링은 서버의 결과과 정확하게 일치시킬 필요가 없어졌다. 대신, 가급적이면 기존 DOM을 최대한 재사용하려는 시도를 한다. 체크섬은 더 이상 필요하지 않다. 일반적으로, 서버 대 클라이언트에서 서로 다른 컨텐츠를 렌더링 하는 것을 권장하지 않지만, 어떤 경우에는 유용하기도 하다 (예를 들면, temostamps). *그러나, 서버에 누락된 노드가 존재하는 것은 위험할 수 있다. 왜냐하면 누락된 노드가 잘못된 속성으로 다시 노드를 만들 수 있기 떄문이다.*

조금 더 자세한 사항은 [ReactDOMServer 문서](https://reactjs.org/docs/react-dom-server.html)에서 확인할 수 있다.

**사용자 정의 DOM 속성의 지원**

기존에는 인식할 수 없는 HTML과 SVG 속성들을 무시였으나, React 16에서는 그것들을 DOM으로 전달한다. 이것은 우리가 React의 허용 속성 목록을 제거하고 파일 사이즈를 줄이는 데에 도움이 되었다.

**감소된 파일 사이즈**

이러한 모든 추가적인 작업에도 불구하고 React 16은 15.6.1 버전에 비하면 사실상 매우 작다.

`react`의 사이즈는 20.7kb(압축의 경우 6.9kb)에서 5.3kb(압축의 경우 2,2kb)로 감소하였다.
`react-dom`의 사이즈는 141kb(압축의 경우 42.9kb)에서 103.7kb(압축의 경우 32.6kb)로 감소하였다.
`react`와 `react-dom`을 합치면 그 사이즈는 161.7kb(압축의 경우 49.8kb)에서 109kb(압축의 경우 34.8kb)로 감소하였다.

**이전 버전에 비해 총 파일 사이즈는 32%(압축의 경우 30%) 감소하였다.**

사이즈의 변화는 부분적으로는 패키징의 변화에 따른 것이다. 이제 React는 각각 다른 형태의 대상들에 대해서 플랫 번들(flat bundle)로 생성하기 위해 [Rollup](https://rollupjs.org/)을 사용하는데, 이것으로 인해 사이즈와 런타임 성능이 모두 향상되었다. 플랫 번들(flat bundle) 형식은 또한 React가 번들 사이즈에 미치는 영향이 앱을 제공하는 방식이나 Webpack, Browserify, 선 빌드된 UMD 번들, 또는 다른 시스템에 관계없이 거의 일관적이라는 것을 의미한다. 

**MIT licensed**

[여러분이 혹 놓친 경우에](https://code.facebook.com/posts/300798627056246/relicensing-react-jest-flow-and-immutable-js/), React 16을 MIT license 하에서 사용이 가능하다. 또란, 혹 즉시 업그레이드를 할 수 없는 경우를 대비하여 React 15.6.2를 MIT에 게시하였다. 

**새로운 핵심 구조**

React 16은 "Fiber"라는 코드명의 새로운 핵심 구조를 바탕으로 구축된 React의 첫 번쨰 버전이다. 이 프로젝트에 대한 모든 것은 [Facebook 엔지니어링 블로그](https://code.facebook.com/posts/1716776591680069/react-16-a-look-inside-an-api-compatible-rewrite-of-our-frontend-ui-library/)에서 읽을 수 있다. (스포일러: 우리는 React를 새로 썼다!)

Fiber는 React 16에서 에러 범위나 fragment와 같은 거의 대부분의 새로운 구조를 담당하고 있다. 다음의 몇 릴리즈에서 React의 숨겨진 잠재력을 최대 끌어내기 위한 더 많은 새로운 기능들을 기대할 수 있다.

아마도 우리가 작업하고 있는 가장 흥미로운 부분은 *비동기 렌더링*일 것이다. 말하자면, 브라우저에서 주기적으로 실행함으로써 렌더링 작업을 공동으로 예약하는 전략이다. React는 메인 스레드(main thread)를 차단하지는 않기 떄문에, 결과적으로 비동기 렌더링에서는 애플리케이션의 응답이 향상되었다.

아래의 데모에서 비동기 렌더링으로 해결할 수 있는 문제의 유형에 대해 살펴볼 수 있다.

![async rendering]
(https://twitter.com/acdlite/status/909926793536094209)

Tip: 우측 상단 회전하는 검은색 사각형의 주의하시오.

우리는 비동기 렌더링은 큰 이슈이며 이것이 React의 미래를 대표한다고 생각한다. 가능한 한 원활하게 v16.0으로 마이그레이션하기 위해서는 비동기 기능들이 아직은 많이 활성화되지는 않았지만 이것들을 곧 출시할 수 있을 것이라 기대해 본다.  

**설치**

npm 레지스트리에 React v16.0.0이 등록되어 있다.

아래는 Yarn을 이용한 React 16 설치 방법이다.
```javascript
yarn add react@^16.0.0 react-dom@^16.0.0
```

아래는 npm을 이용한 React 16 설치 방법이다.
```javascript
npm install --save react@^16.0.0 react-dom@^16.0.0
```

또한, CDN을 통한 React UMD 빌드도 제공한다.
```javascript
<script crossorigin src="https://unpkg.com/react@16/umd/react.production.min.js"></script>
<script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"></script>
```

[설치 명령에 대해 조금 더 자세히](https://reactjs.org/docs/installation.html) 알고자 한다면 문서를 참조하라.

**업그레이드**
React 16이 중요한 내부 변화를 포함하고 있다고 하더라도, 업그레이드 측면에서는 다른 주요 React와 다를 바 없다. 우리는 올 해 초 이후로 Facebook과 Messenger.com 사용자에게 React 16을 제공해 왔고, 여러 번의 beta 버전과 릴리즈 후보 버전을 출시하여 추가 문제들을 해결했다. 자그마한 예외들이 있긴 하지만, **여러분들의 앱에 15.6 에서 아무 오류없이 잘 수행된다면, 16에서도 잘 수행할 것이다.**

**제거 예정 (deprecations)**

서버 렌더링 컨테이너 하이드레이팅(hydrating)에 대한 명확한 API를 제공한다. 따라서, 만약 여러분이 서버에서 렌더한 HTML을 재사용하고자 한다면, `ReactDOM.render` 대신에 `ReactDOM.hydrate`를 사용하라. 만약 단순한 client 측면의 렌더링이라면, `ReactDOM.render`를 유지하라.

**React Addons**

앞서 발표한 것처럼, 우리는 [React Addon의 지원을 중단했다](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html#discontinuing-support-for-react-addons). 우리는 각 addons(`react-addons-perf`를 제외하고; 아래에서 확인하시오.)의 최신 버전이 가까운 미래에도 동작하기를 기대하지만, 그렇다고 추가적인 제시를 하지는 않을 것이다. 

[마이그레이션에 대한 제안 방법](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html#discontinuing-support-for-react-addons)은 이전 발표를 참고하라.

`react-addons-perf`는 React 16에서는 더 이상 동작하지 않는다. 앞으로 이 도구의 새 버전을 출시할 가능성이 크다. 그 때까지는 [React 컴포넌트를 프로파일링하기 위해 브라우저의 성능 도구를 사용할 수 있다](https://reactjs.org/docs/optimizing-performance.html#profiling-components-with-the-chrome-performance-tab). 

**변경 사항 요약**

React 16은 다수의 작은 변경 사항들을 포함한다. 이것들은 오직 보편적이지 않은 경우에만 영향을 미치며, 우리는 이것이 대부분의 앱에 영향이 있다고는 생각하지 않는다.

* React 15에서는 `unstable_handleError`를 사용한 에러 범위에 대하여 제한적이고 문서화되지 않은 지원이 있었다. 이 메서드는 `componentDidCatch`라는 이름으로 변경되었다. 코드 샘플을 사용하여 [새 API로 자동 마이그레이션 할 수 있다](https://github.com/reactjs/react-codemod#error-boundaries).

* `ReactDOM.render`와 `ReactDOM.unstable_renderSubtreeIntoContainer`는 lifecycle 메서드 안에서 호출될 때 null을 반환한다. 이런 현상을 회피하기 위해서 [portals](https://github.com/facebook/react/issues/10309#issuecomment-318433235)와 [refs](https://github.com/facebook/react/issues/10309#issuecomment-318434635)를 사용할 수 있다.

* `setState` :

  * null로 `setState`를 호출해도 더 이상 업데이트(update)가 트리거되지는 않는다. 만약 재렌더링(re-render)를 하고 싶다면 업데이터(updater) 기능에서 결정할 수 있다.
  
  * render에서 `setState`를 직접 호출하는 경우 항상 업데이트(update)가 수행된다. 이것은 이전 버전에서는 수행되지 않았다. 그렇지만, render에서 `setState`를 직접 호출할 때 항상 업데이트가 발생한다 하더라도, render에서 setState를 직접 수행해서는 안 된다.
  
  * `setState` 콜백(callback) (두 번째 인자)은 모든 컴포넌트가 렌더링된 후에 수행되던 대신에, `componentDidMount`/`componentDidUpdate` 후에 수행된다. 

* `<A />`를 `<B />`로 대체할 때, `A.componentWillUnmount` 이전에 `B.componentWillMount`가 항상 먼저 수행된다. 이전에는 이런 경우에는 `A.componentWillUnmount`가 수행되었었다.

* 이전에는 ref를 컴포넌트로 변경하면 해당 컴포넌트의 렌더링이 호출되지 전에 항상 ref가 분리된다. 이제, DOM에 변경 사항을 적용할 때 나중에 ref를 변경한다. 

* React 이외의 어떤 것에 의해 수정된 컴포넌트 안에서 재렌더링(re-render) 하는 것은 안전하지 않다. 이것은 이전에 몇몇 경우에는 동작했지만 지금은 지원하지 않는다. 이런 경우 경고가 표시된다. 대신 `ReactDOM.unmountComponentAtNode`를 사용하여 컴포넌트 트리를 정리해야 한다. [예제를 보라](https://github.com/facebook/react/issues/10294#issuecomment-318820987)

* `componentDidUpdate` lifecycle은 더 이상 `prevContext` 파라미터를 받지 않는다([#8631](https://github.com/facebook/react/pull/8631)).

* Shallow 렌더러는 더 이상 `componentDidUpdate`를 호출하지 않는다. 왜냐하면 DOM refs가 더 이상 유효하지 않기 때문이다. 이것은 또한 (이전 버전에서도 역시 호출되지는 않았던) `componentDidMount`를 일관되게 만든다.

* Shallow 렌더러는 `unstable_batchedUpdates`를 더 이상 구현하지 않는다.

* `ReactDOM.unstable_batchedUpdates`는 이제 콜백 이후 추가적인 인수 하나만 사용한다.

**Packaging**

* `react/lib/*`와 `react-dom/lib/*`는 더 이상 존재하지 않는다. CommonJS 환경에서조차 React와 ReactDOM은 단일 파일들("플랫 번들(flat bundles)")로 사전 컴파일된다. 만일 여러분이 이전에 문서화되지 않은 React 내부 자료에 의존하고 있고 그것이 더 이상 동작하지 않는다면, 새 이슈에 대해 구체적인 살례를 알려 준다면 우리는 그것을 마이그레이션 전략에 명시화하도록 노력할 것이다.

* `react-with-addons.js`는 더 이상 존재하지 않는다. 모든 호환 addons은 npm에 독립적으로 게시되어 있고 필요한 경우 단일 파일 브라우저 버전이 있다.

* 15.x에서 소개된 deprecations는 핵심 패키지에서 제거되었다. `React.createClass`는 `create_react-class`로, `React.PropType`은 `prop-types`로, `React.DOM`는 `react-dom-factories`으로, `react-addons-test-utils`는 `react-dom/test-utils`로, 그리고 Shallow 렌더러는 `react-test-renderer/shallow`로 사용 가능하다. 

* 코드와 자동화 한 샘플 코드의 마이그레이션을 위한 지침은 [15.5.0](https://reactjs.org/blog/2017/04/07/react-v15.5.0.html)과 [15.6.0](https://reactjs.org/blog/2017/06/13/react-v15.6.0.html)을 보라.

* 개발과 프로덕트 빌드 사이의 차이점을 강조하기 위해 단일 파일 브라우저 빌드의 이름과 경로가 변경되었다. 예를 들면 아래와 같다.

  * `react/dist/react.js` → `react/umd/react.development.js`
  
  * `react/dist/react.min.js` → `react/umd/react.production.min.js`
  
  * `react-dom/dist/react-dom.js` → `react-dom/umd/react-dom.development.js`
  
  * `react-dom/dist/react-dom.min.js` → `react-dom/umd/react-dom.production.min.js`

**JavaScript 환경 요구사항**

React 16은 컬렉션 타입 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)과 [Set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)에 의존한다. 지원하지 않는 구버전 브라우저(예를 들어, IE < 11)와 디바이스를 지원하려면, `core-j` 또는 `babel-polyfill`과 같은 번들 애플리케이션에 전역 polyfill을 포함하도록 고려해야 한다.

core-j를 사용한 React 16이 구버전 브라우저를 지원하는 polyfill된 환경은 다음과 같을 것이다.

```javascript
import 'core-js/es6/map';
import 'core-js/es6/set';

import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
    <h1>Hello, world!</h1>,
    document.getElementById('root')
);
```

React는 또한 `requestAnimationFrame`에 의존한다 (그것이 테스트 환경일지라도!). 테스트 환경 에서의 간단한 코드는 아래와 같다.

```javascript
global.requestAnimationFrame = function(callback) {
    setTimeout(callback, 0);
};
```

**감사 인사!**

항상 그렇듯이, 이번 릴리즈도 오픈 소스 기여자들이 없었다면 가능하지 않았을 것이다. 버그를 해결하고 PR을 개설하며 이슈를 해결하고 문서를 작성한 모든 이들에게 감사한다!

홱심 기여자들, 특히 앞선 릴리즈 동안의 지난 몇 주간의 엄청난 열정에 감사한다.: [Brandon Dail](https://twitter.com/aweary), [Jason Quense](https://twitter.com/monasticpanic), [Nathan Hunzaker](https://twitter.com/natehunzaker) 그리고 [Sasha Aickin](https://twitter.com/xander76). 




특별 기고가의 주요 공헌자, 특히 출시 전주기 동안의 지난 몇 주간의 영웅적인 노력에 감사드립니다.
