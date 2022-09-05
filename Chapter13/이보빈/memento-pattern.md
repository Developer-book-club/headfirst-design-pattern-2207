# 메멘토 패턴

- 상태를 따로 저장하는 역할을 맡는 객체
- 객체를 이전 상태로 복구시켜야하는 경우에 사용합니다.
  - ex) 사용자가 작업 취소해야하는 경우, 편집툴의 ctrl-z, ctrl-y 기능

## 작동 원리

- 객체의 중요한 상태 저장
- 객체의 캡슐화 유지

```ts
// 스냅샷 역할을 하는 값 객체
class Memento {
  state: object;
  getState(): Memento;
}

// 원본 객체, 상태를 복구하거나 스냅샷으로 자기 상태 추출 가능
class Originator {
  state: object;

  save(): Memento;
  restore(m: Memento): Memento;
}

// Memento 보관을 책임지는 보관자이며, Memento 스택을 저장함으로써 Originator 의 히스토리를 추적
class CareTaker {
  originator: Originator;
  history: Memento[];

  undo();
  redo();
}
```

## 장점

- 저장된 상태를 핵심 객체와 별도의 객체에 따로 보관하여 안전?합니다.
- 이로인해 핵심 객체의 데이터를 계속해서 캡슐화된 상태로 유지할 수 있습니다.
- 복구 기능 구현이 쉽습니다.

## 단점

- 복구하는데 시간이 오래 걸릴 수 있습니다.
  - 위치에 따라 undo, redo를 여러번 반복적으로 행동해야하기에?
- 저장할때 직렬화 하는것이 좋습니다.
