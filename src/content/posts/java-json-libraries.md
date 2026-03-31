---
title: Java JSON 序列化库详解：Jackson、Gson、Fastjson
description: 本文详细介绍 Java 生态中三种最流行的 JSON 处理库：Jackson、Gson 和 Fastjson。内容将涵盖它们的基本用法、高级特性、性能对比、优缺点分析以及最终的选型建议。目标读者是需要为项目选择 JSON 库或希望深入了解这些库区别的 Java 开发者。
published: 2026-04-01
tags: [Java, Json, Jackson, Gson, Fastjson]
category: Java
lang: zh-CN
image: /images/java-json/json-to-java.png
---

### 1. 概述

本文详细介绍 Java 生态中三种最流行的 JSON 处理库：**Jackson**、**Gson** 和 **Fastjson**。内容将涵盖它们的基本用法、高级特性、性能对比、优缺点分析以及最终的选型建议。目标读者是需要为项目选择 JSON 库或希望深入了解这些库区别的 Java 开发者。

### 2. 库简介

#### 2.1 Jackson

- **官网**： https://github.com/FasterXML/jackson
- **简介**： Jackson 是 Java 生态中事实上的标准 JSON 处理库，功能极其丰富，性能优异。它是 Spring Framework 等众多主流框架的默认选择。Jackson 的核心是基于流的解析器（Streaming API），并在此基础上构建了数据绑定（Data Binding）功能，使开发者可以方便地与 POJO（Plain Old Java Object）进行转换。

#### 2.2 Gson

- **官网**： https://github.com/google/gson
- **简介**： 由 Google 开发，以其简单的 API 和易用性著称。Gson 的设计初衷是提供一种简单的方式将 Java 对象转换为 JSON 字符串（序列化），以及将 JSON 字符串转换为 Java 对象（反序列化）。它的上手难度很低，对于简单的场景非常友好。

#### 2.3 Fastjson

- **官网**： https://github.com/alibaba/fastjson
- **简介**： 由阿里巴巴开发，主打极致性能。在诸多性能基准测试中，Fastjson 的序列化/反序列化速度常常名列前茅。它提供了完整的 JSON 功能，并且 API 设计也较为直接。然而，其历史上出现过较多安全漏洞，这是选型时需要重点考虑的因素。

### 3. 详细使用说明

#### 3.1 基本依赖引入（Maven）

```xml
<!-- Jackson -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.2</version>
</dependency>

<!-- Gson -->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version>
</dependency>

<!-- Fastjson -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>2.0.40</version> <!-- 注意：强烈建议使用最新安全版本 -->
</dependency>
```

#### 3.2 基本用法示例

假设我们有一个简单的 Java 对象 `User`：

```java
public class User {
    private String name;
    private int age;
    private String email;

    // 无参构造器、Getter 和 Setter 方法必须存在
    public User() {}

    public User(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
    }
    // ... getters and setters
}
```

**1. Jackson**

```java
import com.fasterxml.jackson.databind.ObjectMapper;

ObjectMapper mapper = new ObjectMapper();
User user = new User("张三", 25, "zhangsan@example.com");

// 序列化：对象 -> JSON字符串
String jsonString = mapper.writeValueAsString(user);
System.out.println(jsonString); // {"name":"张三","age":25,"email":"zhangsan@example.com"}

// 反序列化：JSON字符串 -> 对象
String jsonInput = "{\"name\":\"李四\",\"age\":30,\"email\":\"lisi@example.com\"}";
User userObject = mapper.readValue(jsonInput, User.class);
System.out.println(userObject.getName()); // 李四
```

**2. Gson**

```java
import com.google.gson.Gson;

Gson gson = new Gson();
User user = new User("张三", 25, "zhangsan@example.com");

// 序列化
String jsonString = gson.toJson(user);
System.out.println(jsonString); // 输出与 Jackson 类似

// 反序列化
String jsonInput = "{\"name\":\"李四\",\"age\":30,\"email\":\"lisi@example.com\"}";
User userObject = gson.fromJson(jsonInput, User.class);
System.out.println(userObject.getName()); // 李四
```

**3. Fastjson**

```java
import com.alibaba.fastjson.JSON;

User user = new User("张三", 25, "zhangsan@example.com");

// 序列化
String jsonString = JSON.toJSONString(user);
System.out.println(jsonString); // 输出与 Jackson 类似

// 反序列化
String jsonInput = "{\"name\":\"李四\",\"age\":30,\"email\":\"lisi@example.com\"}";
User userObject = JSON.parseObject(jsonInput, User.class);
System.out.println(userObject.getName()); // 李四
```

从基本用法看，三者非常相似。

#### 3.3 高级特性对比

| 特性                      | Jackson                                                      | Gson                                                | Fastjson                                                     |
| :------------------------ | :----------------------------------------------------------- | :-------------------------------------------------- | :----------------------------------------------------------- |
| **注解支持**              | **极其丰富** (`@JsonIgnore`, `@JsonProperty`, `@JsonFormat`, `@JsonInclude` 等) | 支持基本注解 (`@SerializedName`, `@Expose`)         | 支持丰富注解 (`@JSONField`, `@JSONType`)，功能接近 Jackson   |
| **日期格式化**            | 通过 `@JsonFormat` 注解或全局配置                            | 通过 `GsonBuilder.setDateFormat()` 或自定义序列化器 | 通过 `@JSONField(format="yyyy-MM-dd")` 或 `SerializerFeature` |
| **空值处理**              | 通过 `@JsonInclude(Include.NON_NULL)` 控制                   | 通过 `GsonBuilder.serializeNulls()` 控制            | 通过 `SerializerFeature` 控制，如 `WriteMapNullValue`        |
| **多态处理**              | **强大支持** (`@JsonTypeInfo`, `@JsonSubTypes`)              | 支持，但需要自定义 `TypeAdapter`，较为复杂          | 支持 (`@JSONType`, `seeAlso`)，功能强大                      |
| **自定义序列化/反序列化** | 通过继承 `JsonSerializer`/`JsonDeserializer`                 | 通过实现 `JsonSerializer`/`JsonDeserializer` 接口   | 通过实现 `ObjectSerializer`/`ObjectDeserializer` 接口        |
| **JSON Schema 支持**      | 有                                                           | 无                                                  | 无                                                           |
| **树模型**                | `JsonNode`                                                   | `JsonElement`                                       | `JSONObject` / `JSONArray`                                   |
| **流式 API**              | **核心优势**，高性能解析和生成                               | 有，但不如 Jackson 强大和常用                       | 有                                                           |

**Jackson 注解示例：**

```java
public class User {
    @JsonProperty("full_name") // 指定JSON字段名
    private String name;

    @JsonIgnore // 忽略该字段
    private String password;

    @JsonFormat(pattern = "yyyy-MM-dd")
    private Date birthday;

    @JsonInclude(JsonInclude.Include.NON_NULL) // 为空时不序列化
    private String nickname;
    // ... 
}
```

### 4. 优缺点对比

| 方面            | Jackson                                                      | Gson                                                         | Fastjson                                                     |
| :-------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **性能**        | **非常高**，尤其是流式 API。在大多数场景下与 Fastjson 互有胜负，综合表现最优。 | **良好**，但在处理复杂对象和大数据量时通常慢于 Jackson 和 Fastjson。 | **极致序列化速度**，尤其在特定场景下。但反序列化性能优势不一定明显，且高版本由于安全修复性能有所下降。 |
| **功能/灵活性** | **最丰富**，提供了从底层流处理到高级数据绑定的完整解决方案，支持大量数据格式（JSON, XML, CSV等）。 | **简单够用**，API 直观，满足日常大部分需求。高级功能需要自定义，灵活性较差。 | **功能丰富**，注解等功能向 Jackson 看齐，但整体生态和扩展性不如 Jackson。 |
| **易用性**      | **中等**，API 略复杂，需要学习的概念较多（如 `ObjectMapper` 的各种配置）。 | **极高**，`new Gson().toJson/fromJson` 即可完成大部分工作，上手飞快。 | **高**，API 设计简单直接，与 Gson 类似，易于上手。           |
| **社区/生态**   | **最活跃和强大**，是 Spring、JAX-RS 等框架的默认集成库，文档齐全，社区支持好。 | **活跃**，由 Google 维护，社区庞大，稳定可靠。               | **活跃但存在争议**，由阿里巴巴维护，但历史上安全漏洞频发，影响其声誉。 |
| **安全性**      | **非常高**，经过大规模企业级应用验证，安全记录良好。         | **非常高**，同样经过广泛验证，安全性值得信赖。               | **历史记录较差**，曾多次曝出高危反序列化远程代码执行（RCE）漏洞，**选型时需特别谨慎**，务必使用最新版本。 |
| **稳定性**      | **极高**，API 稳定，向后兼容性好。                           | **极高**，API 非常稳定。                                     | **一般**，不同大版本间 API 变化可能较大。                    |

### 5. 总结与选型建议

| 场景                           | 推荐库              | 理由                                                         |
| :----------------------------- | :------------------ | :----------------------------------------------------------- |
| **新项目/企业级项目**          | **Jackson**         | 功能、性能、生态、安全性的最佳平衡。尤其是基于 Spring 的项目，无需额外引入，无缝集成。 |
| **快速原型/简单应用**          | **Gson**            | API 极其简单，学习成本低，能让开发者快速实现功能。对于逻辑不复杂的 Android 应用也是好选择。 |
| **极致性能优先（非敏感场景）** | **Fastjson**        | 如果项目的主要瓶颈在于 JSON 序列化性能，且能承担潜在的安全风险（例如严格的内网环境），并承诺始终跟进最新安全版本，可考虑 Fastjson。 |
| **高安全性要求项目**           | **Jackson 或 Gson** | **绝对避免使用 Fastjson**。金融、政务等对安全有严苛要求的项目应选择经过长期考验、安全记录良好的 Jackson 或 Gson。 |

**最终建议：**

**对于绝大多数 Java 项目，尤其是微服务、Web 后端等企业级应用，Jackson 是毋庸置疑的首选。** 它是功能、性能和可靠性上的"三好学生"，拥有最健康的生态。Gson 则在简单性和易用性上胜出，是轻量级项目的绝佳选择。而 Fastjson，尽管在性能上有其亮点，但其历史上的安全问题像一把达摩克利斯之剑，使得在大多数严肃项目中不推荐使用，除非有非常特殊的性能需求并且能完全掌控其安全风险。

在选择时，请务必结合项目的具体需求、团队的技术背景以及对安全性的要求进行综合判断。
