# 10장 상태 패턴 (State Pattern)

## What is State Pattern?

> **상태 패턴 (State Pattern)** 을 사용하면 객체 내부 상태가 바뀜에 따라 객체의 행동을 바꿀 수 있다. <br />
> 객체의 클래스가 바뀌는 것과 같은 결과를 얻을 수 있다.

```javascript
class OrderStatus {
  constructor(name, nextStatus) {
    this.name = name;
    this.nextStatus;
  }

  next() {
    return new this.nextStatus();
  }
}

class WaitingForPayment extends OrderStatus {
  constructor() {
    super("WaitingForPayment", Shipping);
  }
}

class Shipping extends OrderStatus {
  constructor() {
    super("Shipping", Delivered);
  }
}

class Delivered extends OrderStatus {
  constructor() {
    super("Delivered", Delivered);
  }
}

class Order {
  constructor() {
    this.status = new WaitingForPayment();
  }

  nextState() {
    this.state = this.state.next();
  }
}
```

### 장점

- 하나의 객체에 대한 여러 동작을 구현해야 할 때 상태 객체만 수정하므로 동작의 추가, 삭제 및 수정이 간단해진다.
- 객체의 상태에 따른 조건문이 줄어들어 코드가 간결해지고 가독성이 상승한다.

### 단점

- 상태에 따른 조건문을 대신한 상태 객체가 증가하여 관리해야 할 클래스의 수가 증가한다.

## 전략 패턴 vs 상태 패턴

- 전략 패턴은
  - 한 번 인스턴스를 생성하고 나면, 상태가 거의 바뀌지 않는 경우에 사용한다.
  - 객체의 상태 속성을 변환하려면 외부에서 새로운 상태의 주입이 필요하다.
  - 하나의 특정 작업만 처리한다.
- 상태 패턴은
  - 한 번 인스턴스를 생성하고 난 뒤, 상태를 바꾸는 경우가 빈번한 경우에 사용한다.
  - 객체의 상태 속성에 대해 스스로 변환할 수 있다.
  - 컨텍스트 개체가 수행하는 대부분의 모든 것에 대한 기본 구현을 제공한다.
