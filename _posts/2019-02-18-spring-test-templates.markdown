---
layout: post
title: Templates for Spring Tests
date: 2019-02-18 14:30:00
tags: java spring testing template
short_description: These are a few templates for test setups involving Spring (Boot). Because, who wants to remember that stuff when setting up a new project? 
mathjax: false
---

These are a few templates for test setups involving Spring (Boot). Because: Who wants to remember that stuff when setting up a new project? 

For a demonstration you can refer to this Spring Developer Talk: [Test Driven Development with Spring Boot](https://www.youtube.com/watch?v=s9vt6UJiHg4) ([Git repository 1](https://github.com/mbhave/tdd-with-spring-boot), [Git repository 2](https://github.com/sannidhi/tdd-boot-demo﻿)).

# Gradle Project Setup

```groovy
plugins {
    id 'org.springframework.boot' version '2.1.2.RELEASE'
}

apply plugin: 'java'

repositories() {
    mavenCentral()
    mavenLocal()
}

dependencies {
    //main
    implementation group: 'org.springframework.boot', name: 'spring-boot-starter-web', version: '2.1.2.RELEASE'
    implementation group: 'org.springframework.boot', name: 'spring-boot-starter-data-jpa', version: '2.1.2.RELEASE'
    implementation group: 'javax.xml.bind', name: 'jaxb-api', version: '2.3.1'
    
    //test
    testRuntime group: 'org.springframework.boot', name: 'spring-boot-starter-test', version: '2.1.2.RELEASE'
    testRuntime group: 'com.h2database', name: 'h2', version: '1.4.196'
}

sourceCompatibility = '1.8'
targetCompatibility = '1.8'

springBoot {
    mainClassName = 'org.springbootsample.Application'
}
```

# Templates

## JPA Test

Runs the test within Spring context with in-memory database. Flush entities to better simulate production behavior.

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class JpaTest {
    
    @Autowired
    private TestEntityManager entityManager;
    
    @Test
    public void testSomething() {
        Entity entity = //setup entity
        Entity savedEntity = entityManager.persistFlushFind(entity);
        
        //do JPA stuff
    }
}
```

## Service Test

No Spring context with this style. Executing tests is fast. Plus, mocks for the related services help focusing on the functionality of the tested service.

```java
@RunWith(MockitoJUnitRunner.class)
public class ServiceTest {
    
    @Mock
    private JpaService jpaService;
    
    @Mock
    private OtherService otherService;
    
    private Service service;
    
    @Before
    public void beforeTest() {
        service = new Service(jpaService, otherService);
    }
    
    @Test
    public void testSomething() {
        BDDMockito.given(jpaService.someMethod("param")).willReturn(new Entity("someValue"));
        
        //test service
    }
}
```

## REST Test

Note "controller" definition of `@WebMvcTest`. With this only the listed `@Controller`s will be instantiated by Spring.

```java
@RunWith(SpringRunner.class)
@WebMvcTest(TestedController.class)
public class RestTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private Service someService;
    
    @Test
    public void testSomething() throws Exception {
        BDDMockito.given(someService.someMethod()).willReturn(new Something());
        
        mockMvc.perform(MockMvcRequestBuilders.get("/test/path?param=value"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("something").value("someValue"));
        
        //test more stuff
    }
    
    @Test
    public void testSomethingElse() throws Exception {
        BDDMockito.given(someService.someMethod()).willThrow(new SomeException());
        
        mockMvc.perform(MockMvcRequestBuilders.get("/test/path"))
               .andExpect(status().isNotFound());
        
        //test more stuff
    }
}
```

## Integration Test

```java
@RunWith(SpringRunner.class)
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
public class IntegrationTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @Test
    public void testSomething() {
        ResponseEntity<Something> response = restTemplate.getForEntity("/rest/path", Something.class);
        
        //test stuff on response
    }
    
}
```
