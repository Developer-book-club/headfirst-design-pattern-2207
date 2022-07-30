# 5장 싱글턴 패턴 (Singleton Pattern)

## What is Singleton Pattern?

> **싱글턴 패턴(Singleton Pattern)** 은 클래스 인스턴스를 하나만 만들고, 그 인스턴스로의 전역 접근을 제공한다.

### 장점

- 메모리 최소화
  - 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리 낭비를 방지할 수 있다. 또한 이미 생성된 인스턴스를 활용하니 속도 측면에서도 이점이 있다.
- 데이터 공유
  - 전역으로 사용되는 인스턴스이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있다.
  - But 동시성 문제 유의 필요

### 단점

- 구현 복잡성 증가
  - 멀티스레딩 환경일 경우 동시성 문제 해결을 위해 별도 키워드 사용하는 등, 구현 코드가 좀 더 복잡하다.
- 테스트 복잡성 증가
  - 자원을 공유하기 때문에 테스트를 격리된 환경에서 수행되려면 매번 인스턴스의 상태를 초기화시켜야 한다.
- 의존 관계상 클라이언트가 구체 클래스에 의존
  - SOLID 원칙 중 DIP(의존성 역전 원칙) 위반
  - OCP 위반할 가능성 증가

```javascript
class Person {
  constructor() {
    if (typeof Person.instance === "object") {
      return Person.instance;
    }
    Person.instance = this;
    return this;
  }
}

export default Person;
```

```javascript
// In Node.JS Module Cashing
// 파일 이름 대소문자 주의 필요
// 다른 모듈이 npm에서 동일한 모듈을 설치할 때 주의 필요
class Person {
  constructor() {}
}

module.exports = new Person();
```
