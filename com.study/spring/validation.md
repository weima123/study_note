## 参数校验(javax.validation)
#### @NotNull、@NotBlank 、@NotEmpty的区别
````
@NotNull://CharSequence, Collection, Map 和 Array 对象不能是 null, 但可以是空集（size = 0）。  
@NotEmpty://CharSequence, Collection, Map 和 Array 对象不能是 null 并且相关对象的 size 大于 0。  
@NotBlank://String 不是 null 且去除两端空白字符后的长度（trimmed length）大于 0。 
````
### 注解的定义（在version 4.1中）：
#### 1、@NotNull：
````
定义如下：
@Constraint(validatedBy = {NotNullValidator.class})
这个类中有一个isValid方法是这么定义的：
public boolean isValid(Object object, ConstraintValidatorContext constraintValidatorContext) {  
 return object != null;    
} 
对象不是null就行，其他的不保证。
````
#### 2、@NotEmpty：
````
定义如下：
@NotNull    
@Size(min = 1)  
也就是说，@NotEmpty除了@NotNull之外还需要保证@Size(min=1)，这也是一个注解，这里规定最小长度等于1，也就是类似于集合非空。
````
#### 3、@NotBlank：
````
@NotNull    
@Constraint(validatedBy = {NotBlankValidator.class})  
类似地，除了@NotNull之外，还有一个类的限定，这个类也有isValid方法：
if ( charSequence == null ) {  //curious   
  return true;     
}     
return charSequence.toString().trim().length() > 0; 
有意思的是，当一个string对象是null时方法返回true，但是当且仅当它的trimmed length等于零时返回false。即使当string是null时该方法返回true，但是由于@NotBlank还包含了@NotNull，所以@NotBlank要求string不为null。
````
````
示例：
String name = null;
@NotNull: false
@NotEmpty: false
@NotBlank: false

String name = "";
@NotNull: true
@NotEmpty: false
@NotBlank: false

String name = " ";
@NotNull: true
@NotEmpty: true
@NotBlank: false

String name = "Great answer!";
@NotNull: true
@NotEmpty: true
@NotBlank: true
````
### 一、常用的校验注解
#### （1）常用标签
````
@Null  被注释的元素必须为null
@NotNull  被注释的元素不能为null
@AssertTrue  被注释的元素必须为true
@AssertFalse  被注释的元素必须为false
@Min(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@Max(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@DecimalMin(value)  被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@DecimalMax(value)  被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@Size(max,min)  被注释的元素的大小必须在指定的范围内。
@Digits(integer,fraction)  被注释的元素必须是一个数字，其值必须在可接受的范围内
@Past  被注释的元素必须是一个过去的日期
@Future  被注释的元素必须是一个将来的日期
@Pattern(value) 被注释的元素必须符合指定的正则表达式。
@Email 被注释的元素必须是电子邮件地址
@Length 被注释的字符串的大小必须在指定的范围内
@NotEmpty  被注释的字符串必须非空
@Range  被注释的元素必须在合适的范围内
````