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

User类，Session中“user”`Attribute`对应程序中的类。

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

`@LoginUser`自定义注解，方法中以该注解标注的User对象即为需要从`session`中获取的`user`。

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

使用注解的`Controller`示例。

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

`UserArgumentResolver`自定义`HandlerMethodArgumentResolver`，其中有两个方法。

```java
boolean supportsParameter(MethodParameter methodParameter)
```

入参为`MethodParameter`用来判断该`Resolver`是否支持该方法。下面的例子中当方法参数中带有`LoginUser`注解时返回`true`。

```java
Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
			NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception;
```

当`supportsParameter`方法返回值为`true`时，执行该方法。本例中判断了`session`中是否有“user”的`Attribute`，如果没有则添加一个，如果有则直接返回。

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

## 配置Resolver生效

新增配置类实现`WebMvcConfigurer`将自定义的`UserArgumentResolver`添加到	`HandlerMethodArgumentResolver`List中，使其生效。

```java
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

## 如何使用MockMvc对自定义Resolver进行单测

在进行单元测试时，需要将自定义的`Resolvers`添加到`MockMvc`中。

```java
/**
 * deep-in-spring: ResolverControllerTest
 *
 * @author NoPainAnymore
 * @date 2019-05-17 23:08
 */
public class ResolverControllerTest {

    private MockMvc mockMvc;

    @InjectMocks
    private ResolverController resolverController;

    @Before
    public void setUp() throws Exception {
        MockitoAnnotations.initMocks(this);
        mockMvc = MockMvcBuilders
                .standaloneSetup(resolverController)
                .setCustomArgumentResolvers(new UserArgumentResolver())
                .build();
    }

    @Test
    public void user() throws Exception {
        MockHttpSession session = new MockHttpSession();
        session.setAttribute("user", User.builder().userId("1").userName("nopainanymore").build());
        mockMvc.perform(get("/resolver/user")
                .contentType(MediaType.APPLICATION_JSON)
                .session(session)).andExpect(status().isOk());
    }

}
```

## 参考资料

<https://sdqali.in/blog/2016/01/30/using-custom-arguments-in-spring-mvc-controllers/>
