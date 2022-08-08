# 4장 팩토리 패턴 (Factory Pattern)

## What is Factory Pattern?

자바 진영에서 가장 많이 쓰이는 디자인 패턴 중 하나. \*생성 패턴에 속하며, 생성 로직을 클라이언트에 노출시키지 않고 객체를 생성하며, 공통 인터페이스를 통해 새로 생성된 객체를 참조한다. <br />
객체 생성이 복잡하거나 변경될 수 있는 상황에서 팩토리 패턴은 코드를 깔끔하게 유지하게 도와준다. 하지만 잘못 쓰면 \*클래스 폭발로 이어지니 주의.

- 생성 패턴(Creational Patterns): 객체 생성과 관련된 패턴으로 객체의 생성절차를 추상화하는 패턴
- 클래스 폭발(Class Explosion): 하나의 기능을 추가하기 위해 필요 이상으로 많은 수의 클래스를 추가해야 하는 경우

### 장점

- 객체 생성 로직과 클라이언트 간의 느슨한 결합.
- 책임의 명확한 분리
- 클라이언트 프로그램에 영향을 주지 않고 객체 생성 로직을 쉽게 변경할 수 있다.

### 단점

- 경우에 따라 구현이 복잡해짐
- 복잡성에 따라 도입되는 추상화 수준 때문에 테스트하기 여러울 수 있음

## 간단한 팩토리 (Simple Factory)

디자인 패턴보다는 프로그래밍에서 자주 쓰이는 관용구에 가깝다. <br />
다른 객체의 생성을 캡슐화하는 객체를 가리키며, 애플리케이션으로부터 해당 객체를 보호한다. <br />

```javascript
const user = UserFactory.createUser();
```

반환할 수 있는 제품의 수를 늘리기 위해 간단한 팩토리 메소드를 매개변수화(parameterize)하는 것이 일반적.

```javascript
class Admin extends User {}
class Customer extends User {}

class UserFactory {
  /**
   * @params {string} type
   * @returns {User}
   **/
  static createUser(type) {
    if (type === "admin") return new Admin();
    if (type === "customer") return new Customer();
  }
}

// IN APP
const admin = UserFactory.createUser("admin");
const customer = UserFactory.createUser("customer");
```

공장에서 반환되는 것은 보통 `상품` 이다. 위의 UserFactory 코드에서는 Admin, Customer 두 클래스가 상품과도 같다. 이 상품들에게 중요한건 공통된 인터페이스를 가져야 한다는 것이다. 클라이언트가 이 공장으로부터 어떤 상품을 받든, 상품 특정 인터페이스가 존재하는지 검증할 필요가 없게끔 말이다.

## 팩토리 메소드 패턴 (Factory Method Pattern)

간단한 팩토리는 많은 과정에 적합하고 좋은 시작일 수 있다. 하지만 팩토리 메소드 패턴을 통해서 이를 더욱 확장시킬 수 있다.

> <strong>팩토리 메소드 패턴</strong> 에서는 객체를 생성할 때 필요한 인터페이스를 만든다. <br/>
> 어떤 클래스의 인터페이스를 만들지는 서브클래스에서 결정한다. <br/>
> 팩토리 메소드 패턴을 사용하면 클래스 인스턴스 만드는 일을 서브클래스에게 맡기게 된다.

해당 패턴 이름이 팩토리 메소드 패턴인 이유는 템플릿 메소드 패턴(Templete Method Pattern)에서 파생된 패턴이기 때문이다.

```javascript
// Product classes
class ApiRequest {
  get(url) {
    throw new Error(`not implemented get method`);
  }
}

class TcpApiRequest extends ApiRequest {
  get(url) {
    // TCP 구현
  }
}

class HttpApiRequest extends ApiRequest {
  get(url) {
    // HTTP 구현
  }
}

// Creator classes
class Client {
  fetch(url) {
    const request = this.makeRequest();
    request.get(url);
  }

  // factory method
  makeReuqest() {
    return new ApiRequest();
  }
}

class TcpClient extends Client {
  makeRequest() {
    return new TcpApiRequest();
  }
}

class HttpClient extends Client {
  makeRequest() {
    return new HttpApiRequest();
  }
}

// IN APP
const client = new HttpClient();
client.fetch("http://");
```

## 객체 의존성 이란?

파라미터나 리턴값 또는 지역변수 등으로 다른 객체를 참조하는 것

## 의존성 뒤집기 원칙 (Dependency Inversion Principle)

> <strong>디자인 원칙</strong><br />
> 추상화된 것에 의존하게 만들고 구상 클래스에 의존하지 않게 만든다<br /> <br />
> 고수준 모듈은 저수준 모듈의 구현에 의존해서는 안된다. <br />
> 저수준 모듈이 고수준 모듈에서 정의한 추상 타입에 의존해야 한다.

```javascript
const Logger = require("./Logger");

class UserService {
  construct() {
    this.logger = new Logger(); // UserService는 Logger에 의존하는 상태
  }

  async getById(id) {
    // do something
  }
}

module.exports = new UserService();
```

의존성 주입을 통한 의존성 뒤집기

```javascript
const UserLogger = require("./UserLogger");

class UserService {
  construct(logger) {
    this.logger = logger;
  }

  async getById(id) {
    // do something
  }
}

module.exprots = new UserService(new UserLogger());
```

## 추상 팩토리 패턴 (Abstract Factory Pattern)

> 추상 팩토리 패턴은 구상 클래스에 의존하지 않고도 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생산하는 인터페이스를 제공한다.<br />
> 구상 클래스는 서브 클래스에서 만든다.

```javascript
// Product classes
class ApiRequest {
  get(url) {
    throw new Error(`not implemented get method`);
  }
}

class TcpApiRequest extends ApiRequest {
  get(url) {
    // TCP 구현
  }
}

class HttpApiRequest extends ApiRequest {
  get(url) {
    // HTTP 구현
  }
}

// Creator classes
class ApiRequestFactory {
  static create() {
    return new ApiRequest();
  }
}

class TcpApiRequestFactory extends ApiRequestFactory {
  static create() {
    return new TcpApiRequest();
  }
}

class HttpApiRequestFactory extends ApiRequestFactory {
  static create() {
    return new HttpApiRequestFactory();
  }
}

class Client {
  static create(factory) {
    const request = factory.create(); // 의존성 주입(Dependency Injection, DI)
    return new Client(request);
  }

  construct(request) {
    this.request = request;
  }

  fetch(url) {
    this.request.get(url);
  }
}

const client = Client.create(HttpApiRequestFactory);
client.fetch("http://");
```
