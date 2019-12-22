# String

## 属性描述

```java
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0
```

String类被final修饰，不能被继承。内部使用final修饰的char数组来存储字符串，不能修改数组引用。

## 常用方法

