---
title: "Java Access Modifiers"
source-url: https://jenkov.com/tutorials/java/access-modifiers.html
captured-date: 2026-05-28
author: Jenkov
---

# Java Access Modifiers

Java access modifiers specify which classes can access a given class and its fields, constructors and methods.

## Access Modifiers Summary

| Modifier | Class | Nested Class | Constructor | Method | Field |
|----------|-------|--------------|-------------|--------|-------|
| private | No | Yes | Yes | Yes | Yes |
| default | Yes | Yes | Yes | Yes | Yes |
| protected | No | Yes | Yes | Yes | Yes |
| public | Yes | Yes | Yes | Yes | Yes |

## private Access Modifier

If a method or variable is marked as `private`, then only code inside the same class can access it.

```java
public class Clock {
    private long time = 0;
    
    public long getTime() {
        return this.time;
    }
}
```

### private Constructors

A private constructor cannot be called from outside the class. Useful for:
- Singleton pattern
- Factory methods
- Preventing instantiation

```java
public class Clock {
    private Clock(long time) {
        this.time = time;
    }
    
    public static Clock newClock() {
        return new Clock(System.currentTimeMillis());
    }
}
```

## default (package) Access Modifier

The default access modifier is declared by not writing any access modifier. Code inside the same package can access it.

```java
public class Clock {
    long time = 0;  // default access
}
```

## protected Access Modifier

Provides the same access as default, plus subclasses can access protected members even if in different packages.

```java
public class Clock {
    protected long time = 0;
}

public class SmartClock extends Clock {
    public long getTimeInSeconds() {
        return this.time / 1000;  // can access protected field
    }
}
```

## public Access Modifier

All code can access regardless of package. Used for public API.

```java
public class Clock {
    public long time = 0;
}
```

## Access Modifiers and Inheritance

When overriding methods:
- Cannot decrease accessibility (e.g., public → protected is invalid)
- Can increase accessibility (e.g., default → public is valid)

## Class Access Modifiers

- Classes can only have `default` or `public` access modifier
- `private` and `protected` are not allowed for top-level classes
- Nested classes can have all four modifiers

## Interface Access Modifiers

- Fields and methods in interfaces are implicitly `public`
- Cannot use `private`, `protected`, or default in interfaces