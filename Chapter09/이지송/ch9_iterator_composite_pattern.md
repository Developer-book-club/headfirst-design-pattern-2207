# 9장 반복자 패턴 (Iterator Pattern)과 컴포지트 패턴(Composite Pattern)

## What is Iterator Pattern?

> **반복자 패턴 (Iterator Pattern)** 은 컬렉션의 구현 방법을 노출하지 않으면서 집합체 내의 모든 항목에 접근하는 방법 제공

### 장점

- 크기가 큰 순회 알고리즘을 별도의 클래스로 추출하여 클라이언트 코드와 컬렉션을 정리
  - 단일 책임 원칙 만족
- 새로운 유형의 컬렉션 및 반복자를 구현하고 기존의 코드는 수정하지 않음
  - 개방/폐쇄 원칙 만족
- 각각의 반복자 객체는 그들만의 고유한 반복 상태가 포함되어 있음
  - 동일한 컬렉션을 병렬로 반복 가능
  - 반복을 지연시킬 수 있고 필요할 때 계속할 수 있음

### 단점

- 앱이 간단한 컬렉션만으로도 동작하는 경우 패턴을 적용하는 것이 지나칠 수 있음
- 반복자 사용이 일부 특수 컬렉션의 요소를 직접 탐색하는 것보다 덜 효율적일 수 있음

```javascript
class Iterator {
  constructor(arr) {
    this.index = 0;
    this.arr = arr;
  }

  first() {
    this.reset();
    return this.next();
  }

  next() {
    return this.arr[this.index++];
  }

  hasNext() {
    return this.index < this.arr.length;
  }

  reset() {
    this.index = 0;
  }

  each(callback) {
    for (let item = this.first(); this.hasNext(); item = this.next()) {
      callback(item);
    }
  }
}
```

### 내부 반복자(Internal Iterator) vs 외부 반복자 (External Iterator)

반복 작업을 누가 제어하는지 여부에 따라 정해짐 <br />
클라이언트가 제어하면 외부 반복자, 반복자 자신이 제어하면 내부 반복자 (이때 클라이언트는 할일만 알려준다)

## 디자인 원칙

> 어떤 클래스가 바뀌는 이유는 하나뿐이어야 한다.

### 응집도

한 클래스 또는 모듈이 특정 목적이나 역할을 얼마나 일관되게 지원하는지 나타내는 척도. <br />
어떤 모듈이나 클래스의 응집도가 높다는 것은 서로 연관된 기능이 묶여있다는 것을, 응집도가 낮다는 것은 서로 상관 없는 기능들이 묶여있다는 것을 뜻한다.

## What is Composite Pattern?

> **컴포지트 패턴(Composite Pattern)** 은 객체를 트리구조로 구성하여 부분-전체 계층 구조 구현 <br />
> 클라이언트에서 개별 객체와 복합 객체를 똑같은 방법으로 다룰 수 있음

단일 책임 원칙을 깨는 대신 투명성(transparency) 확보

```javascript
class Buffer {
  getSize() {}
}

class DirectoryBuffer extends Buffer {
  constructor() {
    super();
    this.buffers = [];
  }

  getSize() {
    return this.buffers.map((e) => e.getSize()).reduce((a, b) => a + b);
  }

  addBuffer(buffer) {
    this.buffers.push(buffer);
  }
}

class FileBuffer extends Buffer {
  constructor() {
    super();
  }

  setBuffer(buffer) {
    this.buffer = buffer;
    return this;
  }

  getSize() {
    return this.buffer.length || 0;
  }
}

const file1 = new FileBuffer().setBuffer(Buffer.from("hello"));
const file2 = new FileBuffer().setBuffer(Buffer.from("world"));

const composite = new DirectoryBuffer();
composite.addBuffer(file1);
composite.addBuffer(file2);
console.log(composite.getSize());
```

### 장점

- 객체들이 모두 같은 타입으로 취급되어 새로운 클래스 추가가 용이하다.
- 단일 객체 및 집합 객체를 구분하지 않고 코드 작성이 가능하여 사용자 코드가 단순해진다.
- 단일 객체와 집합 객체로 구성된 하나의 일관된 클래스 계통을 정의한다.
  - 런타임 단일 객체와 집합 객체를 구분하지 않고 일관된 프로그래밍 가능

### 단점

- 설계가 지나치게 범용성을 많이 가짐
  - 복합체 구성 요소에 제약을 가하기 힘듦
- 복합체가 오직 한개의 구성 요소만 있기를 바라는 경우, Composite 클래스만으로는 제어 어려움
  - 런타임 점검 필요
