# Chap06. Command
메서드 호출을 캡슐화 합니다. 특정 객체에 관한 특정 작업 요청을 캡슐화한다.

핵심은 특정 객체가 특정 작업이 무엇을 하는지 모른다는 점이다. 

커맨드 객체는 행동과 리시버의 정보가 같이 들어있다
커맨드 객체는 execute() 메서드 하나 뿐이다
클라이언트는 인보커 객체의 setCommand() 메서드를 호출한다 이때 커맨드 객체를 넘겨준다 이 커맨드 객체는 인보커 객체에 저장된다
인보커 커맨드 객체의 execute() 를 호출하면 리시버의 행동 메서드가 호출된다.


# 코드 작성

## 커맨드 객체 정의
```java
public interface Command { // Command
    void execute();
}

public class LightOnCommand implements Command { // ConcreteCommand 
    Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }
    
    @Override
    public void execute() {
        light.on();
    }
}
```

## 커맨드 객체 사용
```java
public class SimpleRemoteControl { // invoker
    Command slot; // LightOnCommand
    
    public SimpleRemoteControl() {}
    
    public void setCommand(Command command) { 
        slot = command;
    }
    
    public void buttonWasPressed() {
        slot.execute(); // lightOnCommand.execute();
    }
}
```

## 여러개의 커맨드를 가진 Command
```java
public class MultipleRemoteControl {
    Command[] onButton;
    Command[] offButton;
    
    ...
    ...

    public void setCommand(int slot, Command on, Command off) {
        slot = command;
    }

    public void on(int slot) {
        onButton[slot].execute();
    }

    public void off(int slot) {
        offButton[slot].execute();
    }


}
```

## 여러개의 커맨드를 가진 Invoker
```java
public class MacroCommand implements Command {
    Command[] commands;
    
    public MacroCommand(Command[] commands) {
        this.commands = commands;
    }
    
    @Override
    public void execute() {
        for (Command c : commands) {
            c.execute();
        }
    } 
    
}
```

## 활용 예제

Spring AOP

데코레이터 + 커맨드

```java
package org.aspectj.lang;

import org.aspectj.runtime.internal.AroundClosure;

public interface ProceedingJoinPoint extends JoinPoint { // Command
    void set$AroundClosure(AroundClosure var1);

    Object proceed() throws Throwable;

    Object proceed(Object[] var1) throws Throwable;
}
```

```java
@Aspect
public class Receiver { // Receiver
    
    ...
    ...

    @Around
    public Object process(final ProceedingJoinPoint joinPoint) throws Throwable { // execute
        return joinPoint.proceed();
    }
}
```

* 데코레이터 패턴을 통해서 액션을 메서드 파라미터로 받는다. (process(ProceedingJoinPoint))
* 액션이 커맨드 인터페이스로 수행된다. 어떤 동작을 하는지 Receiver는 알 수가 없다.
* 액션 또한 Receiver를 모른다.



