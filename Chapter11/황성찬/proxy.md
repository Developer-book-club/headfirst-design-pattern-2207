# 11.프록시

모든 뽑기 기계의 재고와 현재 상태를 알려주는 기능을 추가하자

```jsx
// 기계의 현재 위치를 알려주는 기능
public class GumbalMachine {
	String location;
	
	public GumbalMachine(String location, int count) {
		this.location = location;
	}

	public String getLocation() {
		return location;
	}
}
```

이번엔 기계의 위치, 재고, 현재 상태를 가져와서 보고서를 출력해주는 클래스를 정의하자

```jsx
public class GumbalMonitor {
	GumbalMachine machine;

	public GumbalMonitor(GumbalMachine machine) {
		this.machine = machine;
	}

	public void report() {
		System.out.println("뽑기 기계 위치: " + machine.getLocation());
		System.out.println("현재 재고: " + machine.getCount() + " 개");
		System.out.println("현재 상태: " + machine.getState());
	}
}
```

위의 코드를 원격으로 모니터링 할 수 있게 만들어야한다.

원격 프록시와 함께 해결해보자

원격 프록시는 로컬 대변자 역할을 한다. 원격 객체란 다른 외부 머신에서 실행되고 있는 객체를 말한다. 로컬 대변자의 메서드를 호출하면 원격 객체에게 그 메서드 호출을 전달해준다. 즉, 물리적으로 분리되어 있는 머신의 메서드를 호출한다.

힙 공간이 분리되어 있기 때문에 `Duck d = new Duck();` 같은 코드는 안통한다.

그래서 자바의 원격 메서드 호출 API를 사용해야 한다. Remote Method Invocation(RMI)

# RMI Basic

로컬 객체의 메서드를 호출하면 그 요청을 원격 객체에 전달해주는 시스템이 필요하다.

우리 대신 통신을 처리해주는 보조 객체가 필요하다.

보조 객체를 사용하면 클라이언트는 로컬 객체의 메서드를 호출하면 된다.

클라이언트는 보조 객체가 실제 서비스를 제공한다고 생각하지만 사실은 클라이언트 보조 객체가 그 요청을 원격 객체에 전달한다. 조금 더 구체적으로 말하자면 클라이언트 보조 객체는 서버에 연락을 취하고 메서드 이름, 인자 등의 호출 정보를 전달하고 원격 객체로부터 리턴되는 응답을 기다린다. 즉, 실제 일은 원격 객체가 한다. 비즈니스 로직도 원격 객체에 존재한다.

서버는 서비스 보조 객체가 있어서 Socket 연결로 클라이언트 보조 객체로부터 요청을 받아오고 호출 정보를 해석해서 서비스 객체에 있는 메서드를 호출한다. 따라서 서비스 객체는 메서드 호출이 원격 클라이언트가 아닌 로컬 객체로부터 들어온다고 생각한다.

서비스 보조 객체는 서비스 객체의 리턴값을 받아서 Socket의 출력 스트림으로 클라이언트 보조 객체에게 전송한다.

# Java RMI의 개요

lookup 서비스도 RMI에서 제공한다. RMI에서 클라이언트 보조 객체는 스텁(stub), 서비스 보조 객체는 스켈레톤(skeleton)이라고 부른다. 원격 서비스를 만드는 것은 4단계로 이루어진다.

1. 원격 인터페이스 만들기
2. 서비스 구현 클래스 만들기
3. RMI 레지스트리 실행하기
4. 원격 서비스 실행하기

## 서버 코드

```jsx
// 원격 인터페이스
public interface MyRemote extends Remote {
	public String sayHello() throws RemoteException;
}

// 원격 서비스를 구현한 클래스
public class MyRemoteImpl extends UnicastRemoteObject implements MyRemote {
	private static final long serialVersionUID = 1L;

	public String sayHello() {
		return "Server says, 'Hey'";
	}

	public MyRemoteImpl() throws RemoteException { }

	public static void main(String[] args) {
		try {
			MyRemote service = new MyRemoteImpl();
			Naming.rebind("RemoteHello", service());
		} catch(Exception ex) {
			ex.printStackTrace();
		}
	}
}
```

## 클라이언트 코드

```jsx
public class MyRemoteClient {
	public static void main(String[] args) {
		new MyRemoteClient().go();
	}

	public void go() {
		try {
			MyRemote service = (MyRemote) Naming.lookup("rmi://127.0.0.1/RemoteHello");
			String s = service.sayHello();
			System.out.println(s);
		} catch(Exception ex) {
			ex.printStackTrace();
		}
	}
}
```

## GumballMachine 클래스를 원격 서비스로 바꾸기

```jsx
public interface GumballMachineRemote extends Remote {
	public int getCount() throws RemoteException;
	public String getLocation() throws RemoteException;
	public State getState() throws RemoteException;
}
```

```jsx
public interface State extends Serializable {
	public void insertQuarter();
	public void ejectQuarter();
	public void turnCrank();
	public void dispense();
}
```

…

# 프록시 패턴

프록시 패턴은 특정 객ㅔ로의 접근을 제어하는 대리인을 제공한다.

말 그대로 프록시 객체는 실제 객체의 대리인이다.

인터페이스, 실제 클래스, 프록시 클래스 가 필요하다.