# 7장 어댑터 패턴(Adapter Pattern)과 퍼사드 패턴(Facade Pattern)

## What is Adapter Pattern?

> **어댑터 패턴(Adapter Pattern)** 특정 클래스 인터페이스를 클라이언트에서 요구하는 다른 인터페이스로 변환한다. <br />
> 인터페이스가 호환되지 않아 같이 쓸 수 없었던 클래스를 사용할 수 있게 도와준다. <br />
> 래퍼 패턴 (Wrapper Pattern) 이라고도 한다.

### 장정

- 기존 코드를 변경하지 않아도 된다.
- 클래스 재활용성 증가

### 단점

- 구성 요소를 위해 클래스를 증가시켜야 하기 때문에 복잡도 증가
- Class Adapter의 경우 상속을 사용하여 유연하지 못함
- Object Adapter의 경우 대부분의 코드를 다시 작성해야 하기 때문에 비효율적

```javascript
class Printer {
  constructor() {
    this.textArr = [];
  }

  input(text) {
    this.textArr.push(text);
  }

  print() {
    return this.textArr.join(" ");
  }
}
```

```javascript
const printer = new Printer();
printer.input("Hello");
printer.input("World");

console.log(printer.print()); // Hello World
```

```javascript
class HashTagPrinter {
  constructor() {
    this.textArr = [];
  }

  input(text) {
    this.textArr.push(text);
  }

  printWithHashTag() {
    return this.textArr.map((text) => `#${text}`).join(" ");
  }
}
```

```javascript
class HashTagAdapter {
  constructor(hashTagPrinter) {
    this.printer = hashTagPrinter;
  }

  input(text) {
    this.printer.input(text);
  }

  print() {
    return this.printer.printWithHash();
  }
}
```

```javascript
const printer = new HashTagAdapter(new Printer());
printer.input("Hello");
printer.input("World");

console.log(printer.print()); // #Hello #World
```

## Object Adapter vs. Class Adapter

```javascript
// Class Adapter
class Adapter extends HashTagPrinter {
  constructor() {
    super();
  }

  print() {
    return this.printWithHash();
  }
}
```

클래스 어댑터는 모든 객체 지향 언어에서 사용할 수 있는 일반적이고 오래된 "상속"인 반면, 객체 어댑터는 어댑터 설계 패턴의 고전적인 방식이다.

> 진짜 지저분한 걸 보고 싶으신가요? 거울을 한 번 들여다보세요. <br /> _- 객체 어댑터가 클래스 어댑터에게... -_

클래스 어댑터에서 어댑터를 서브클래스화 하여 어댑터의 동작을 쉽게 재정의할 수 있다. <br />
그러나 여러 서브 클래스들을 조정하는데 사용해서는 안 된다. 선택한 언어에서 다중 상속을 사용할 수 있다고 가정하더라도 유지 불가능한 상속 체인이 생성된다.

객체 어댑터의 이점은 클라이언트와 어댑티의 느슨한 결합이다.
래핑하는 객체의 클래스 동작을 수정할 수는 없지만, 해당 동작을 사용하는 방법을 결정할 수 있다.

## What is Facade Pattern?

> **퍼사드 패턴(Facade Pattern)** 하나 이상의 서브 시스템에서 복잡한 기능으로부터 클라이언트를 보호하는 인터페이스를 제공한다. <br />
> 사소해보일 수 있는 간단한 패턴이지만 강력하고 매우 유용하다. <br />
> 다층 아키텍처를 중심으로 구축된 시스템에 종종 존재한다.

### 장점

- 서브시스템 간의 결합도를 낮춘다.
- 클라이언트 입장에서 서브시스템을 사용해야 할때 다루어야 할 객체의 수를 줄여준다.
- 클라이언트 입장에서 좀 더 간결하게 코드를 알아볼 수 있게 해준다.

### 단점

- 클라이언트에게 내부 서브시스템까지 숨길 수 없다.
- 클라이언트가 서브시스템 내부 클래스를 직접 사용하는 것을 막을 수 없다.

```javascript
class Mortgage {
  static of(name) {
    return new Mortgage(name);
  }

  constructor(name) {
    this.name = name;
  }

  applyFor(amount) {
    const result = (function () {
      if (!Bank.verify(this.name, amount)) {
        return "denied";
      }
      if (!Credit.get(name)) {
        return "denied";
      }
      if (!Background.check(name)) {
        return "denied";
      }
      return "approved";
    })();

    return `${this.name} has been ${result} for a ${amount} mortgage`;
  }
}

class Bank {
  static verify(name, amount) {
    // complex logic
    return true;
  }
}

class Credit {
  static get(name) {
    // complex logic
    return true;
  }
}

class Background {
  static check(name) {
    // complex logic
    return true;
  }
}

const mortgage = Mortgage.of("Joan Templeton");
const result = mortgage.applyFor("$100,000");

console.log(result); // Joan Templeton has been approved for a $100,000 mortgage
```

## Decorator vs. Adapter vs. Facade

어댑터 패턴은 인터페이스를 전환하지만 데코레이터 패턴은 인터페이스를 전환하지 않고 원본 객체를 받아들이는 메소드에 전달될 수 있도록 원본 객체의 인터페이스를 구현할 뿐이다.

퍼사드 패턴은 데코레이터 패턴과 비교하자면, 새로운 동작을 추가하지 않고 단지 인터페이스에 있는 메소드를 호출할 뿐이다. 그저 Facade로 제공할 뿐이다.

## 최소 지식 원칙 (Principle of Least Knowledge)

> 진짜 절친에게만 이야기해야 한다.

데메테르의 법칙이라고도 불린다. <br />
상호작용을 하는 클래스의 개수와 상호작용 방식에 주의를 기울여야 한다. <br />
아래의 4개의 가이드라인을 제시한다.

- 자기 자신의 메소드
- 메소드에 매개변수로 전달된 객체
- 그 메소드에서 생성하거나 인스턴스를 만든 객체
- 그 객체에 속하는 구성 요소

```javascript
class Car {
  constructor() {
    // 이 클래스의 구성 요소. 메소드 호출 가능
    this.engine = new Engine();
  }

  start(key) {
    // 메소드 내의 새로운 객체. 메소드 호출 가능
    const doors = new Doors();

    // 메소드의 매개변수. 메소드 호출 가능
    const authorized = key.turns();
    if (authorized) {
      engine.start();
      // 객체의 구성요소. 메소드 호출 가능
      this.updateDashboardDisplay();
      doors.lock();
    }
  }

  updateDashboardDisplay() {}
}
```

### 주의할 점

객체들 간의 의존성과 유지보수가 용이해지나, 메소드 호출을 처리하기 위한 래퍼클래스의 필요성과 시스템의 복잡도 증가, 성능 저하가 있을 수 있다.
