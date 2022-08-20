# 11장 프록시 패턴 (Proxy Pattern)

## What is Proxy Pattern?

> **프록시 패턴 (Proxy Pattern)** 은 특정 객체로의 접근을 제어하는 **대리인** (특정 객체를 대변하는 객체)을 제공한다.

### 장점

- 사이즈가 큰 객체(e.g. 이미지)가 로딩되기 전에도 프록시를 통해 참조할 수 있다.
- 실제 객체의 메소드들을 숨기고 인터페이스를 통해 노출시킬 수 있다.
- 로컬에 있지 않고 떨어져 있는 객체를 사용할 수 있다.
- 원래 객체의 접근에 대해서 사전처리를 할 수 있다.

### 단점

- 객체를 생성할때 한 단계를 거치게 되므로, 빈번한 객체 생성이 필요한 경우 성능이 저하될 수 있다.
- 프록시 내부에서 객체 생성을 위해 스레드가 생성, 동기화가 구현되어야 하는 경우 성능이 저하될 수 있다.
- 로직이 난해해져 가독성이 떨어질 수 있다.

```javascript
// Google Geo Coding Service Mock
class GeoCoder {
  getLatLng(address) {
    switch (address) {
      case "Amsterdam":
        return "52.3700° N, 4.8900° E";
      case "London":
        return "51.5171° N, 0.1062° W";
      case "Paris":
        return "48.8742° N, 2.3470° E";
      case "Berlin":
        return "52.5233° N, 13.4127° E";
      default:
        return "";
    }
  }
}

class GeoProxy {
  constructor() {
    this.geoCoder = new GeoCoder();
    this.cache = {};
  }

  getLatLng(address) {
    if (!this.cache[address]) {
      this.cache[address] = this.geoCoder.getLatLng(address);
    }
    return this.cache[address];
  }

  getCount() {
    return Object.keys(address).length;
  }
}

//In App
const geo = new GeoProxy();
geo.getLatLng("Paris");
geo.getLatLng("London");
geo.getLatLng("London");
geo.getLatLng("Amsterdam");
geo.getLatLng("Amsterdam");
geo.getLatLng("London");

console.log("\nCache size: " + geo.getCount());
```

## 프록시가 사용되는 대표적인 3가지

### 원격 프록시 (remote proxy)

원격 객체로의 접근 제어를 다룬다.<br/>
서로 다른 주소 공간에 있는 객체에 대해 마치 같은 주소 공간에 있는 것처럼 동작하게 만드는 패턴. <br />
Google Docs를 예시로 들 수 있으며, 브라우저는 브라우저대로 필요한 자원을 로컬에 가지고 있고 또 다른 자원은 Google 서버에 있는 형태이다.

### 가상 프록시 (virtual proxy)

생성하기 힘든 자원으로의 접근 제어를 다룬다.<br/>
꼭 필요로 하는 시점까지 객체의 생성을 연기하고, 해당 객체가 생성된 것처럼 동작하도록 만들고 싶을때 사용하는 패턴. <br/>
프록시 클래스에서 자잘한 작업들을 처리하고 리소스가 많이 요구되는 작업들이 필요할 때에만 주체 클래스를 사용하도록 구현하며, 위에서 예로 들었다시피 해상도가 아주 높은 이미지를 처리해야 하는 경우 작업을 분산하는 것을 예로 들 수 있다.

### 보호 프록시 (protection proxy)

접근 권한이 필요한 자원으로의 접근 제어를 다룬다. <br />
주체 클래스에 대한 접근을 제어하기 위한 경우로, 객체에 대한 접근 권한을 제어하거나 객체마다 접근 권한을 달리하고 싶을때 사용하는 패턴. <br />
프록시 클래스에서 클라이언트가 주체 클래스에 대한 접근을 허용할지 말지 결정하도록 할 수 있다.

## 데코레이터 패턴 vs. 프록시 패턴

### 유사점

- 둘 다 내부 인스턴스를 감싼 클래스로, 동일한 인터페이스를 구현하고 자신들의 메소드에 내부 인스턴스의 동일한 메소드 호출을 위임하는 아이디어를 중심으로 돌아간다.

### 차이점

- 필수 vs 선택적 종속성
  - 데코레이터는 래핑하는 인터페이스의 인스턴스가 필수이다.
  - 프록시는 인스턴스를 받거나, 그 자체를 생성할 수 있다.
- 추가 vs 제한
  - 데코레이터는 함수 호출을 래핑하고 원래 값을 반환함으로써 새로운 기능을 추가한다. 이는 함수 호출 전후로 무엇이든 할 수 있는 자유로움을 제공한다.
  - 프록시는 제한적이다. 함수의 동작을 변경하거나 예외를 throw 하여 특정 함수 호출을 제한할 수 있다.
- 범용 vs 특정 목적
  - 데코레이터는 일반적인 용도로 사용된다. 래핑하는 인스턴스에 관계 없이 일부 기능을 추가한다. 이는 여러 데코레이터가 임의의 순서로 적용될 수 있어야 하며, 여전히 동일한 결과를 가질 수 있어야 함을 의미한다.
  - 프록시는 보다 구체적인 목적을 제공한다. 주로 특정 인스턴스 기능을 변경하거나 추가하는데 사용된다. 프록시는 일반적으로 단일 프록시로 충분하기 때문에 일반적으로 스택처럼 쌓아올리지 않는다.

정리하면,

- 보안이나 성능, 네트워킹 등의 이유로 실제 object를 숨겨야 한다면 Proxy 패턴
- 런타임으로 기존 object에 새로운 동작을 추가해야 한다면 Decorator 패턴
  - Decorator는 클라이언트 요청에 따라 다른 순서로 동작을 섞어서 적용할 수 있는 유연함을 제공

### 프록시 변형 패턴

- 방화벽 프록시(firewll proxy)
  - 일련의 네트워크 자원에 대한 접근을 제어함으로써 주 객체를 '나쁜' 클라이언트들로부터 보호하는 역할.
  - e.g. 기업용 방화벽 시스템
- 스마트 레퍼런스 프록시 (smart reference proxy)
  - 주제가 참조될 때마다 추가 행동 제공.
  - e.g. 객체 참조에 대한 선/후 작업
- 캐싱 프록시 (caching proxy)
  - 비용이 많이 드는 작업의 결과를 임시로 저장, 추후 여러 클라이언트에 저장된 결과를 실제 작업처리 대신 보여주고 자원을 절약하는 역할
  - e.g. 웹 서버 프록시 또는 컨텐츠 관리 및 퍼블리싱 시스템
- 동기화 프록시 (synchronization proxy)
  - 여러 스레드에서 주제에 접근하는 경우에 안전하게 작업을 처리할 수 있도록 함.
  - e.g. 자바 스페이스
- 복잡도 숨김 프록시 (complexity hiding proxy)
  - 복잡한 클래스들의 집합에 대한 접근 제어. 복잡도를 숨김
- 지연 복사 프록시 (copy-on-write proxy)
  - 클라이언트에서 필요로 할 때까지 객체가 복사되는 것을 지연시킴으로써 객체 복사 제어. '변형된 가상 프록시'.
  - e.g. 자바 CopyOnWriteArrayList
