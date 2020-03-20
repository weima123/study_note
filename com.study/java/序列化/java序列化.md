https://www.cnblogs.com/zhengshao/p/8882613.html
## 一.java序列化的两种方式
### 1.实现Serializable接口

### 2.实现Externalizable接口
```
writeExternal(ObjectOutPut out);
readExternal(ObjectInput in);
```

### 3.Externalizable Serializable 区别
```
Externalizable 可以手动实现序列化|反序列化的过程，更灵活
```

## 二.java不做序列化的处理方式
### 1.transient --java关键字
```
属性前增加transient关键字
public class User{
 private transient String userName;
}
userName将不会被序列化|反序列化
```
### 2.被static修饰的属性不会做序列化

## 三.基于多态的序列化
### 1.如果父类实现了Serializable，子类没有实现Serializable，则子类自动实现序列化
### 2.如果子类实现了Serializable，父类没有实现Serializable，则父类不会被序列化

