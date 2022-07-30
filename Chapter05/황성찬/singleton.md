# Chap05. Singleton
프로그램에 단 하나만 존재하는 객체를 만드는 패턴이다. 굳이 매번 생성해야 하는 객체가 아니라면 싱글턴으로 생성해서 자원을 아낄 수 있다. 또, 전역 변수를 사용할 때 감수해야하는 단점들을 감수할 필요도 없다.

# 고전적인 싱글턴

```java
public class Singleton {
    private final Singleton unique;
    
    private Singleton {}
    
    public static Singleton getInstance() {
        if (unique == null) {
            unique = new Singleton();
        }
        
        return unique;
    }
}
```

서버 설정 값, connection pool, thread pool 등에 사용된다.
private 생성자만 가지고 있다.
전역적으로 사용할 수 있는 static 메서드를 통해서 인스턴스를 제공한다.

# 멀티스레드 환경에서의 싱글턴

```java
public class Singleton {
    private final Singleton unique;
    
    private Singleton {}
    
    public static synchronized Singleton getInstance() {
        if (unique == null) {
            unique = new Singleton();
        }
        
        return unique;
    }
}
```

인스턴스가 처음 생성될 때만 동기화가 필요하고 그외에는 필요하지 않다.
오버헤드가 많이 발생해서 속도 저하가 우려된다.
즉, 생성하는 시점이 문제가 된다. 이걸 어떻게 해결 할 수 있을까?

## JVM 에게 생성 위임하기
```java
public class Singleton {
    private static final Singleton unique = new Singleton();
    
    private Singleton {}
    
    public static synchronized Singleton getInstance() {
        return unique;
    }
}
```

static 키워드를 사용해서 JVM이 실행될 때 유일한 인스턴스가 생성되도록 한다.

## Double Checked Locking 
```java
public class Singleton {
    private volatile static Singleton unique;
    
    private Singleton {}
    
    public static synchronized Singleton getInstance() {
        if (unique == null) {
            synchronized (Singleton.class) {
                if (unique == null) {
                    unique = new Singleton();
                }
            }
        }
        
        return unique;
    }
}
```

이러면 처음만 동기화 된다. 근데 static 방법이 더 간단하고 좋은 것 같다.

# Enum 사용하기

```java
public enum Singleton {
    UNIQUE;    
}

public class Main {
    public static void main(String[] args) {
        Singleton singleton = Singleton.UNIQUE;
    }
}

```

더 간단하다.