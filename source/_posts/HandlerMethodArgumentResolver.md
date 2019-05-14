---
title: HandlerMethodArgumentResolver
entitle: SpringBoot-HandlerMethodArgumentResolver
tags: [SpringBoot,HandlerMethodArgumentResolver]
categories:
- SpringBoot
- HandlerMethodArgumentResolver
date: 2019-05-14 22:52:37
---



本文介绍SpringBoot中如何自定义HandlerMethodArgumentResolver以及怎样对对应的Controller进行单测。

<!--more-->

## HandlerMethodArgumentResolver
`HandlerMethodArgumentResolver`，是一个策略接口，目的是将给定Request中的方法参数解析为给定请求的上下文中的参数值。即可以自定义Request方法中的数据绑定，比如可以将用户信息置于session中，通过实现`HandlerMethodArgumentResolver`接口，实现session中数据和代码中的类进行数据绑定。
> Strategy interface for resolving method parameters into argument values in the context of a given request.
 

```java
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.experimental.Accessors;

/**
 * deep-in-spring: User
 *
 * @author NoPainAnymore
 * @date 2019-05-14 22:57
 */
@Data
@Accessors(chain = true)
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class User {

    private String userId;

    private String userName;

}



/**
 * deep-in-spring: LoginUser
 *
 * @author NoPainAnymore
 * @date 2019-05-14 22:56
 */
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface LoginUser {
}


```

```java
/**
 * deep-in-spring: ResolverController
 *
 * @author NoPainAnymore
 * @date 2019-05-14 22:56
 */
@RestController
@RequestMapping("/resolver")
@Slf4j
public class ResolverController {

    private static final Gson gson = new Gson();

    @GetMapping("/user")
    public String user(@LoginUser User user) {
        log.info("ResolverController- user:{}", gson.toJson(user));
        return user.getUserName();
    }
}
```



```java
import com.google.gson.Gson;
import lombok.extern.slf4j.Slf4j;
import org.springframework.core.MethodParameter;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.util.Optional;

/**
 * deep-in-spring: UserArgumentResolver
 *
 * @author NoPainAnymore
 * @date 2019-05-14 22:55
 */
@Component
@Slf4j
public class UserArgumentResolver implements HandlerMethodArgumentResolver {

    private static final Gson gson = new Gson();

    @Override
    public boolean supportsParameter(MethodParameter methodParameter) {
        return methodParameter.hasParameterAnnotation(LoginUser.class);
    }

    @Override
    public Object resolveArgument(MethodParameter methodParameter, ModelAndViewContainer modelAndViewContainer, NativeWebRequest nativeWebRequest, WebDataBinderFactory webDataBinderFactory) throws Exception {
        Optional<User> userOptional = Optional.ofNullable((User) nativeWebRequest.getAttribute("user", RequestAttributes.SCOPE_SESSION));
        User user = userOptional.orElseGet(() -> {
            log.info("UserArgumentResolver- resolveArgument- user is null, set user");
            User add = User.builder().userId("1").userName("noPainAnyMore").build();
            HttpServletRequest httpServletRequest = (HttpServletRequest) nativeWebRequest.getNativeRequest();
            HttpSession session = httpServletRequest.getSession();
            session.setAttribute("user", add);
            return add;
        });
        log.info("UserArgumentResolver- resolveArgument- user{}", gson.toJson(user));
        return user;
    }
}

```


```java
import java.util.List;

/**
 * deep-in-spring: UserConfig
 *
 * @author NoPainAnymore
 * @date 2019-05-14 23:05
 */
@Configuration
@Slf4j
public class UserConfig implements WebMvcConfigurer {

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        log.info("UserConfig- addArgumentResolvers- initial resolver");
        resolvers.add(new UserArgumentResolver());
    }
}


```
### supportsParameter


### resolveArgument


## 配置Resolver生效


## 如何使用MockMvc对自定义Resolver进行单测



## 参考资料
<https://sdqali.in/blog/2016/01/30/using-custom-arguments-in-spring-mvc-controllers/>
