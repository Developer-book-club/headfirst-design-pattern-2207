# 싱글톤 패턴

## Feature

- 하나의 객체만 생성하고 글로벌로 접근한다.
- 하나의 객체는 앱내 공유할 수 있고 글로벌 상태를 관리하는데 유용합니다.
- 글로벌 상태에서 값을 수정하기에 실행의 순서가 중요합니다.
  - 전역 상태를 사용할 때 데이터 흐름을 이해하는 것은 애플리케이션이 성장하고 수십 개의 구성 요소가 서로 의존함에 따라 매우 까다로울 수 있습니다.

## Adavantages

- 제한된 하나의 객체 생성은 메모리 절약할 수 있는 측면이 있음.

## Disadvantage

- 많은 언어(ex) C++)에서는 직접적으로 구현이 어렵다.
- 글로벌 scope 오염은 글로별 변수를 우연히 덮어쓸 경우 예기치 못한 행동을 야기시킬 수 있습니다.
- js는 module 시스템이 글로벌 scope 오염없이 값을 쉽게 접근할 수 있게 만들어줍니다.

## Reac에서의 상태관리

- 싱글톤 대신에 Redux, React Context, Zustand, Mobx ... 같은 상태관리 라이브러리에 의존한다.
- 싱글톤과 행동은 비슷해보이지만 이 라이브러리들은 read-only 상태를 제공해준다.
- ex) redux
  - 컴포넌트에서 action을 dispatcher를 통해 보낸 후 오직 reducers에 의해 상태를 업데이트 진행한다.
- 전역 상태의 단점이 이러한 도구를 사용하여 마술처럼 사라지지는 않지만, 구성 요소가 상태를 직접 업데이트할 수 없기 때문에 최소한 전역 상태가 의도한 대로 변경되도록 할 수 있습니다.

## Example

- LocalStorage

```ts
// libs/localStorage.ts
export const localStorage = (() => {
  const storage = window.localStorage;

  const saveUser = (user) => {
    // saveUser
    storage.save(user);
  };

  const removeUser = () => {
    // saveUser
    storage.delete(user);
  };
  return {
    saveUser,
    removeUser,
  };
})();
```

## Usecase

- React 상태관리 라이브러리
  - Redux, React Context, Zustand, Mobx, Recoil, Jotai
- localStorage, sessionStorage, analytics, monitoring 와 여러개 객체 생성이 필요하지 않는 라이브러리 관리용도

## Reference

- https://www.patterns.dev/posts/singleton-pattern/
