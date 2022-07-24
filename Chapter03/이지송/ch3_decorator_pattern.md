# 3장 데코레이터 패턴 (Decorator Pattern)

## What is Decorator Pattern?

\*구조 패턴 중 하나. 주어진 상황 및 용도에 따라 어떤 객체에 책임(기능)을 동적으로 추가하는 패턴이다. 동일한 클래스의 다른 객체에 영향을 주지 않고 개별 객체마다 동작을 동적으로 추가할 수 있다. 단일 책임 원칙(Single Responsibility Principle) 준수하는데 유용.

- 구조 패턴(Structual Pattern): 클래스나 객체를 조합해 더 큰 구조를 만드는 패턴. 예를 들어, 서로 다른 인터페이스를 지닌 2개의 객체를 묶어 단일 인터페이스를 제공하거나 객체들을 서로 묶어 새로운 기능을 제공하는 패턴이다.

### 장점

- 기존 코드를 수정하지 않고도 데코레이터 패턴을 통해 행동 확장 가능
- 구성과 위임을 통해서 실행중에 새로운 행동을 추가 가능

### 단점

- 의미없는 객체들이 너무 많이 추가될 수 있음
- 데코레이터를 너무 많이 사용하면 코드가 필요 이상으로 복잡해질 수 있음

```javascript
/**
 * Abtract component
 */
class Logger {
  log() {
    throw new Error(`not implemented log method`);
  }
}

class BasicLogger extends Logger {
  log(msg) {
    console.log(msg);
  }
}

// Decorator class 1
class DateDecorator extends Logger {
  constructor(logger) {
    super();
    this._logger = logger;
  }

  log(msg) {
    msg = "[" + new Date() + "] " + msg;
    this._logger.log(msg);
  }
}

// Decorator class 2
class ColorDecorator extends Logger {
  constructor(logger) {
    super();
    this._logger = logger;
    this.color = "\x1b[40m";
  }

  log(msg) {
    msg = "\x1b[36m" + msg + "\x1b[0m";
    this._logger.log(msg);
  }
}

/**
 * Enhancing logger via decoratoe
 */
const basicLogger = new BasicLogger();
const colorDecorator = new ColorDecorator(basicLogger);
const dateDectorator = new DateDecorator(colorDecorator);
dateDectorator.log("Hello World");
```

## 개방-폐쇄 원칙 (Open-Closed Principle, OCP)

> 소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다

### 확장에 대해 열려 있다

모듈의 동작을 확장할 수 있다. 애플리케이션의 요구 사항이 변경될 때, 이 변경에 맞게 새로운 동작을 추가해 모듈을 확장할 수 있다. 즉, 모듈이 하는 일을 변경할 수 있다.

### 수정에 대해 닫혀 있다.

모듈의 소스 코드나 바이너리 코드를 수정하지 않아도 모듈의 기능을 확장하거나 변경할 수 있다. 그 모듈의 실행 가능한 바이너리 형태나 링크 가능한 라이브러리를 건드릴 필요가 없다.
