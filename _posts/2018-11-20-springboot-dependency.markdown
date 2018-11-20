---
layout:     post
title:      "Java SpringBoot Bean Dependency"
subtitle:   "创建SpringBoot Bean时如何解决依赖问题"
date:       2018-11-20
author:     "pinasterist"
header-img: "img/post-bg-mobilephone.jpg"
tags:
    - java
    - springboot
    - bean
---

我们知道SpringBoot基于反射和自省，可以快速初始化各种Bean，并且可以初始化其成员变量。但是当Bean的数量变多，且他们之间有复杂的依赖，如何确定Bean的初始化顺序就变得很麻烦，很棘手了。
为此，SpringBoot提供几种Pattern和一些标签，可以帮助优化Bean的创建和初始化：

* Bean内引用：

假如MyService依赖MyRepository，则可以将myRepository()传递到MyService的构造函数里面，如下：

@Configuration
class MyConfiguration {
    @Bean
    public MyService myService() {
        return new MyService(myRepository());
    }
    @Bean
    public MyRepository myRepository() {
        return new MyRepository();
    }
}

* 带值引用的Bean内引用

如下，注意myRepository的构造函数了吗？那里面有两个null指针。不要奇怪，因为这个函数已经被系统识别为Bean了，所以不管其传入的参数如何，都会得到一致的Bean定义。

public class MyRepository {
    private final String prefix;
    private final String suffix;
    public MyRepository (String prefix, String suffix) {
        this.prefix = prefix;
        this.suffix = suffix;
    } 
    public String findString() {
        return prefix + "-some-string-" + suffix;
    }
}

@Configuration
class MyConfiguration {
    @Bean
    public MyService myService() {
        return new MyService(myRepository(null, null));
    }
    @Bean
    public MyRepository myRepository(@Value("${repo.prefix}") String prefix,
                                     @Value("${repo.suffix}") String suffix) {
        return new MyRepository(prefix, suffix);
    }
}

* 方法参数引用

如下，把myRepository放入MyService的Bean创建函数，就可以实现MyService对MyRepository的依赖。

@Configuration
class MyConfiguration {
    @Bean
    public MyService myService(MyRepository myRepository) {
        return new MyService(myRepository);
    }
}

* 多次引用

如果有多个MyRepository，那么可以通过名字来区别。

@Configuration
class MyConfiguration {
    @Bean
    public MyRepository myFirstRepository() {
        return new MyRepository("first", "repository");
    }
    //a bean that will be injected by name into myService
    @Bean
    public MyRepository mySecondRepository() {
        return new MyRepository("second", "repository");
    }
    @Bean
    public MyService myService(MyRepository mySecondRepository) {
        return new MyService(myRepository);
    }
}

或者通过Qualify标签。

@Configuration
class MyConfiguration {
    //a bean that will be injected by name into myService
    @Bean
    public MyRepository myFirstRepository() {
        return new MyRepository("first", "repository");
    }
    @Bean
    public MyRepository mySecondRepository() {
        return new MyRepository("second", "repository");
    }
    @Bean
    public MyService myService(@Qualifier("myFirstRepository") MyRepository someRepository) {
        return new MyService(someRepository);
    }
}

* Configuration之间的依赖

@Configuration
class FirstConfiguration {
    @Bean
    public FirstService firstService() {
        return new FirstService();
    }
}

@Configuration
@Import({FirstConfiguration.class})
class SecondConfiguration {
    @Bean
    public SecondService secondService(FirstService firstService) {
        return new SecondService(firstService);
    }
}

当然，对firstService的引用还有两种方法，如下：

# 使用Autowired标签
@Configuration
class SecondConfiguration {
    @Autowired
    private FirstService firstService;
    @Bean
    public SecondService secondService() {
        return new SecondService(firstService);
    }
}

# 使用FirstConfiguration Bean
@Configuration
class SecondConfiguration {
    @Autowired
    private FirstConfiguration firstConfiguration;
    @Bean
    public SecondService secondService() {
        return new SecondService(firstConfiguration.firstService());
    }
}

* 打破循环依赖

如下，A依赖B，B又依赖A。

@Component
public class CircularDependencyA {
 
    private CircularDependencyB circB;
 
    @Autowired
    public void setCircB(CircularDependencyB circB) {
        this.circB = circB;
    }
 
    public CircularDependencyB getCircB() {
        return circB;
    }
}

@Component
public class CircularDependencyB {
 
    private CircularDependencyA circA;
 
    private String message = "Hi!";
 
    @Autowired
    public void setCircA(CircularDependencyA circA) {
        this.circA = circA;
    }
 
    public String getMessage() {
        return message;
    }
}

# 重构，这个是最彻底的办法，但是很多时候无法做到

# 使用@PostConstruct标签

@Component
public class CircularDependencyA {
 
    @Autowired
    private CircularDependencyB circB;
 
    @PostConstruct
    public void init() {
        circB.setCircA(this);
    }
 
    public CircularDependencyB getCircB() {
        return circB;
    }
}

@Component
public class CircularDependencyB {
 
    private CircularDependencyA circA;
     
    private String message = "Hi!";
 
    public void setCircA(CircularDependencyA circA) {
        this.circA = circA;
    }
     
    public String getMessage() {
        return message;
    }
}

# 使用@Lazy标签

@Component
public class CircularDependencyA {
 
    private CircularDependencyB circB;
 
    @Autowired
    public CircularDependencyA(@Lazy CircularDependencyB circB) {
        this.circB = circB;
    }
}

# 使用Setter/Field注入

@Component
public class CircularDependencyA {
 
    private CircularDependencyB circB;
 
    @Autowired
    public void setCircB(CircularDependencyB circB) {
        this.circB = circB;
    }
 
    public CircularDependencyB getCircB() {
        return circB;
    }
}

@Component
public class CircularDependencyB {
 
    private CircularDependencyA circA;
 
    private String message = "Hi!";
 
    @Autowired
    public void setCircA(CircularDependencyA circA) {
        this.circA = circA;
    }
 
    public String getMessage() {
        return message;
    }
}

# 使用ApplicationContextAware和InitializingBean

@Component
public class CircularDependencyA implements ApplicationContextAware, InitializingBean {
 
    private CircularDependencyB circB;
 
    private ApplicationContext context;
 
    public CircularDependencyB getCircB() {
        return circB;
    }
 
    @Override
    public void afterPropertiesSet() throws Exception {
        circB = context.getBean(CircularDependencyB.class);
    }
 
    @Override
    public void setApplicationContext(final ApplicationContext ctx) throws BeansException {
        context = ctx;
    }
}

@Component
public class CircularDependencyB {
 
    private CircularDependencyA circA;
 
    private String message = "Hi!";
 
    @Autowired
    public void setCircA(CircularDependencyA circA) {
        this.circA = circA;
    }
 
    public String getMessage() {
        return message;
    }
}


