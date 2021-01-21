---
title: 业务代码的痛处——DTO
entitle: DTO-Understanding
tags: [业务代码的痛处,DTO]
categories:
  - 业务代码的痛处
  - DTO
date: 2019-04-08 23:43:06
---

本文介绍了DTO，以及如何优雅将DTO与entity进行相互转化。

<!--more-->

## 了解DTO

DTO：数据传输对象，只要用于网络传输的对象，都认为他们可以当做是DTO对象。
### DTO的转化

DTO作为系统与外界交互的模型对象，将DTO转化为BO对象或者是普通的entity对象是必不可少的一个步骤。

```java
@Data
@EqualsAndHashCode(callSuper = true)
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class User extends BaseEntity {

    private String name;

    private Integer age;

    private String password;

    private Timestamp createTime;


}
```

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
@Accessors(chain = true)
public class UserDTO {

    private String name;

    private Integer age;

}
```
### 使用工具

可以使用`org.springframework.beans.BeanUtils.copyProperties`进行属性的拷贝。
BeanUtils.copyProperties是一个浅拷贝方法，复制属性时，只需要把DTO对象和要转化的对象两个的属性值设置为一样的名称，并且保证两者是一样的类型。

```java
   public User addUser(UserDTO userDTO) {
        User user = new User();
        BeanUtils.copyProperties(userDTO, user);
        return userService.add(user);
    }
```

### 转化的语义

```java
    public User addUser(UserDTO userDTO) {
        User user = convertFor(userDTO);
        return userService.add(user);
    }

    private User convertFor(UserDTO userDTO) {
        User user = new User();
        BeanUtils.copyProperties(userDTO, user);
        return user;
    }
```

这是一个更好的语义写法，虽然麻烦了些，但是可读性大大增加了，在写代码时，我们应该尽量把语义层次差不多的放到一个方法中。

### 抽象接口定义

在实际情况中，DTO的转化操作有很多，那么就应该定义一个接口，让所有的转化操作都有规则的进行。

```java
// 泛型抽象接口
public interface DTOConverter<S, T> {

    T convert(S s);
}

```

```java
public class UserDTOConverter implements DTOConverter<UserDTO, User> {

    @Override
    public User convert(UserDTO userDTO) {
        User user = new User();
        BeanUtils.copyProperties(userDTO, user);
        return user;
    }

}

```

重构后的代码如下：

```java
    public User addUser(UserDTO userDTO) {
        User user = new UserDTOConverter().convert(userDTO);
        return userService.add(user);
    }
```
### review

将UserDTOConvert 与 UserDTO进行聚合：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserInputDTO {

    private String name;

    private Integer age;

    public User convertToUser() {
        UserInputDTOConvert userInputDTOConvert = new UserInputDTOConvert();
        return userInputDTOConvert.convert(this);
    }

    private static class UserInputDTOConvert implements DTOConverter<UserInputDTO, User> {
        @Override
        public User convert(UserInputDTO userInputDTO) {
            User user = new User();
            BeanUtils.copyProperties(userInputDTO, user);
            return user;
        }
    }

}
```

则转化代码变成：

```java
    public User addUser(UserDTO userDTO) {
        User user = userDTO.convertToUser();
        return userService.add(user);
    }
```

在DTO对象中添加了转化的行为，这样的操作可以让代码的可读性变得更强，并且是符合语义的。

完成正向转化和逆向转化：

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class UserInputDTO {

    private String name;

    private Integer age;

    public User convertToUser() {
        UserInputDTOConvert userInputDTOConvert = new UserInputDTOConvert();
        return userInputDTOConvert.convert(this);
    }

    public UserInputDTO convertFor(User user) {
        UserInputDTOConvert userInputDTOConvert = new UserInputDTOConvert();
        return userInputDTOConvert.reverse(user);
    }

    private static class UserInputDTOConvert implements DTOConverter<UserInputDTO, User> {
        @Override
        public User convert(UserInputDTO userInputDTO) {
            User user = new User();
            BeanUtils.copyProperties(userInputDTO, user);
            return user;
        }

        @Override
        public UserInputDTO reverse(User user) {
            UserInputDTO userDTO = new UserInputDTO();
            BeanUtils.copyProperties(user, userDTO);
            return userDTO;
        }
    }

}
```

## 参考资料
[细思极恐-你真的会写java吗?——Lrwin](http://lrwinx.github.io/2017/03/04/%E7%BB%86%E6%80%9D%E6%9E%81%E6%81%90-%E4%BD%A0%E7%9C%9F%E7%9A%84%E4%BC%9A%E5%86%99java%E5%90%97/ "细思极恐-你真的会写java吗?")
