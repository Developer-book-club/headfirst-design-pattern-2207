# 6장 커맨드 패턴 (Command Pattern)

## What is Command Pattern?

> **커맨드 패턴 (Command Pattern)** 은 액션(요청 수행)을 객체로 캡슐화한다. <br />
> 커맨드 객체는 실제로 요청을 처리하는 객체로부터 요청을 발행하는 객체를 분리하여 느슨하게 결합된 시스템을 구축한다. <br />
> 이러한 요청을 event라고 하고 요청을 처리하는 코드를 event handler라고 한다.

### 구조

- Invoker
  - 요청을 발행하는 class
- Receiver
  - 요청을 수신하는 class. ConcreteCommand 기능 실행
- Command
  - 실행될 기능에 대한 인터페이스
  - 실행될 기능을 execute 메서드로 선언
- ConcreteCommand
  - 실제로 실행되는 기능 구현
  - Command 인터페이스의 execute 메서드를 실제로 구현

### 장점

- SRP 원칙 준수
  - 요청부와 동작부 분리
- OCP 원칙 준수
  - 별도 코드 수정 없이 새로운 Receiver와 새로운 Command 추가 가능
- Command 단위 별도 액션 가능
- Command 상속 및 조합을 통해 더 정교한 커맨드 구현 가능

### 단점

- 전체적으로 이해가 필요하고 복잡한 설계 구조를 가짐
- Receiver 및 Receiver의 동작이 추가되면 그 동작에 대한 클래스를 만들어야 하므로 다소 많은 잡다한 클래스들이 추가됨

```javascript
class Command {
  constructor(value) {
    this.value = value;
  }

  execute() {
    throw new Error(`not implemented execute method`);
  }

  undo() {
    throw new Error(`not implemented undo method`);
  }
}

class AddCommand extends Command {
  constructor(value) {
    super(value);
  }

  execute(value) {
    return value + this.value;
  }

  undo(value) {
    return value - this.value;
  }
}

class SubCommand extends Command {
  constructor(value) {
    super(value);
  }

  execute(value) {
    return value - this.value;
  }

  undo(value) {
    return value + this.value;
  }
}

class MulCommand extends Command {
  constructor(value) {
    super(value);
  }

  execute(value) {
    return value * this.value;
  }

  undo(value) {
    return value / this.value;
  }
}

class DivCommand extends Command {
  constructor(value) {
    super(value);
  }

  execute(value) {
    return value / this.value;
  }

  undo(value) {
    return value * this.value;
  }
}

class Calculator {
  constructor() {
    this.current = 0;
    this.commands = [];
  }

  execute(command: Command) {
    this.current = command.execute(this.current);
    this.commands.push(command);
  }

  undo() {
    if (this.commands.length < 1) return;
    const command = this.commands.pop();
    this.current = command.undo(this.current);
  }

  getValue() {
    return this.current;
  }
}

const calculator = new Calculator();

calculator.execute(new AddCommand(100));
calculator.execute(new SubCommand(24));
calculator.execute(new MulCommand(6));
calculator.execute(new DivCommand(2));

calculator.undo();
calculator.undo();

console.log(`value: ${calculator.getValue()}`);
```
