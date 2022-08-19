# 8장 템플릿 메소드 패턴 (Templete Method Pattern)

## What is Templete Method Pattern?

> **템플릿 메소드 패턴 (Templete Method Pattern)** 은 알고리즘의 골격을 정의한다. <br />
> 템플릿 메소드를 사용하면 알고리즘의 일부 단계를 서브클래스에서 구현할 수 있으며, 알고리즘의 구조는 그대로 유지하면서 알고리즘의 특정 단계를 서브 클래스에서 재정의할 수도 있다. <br />
> 래퍼 패턴 (Wrapper Pattern) 이라고도 한다.

### 장점

- 중복 코드를 줄일 수 있다.
- 자식 클래스의 역할을 줄여 핵심 로직의 관리가 용이하다.
- 좀더 코드를 객체지향적으로 구성할 수 있다.

### 단점

- 추상 메소드가 많아지면서 클래스 관리가 복잡해진다.
- 클래스 간의 관계와 코드가 꼬여버릴 염려가 있다.

```javascript
class DataBuffer {
  constructor(data) {
    this.data = data;
  }

  sanitize(data) {
    return data;
  }

  checkForErrors() {
    return false;
  }

  compute() {
    isError = this.checkForErrors(this.data);
    if (isError) {
      return -1;
    }

    const sanitizedBuffer = this.sanitize(this.data);
    return sanitizedBuffer.byteLength;
  }
}

class WordBuffer extends DataBuffer {
  constructor(data) {
    super(data);
  }

  sanitize(data) {
    return Buffer.from(data.toString().split(" ")[0]);
  }
}

const generalBuffer = new DataBuffer(Buffer.from("abc def"));
console.log(generalBuffer.compute());

const wordBuffer = new WordBuffer(Buffer.from("abc def"));
console.log(wordBuffer.compute());
```

## 할리우드 원칙 (Hollywood Principle)

> 먼저 연락하지 마세요. 저희가 연락 드리겠습니다.

**\*의존성 부패(dependency rot)** 방지 가능 <br />
저수준의 구성 요소가 고수준의 구성 요소에 접근할 수는 있어도 직접 호출할 수 없고 고수준의 구성 요소에 의해 어떻게 쓰일지 결정하게 해야 한다.

- 의존성 부패(dependency rot): 고수준 구성 요소가 저수준 구성요소에 의존하고 또 그 저수준 구성 요소는 고수준 구성요소에 의존하고, 고수준 구성요소가 또 다른 구성 요소에 의존하는 등, **의존성이 복잡하게 꼬여있는 상황**.
