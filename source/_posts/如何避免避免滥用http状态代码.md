---
title: "如何避免避免滥用http状态代码"
categories: [ "Java" ]
tags: [ "java","restful","状态码" ]
draft: false
slug: "avoid-abusing-http-status-code"
date: "2020-01-21 15:21:00"
---

在使用JAX-RS，Spring Boot或任何其他技术的RESTful Web服务中,必须使用机器可读且人性化的自定义业务错误代号。

假设您正在编写订单处理系统，客户可能没有资格使用某种付款方式下订单，您想通过Web前端或HTTP API调用的结果向用户反馈这种问题。可以通过查看http规范，并使用代码405：“不允许使用方法”来解决。

听起来完全符合您的需求。它可以在您的测试中工作得很好，并且可以投入生产正常运行一段时间。但是随后，对负载均衡器进行例行更新时会破坏系统。很快，在开发人员和运维人员之间进行了相互指责，最终爆发了一场全面的责任大战。看起来好像是由ops运维人员进行更新引起的问题，但他们声称负载平衡器中没有错误，原因是由于旧版本的安全性问题，必须对其进行更新。


<!--more-->


实际上确实应该归咎于开发人员：您误用了具有特定语义的技术代码，以表示完全不同的业务语义-这绝不是一个好主意。在这种情况下，明确使用了可以允许缓存的405代码。

http状态代码（请参阅[rfc-7231](#section-6)或格式正确的[https://httpstatuses.com](https://httpstatuses.com/)）精确地指定了不同的情况，主要是细粒度的技术问题。特定于应用程序的问题仅限于通用代码[400 Bad Request](#section-6.5.1)（以及其他一些[500 Internal Server Error](#section-6.6.1)代码）或状态代码，它们可用于表示客户端或服务器端的一般故障。但是我们需要区分许多情况。我们还能如何将各种问题传达给客户端？

http协议允许几乎在任何响应中不仅在GET请求后具有200 OK状态，还可以包含一个正文（在RFC中称为“实体”）。在这种情况下，大多数系统都会显示自定义html错误页面。如果我们使此主体机器可读，则我们的客户可以做出适当的反应。为每个端点甚至每个应用程序定义一个新的文档类型是一项繁重的工作：您不仅需要编写代码，而且还要编写文档，测试并将其全部传达给客户端等，并且客户端必须使用对于一个请求正是这种格式，对于另一个请求正是这种格式，这太麻烦了。有一个标准会很好-实际上，有一个标准：RFC-7807。

[***\*RFC-7807\****](https://tools.ietf.org/html/rfc7807)

该标准定义了一种媒体类型application/problem+json（或+xml）以及与其精确语义一起使用的标准字段。这是一个简短的摘要：

· type：一个URI，用于标识发生了什么类型的问题。理想情况下，它应该是有关此类错误的详细信息的文档的稳定 URL，例如https://api.myshop.example/problems/not-entitled-for-payment-method；但它也可以是[URN](https://en.wikipedia.org/wiki/Uniform_Resource_Name)，例如urn:problem-type:not-entitled-for-payment-method。在任何情况下，更改都type被定义为API 的重大更改，因此对于客户而言，使用此方法切换到不同的问题情况是安全的。

· title：对问题的一般类型的非正式的，人类可读的简短描述，例如You're not entitled to use this payment method。可以在不破坏API的情况下进行更改。

· status：重复响应状态代码，例如403为Forbidden。由于代理更改了http状态代码，因此服务器抛出的内容和客户端收到的内容可能有所不同。它仅是帮助调试的建议，因此可以在不破坏API的情况下对其进行更改。

· detail：关于错误原因的易于理解的完整描述，例如Customer 123456 has only GOLD status but needs PLATINUM to be entitled to order the sum of USD 1,234.56 on account.可以在不破坏API的情况下进行更改。

· instance：用于标识问题具体发生的URI。如果这是URL，则应提供有关此事件的详细信息，例如，指向您的日志https://logging.myshop.example/prod?query=d294b32b-9dda-4292-b51f-35f65b4bf64d-请注意，仅仅因为它是URL，并不意味着所有人都必须可以访问它！如果您甚至不想在Web上提供有关日志记录系统的详细信息，也可以生成一个UUID URN，例如urn:uuid:d294b32b-9dda-4292-b51f-35f65b4bf64d。可以在不破坏API的情况下进行更改。

· 所有其他字段均为扩展名，即自定义的机器可读字段；例如customer-status或order-sum。扩展也可以是复杂的类型，即列表或包含多个字段的对象，只要它们可以（反）序列化即可。客户可能想将此显示给客户。您可以在不破坏API的情况下添加新扩展名，但是删除扩展名（或更改语义）是对API 的重大更改。

***\*Spring Boot\****

假设我们有一个REST控制器OrderBoundary（我在这里使用[BCE](https://en.wikipedia.org/wiki/Entity-control-boundary)术语“边界”）：

```java
@RestController
@RequestMapping(path = "/orders")
@RequiredArgsConstructor
public class OrderBoundary {
    private final OrderService service;
 
    @PostMapping
    public Shipment order(@RequestParam("article") String article) {
        return service.order(article);
    }
}
```

这个OrderService也许抛出UserNotEntitledToOrderOnAccountException错误。

默认情况下，Spring Boot已经提供了一个json错误体，但这是非常技术性的。它包含以下字段：

· status+ error：例如403和Forbidden

· message：例如 You're not entitled to use this payment method

· path：例如 /orders

· timestamp：例如 2020-01-10T12:00:00.000+0000

· trace：堆栈跟踪

我们需要通过注释以下内容来指定UserNotEntitledToOrderOnAccountException错误的对应http状态代码和消息：

```java
@ResponseStatus(code = FORBIDDEN,
    reason = "You're not entitled to use this payment method")
public class UserNotEntitledToOrderOnAccountException
  extends RuntimeException {
    ...
}
```

注意，没有统一的字段可以区分不同的错误情况，这是我们的主要用例。因此，我们需要采取不同的路线：

\1. 手动异常映射

最基本的方法是手动捕获和映射异常，即在我们中，OrderBoundary控制器中我们返回的ResponseEntity中带有两种不同主体类型之一：要么是商品已经运货或出现了问题的详细信息：

```java
public class OrderBoundary {
    @PostMapping
    public ResponseEntity<?> order(@RequestParam("article") String article) {
        try {
            Shipment shipment = service.order(article);
            return ResponseEntity.ok(shipment);
 
        } catch (UserNotEntitledToOrderOnAccountException e) {
            ProblemDetail detail = new ProblemDetail();
            detail.setType(URI.create("https://api.myshop.example/problems/" +
                "not-entitled-for-payment-method")); ①
            detail.setTitle("You're not entitled to use this payment method");
            detail.setInstance(URI.create(
                "urn:uuid:" + UUID.randomUUID())); ②
 
            log.debug(detail.toString(), exception); ③
 
            return ResponseEntity.status(FORBIDDEN).
                contentType(ProblemDetail.JSON_MEDIA_TYPE)
                .body(detail);
        }
    }
}
```

①：选择type字段使用固定的URL ，例如对Wiki。

②：选择使用随机UUID URN instance。

③：记录了问题的详细信息和堆栈跟踪，因此我们可以在日志中搜索UUID，instance以查看导致问题的日志上下文中的所有详细信息。

***\*问题细节\****

ProblemDetail班是微不足道的（使用了Lombok）：

```java
@Data
public class ProblemDetail {
    public static final MediaType JSON_MEDIA_TYPE =
        MediaType.valueOf("application/problem+json");
 
    private URI type;
    private String title;
    private String detail;
    private Integer status;
    private URI instance;
}
```

***\*错误处理器\****

如果要转换的异常很多，手动映射代码可能会增长很多。通过使用一些约定，我们可以为所有异常将其替换为通用映射。我们可以将还原OrderBoundary为简单形式，而使用异常处理程序控制器建议：

```java
@Slf4j
@ControllerAdvice ①
public class ProblemDetailControllerAdvice {
    @ExceptionHandler(Throwable.class) ②
    public ResponseEntity<?> toProblemDetail(Throwable throwable) {
        ProblemDetail detail = new ProblemDetailBuilder(throwable).build();
 
        log.debug(detail.toString(), throwable); ③
 
        return ResponseEntity.status(detail.getStatus())
            .contentType(ProblemDetail.JSON_MEDIA_TYPE)
            .body(detail);
    }
}
```

 

①：使实际的异常处理程序方法可由Spring发现。

②：我们处理所有异常和错误。

③：我们记录详细信息（包括instance）和堆栈跟踪。

有趣的部分在ProblemDetailBuilder里面。

使用的约定是：

· type：托管于的异常的javadoc的URL https://api.myshop.example/apidocs。这可能不是最稳定的URL，但此演示可以。

· title：使用简单的类名，将驼峰式大小写转换为空格。

· detail：异常消息。

· instance：使用随机UUID URN。

· status：如果将异常注释为Status使用该注释；否则使用500 Internal Server Error。

```java
@Retention(RUNTIME)
@Target(TYPE)
public @interface Status {
    int value();
}
```

请注意，您应该非常谨慎地使用约定：它们永远不会令人惊讶。ProblemDetailBuilder是几行代码，但是阅读起来应该很有趣：

```java
@RequiredArgsConstructor
class ProblemDetailBuilder {
    private final Throwable throwable;
 
    ProblemDetail build() {
        ProblemDetail detail = new ProblemDetail();
        detail.setType(buildType());
        detail.setTitle(buildTitle());
        detail.setDetail(buildDetailMessage());
        detail.setStatus(buildStatus());
        detail.setInstance(buildInstance());
        return detail;
    }
 
    private URI buildType() {
        return URI.create("https://api.myshop.example/apidocs/" +
            javadocName(throwable.getClass()) + ".html");
    }
 
    private static String javadocName(Class<?> type) {
        return type.getName()
            .replace('.', '/') // the package names are delimited like a path
            .replace('$', '.'); // nested classes are delimited with a period
    }
 
    private String buildTitle() {
        return camelToWords(throwable.getClass().getSimpleName());
    }
 
    private static String camelToWords(String input) {
        return String.join(" ", input.split("(?=\\p{javaUpperCase})"));
    }
 
    private String buildDetailMessage() {
        return throwable.getMessage();
    }
 
    private int buildStatus() {
        Status status = throwable.getClass().getAnnotation(Status.class);
        if (status != null) {
            return status.value();
        } else {
            return INTERNAL_SERVER_ERROR.getStatusCode();
        }
    }
 
    private URI buildInstance() {
        return URI.create("urn:uuid:" + UUID.randomUUID());
    }
}
```

您可以将此错误处理提取到单独的模块中，并且如果您可以与其他团队达成相同的约定，则可以共享它。您甚至可以简单地使用其他人（例如[mine](https://github.com/t1/problem-details) artifact）定义的问题详细信息工件，该工件还允许扩展字段和其他内容。

***\*客户端\****

我不想在整个域代码中散布技术细节，因此我提取了一个OrderServiceClient类来进行调用并将这些问题详细信息映射回异常。我希望领域代码看起来像这样：

```java
@RequiredArgsConstructor
public class MyApplication {
    private final OrderServiceClient client;
    public OrderStatus handleOrder(String articleId) {
        try {
            Shipment shipment = client.postOrder(articleId);
            // store shipment
            return SHIPPED;
        } catch (UserNotEntitledToOrderOnAccount e) {
            return NOT_ENTITLED_TO_ORDER_ON_ACCOUNT;
        }
    }
}
```

有趣部分在OrderServiceClient，在其中手动映射细节错误：

```java
public class OrderServiceClient {
    public Shipment postOrder(String article) {
        MultiValueMap<String, String> form = new LinkedMultiValueMap<>();
        form.add("article", article);
        RestTemplate template = new RestTemplate();
        try {
            return template.postForObject(BASE_URI + "/orders", form, Shipment.class);
        } catch (HttpStatusCodeException e) {
            String json = e.getResponseBodyAsString();
            ProblemDetail problemDetail = MAPPER.readValue(json, ProblemDetail.class);
            log.info("got {}", problemDetail);
            switch (problemDetail.getType().toString()) {
                case "https://api.myshop.example/apidocs/com/github/t1/problemdetaildemoapp/" +
                        "OrderService.UserNotEntitledToOrderOnAccount.html":
                    throw new UserNotEntitledToOrderOnAccount();
                default:
                    log.warn("unknown problem detail type [" +
                        ProblemDetail.class + "]:\n" + json);
                    throw e;
            }
        }
    }
 
    private static final ObjectMapper MAPPER = new ObjectMapper()
        .disable(FAIL_ON_UNKNOWN_PROPERTIES);
}
```

下面是响应错误处理，Spring REST客户端上还有一种机制可以使我们对该处理进行概括：

```java
public class OrderServiceClient {
    public Shipment postOrder(String article) {
        MultiValueMap<String, String> form = new LinkedMultiValueMap<>();
        form.add("article", article);
        RestTemplate template = new RestTemplate();
        template.setErrorHandler(new ProblemDetailErrorHandler()); ①
        return template.postForObject(BASE_URI + "/orders", form,
            Shipment.class);
    }
}
①：此行替换了try-catch块。
```

ProblemDetailErrorHandler使用了所有约定; 包括一些错误处理。在这种情况下，我们会记录一条警告，然后回退到Spring默认处理方式：

```java
    @Override public void handleError(ClientHttpResponse response) throws IOException {
        if (ProblemDetail.JSON_MEDIA_TYPE.isCompatibleWith(
            response.getHeaders().getContentType())) {
            triggerException(response);
        }
        super.handleError(response);
    }
 
    private void triggerException(ClientHttpResponse response) throws IOException {
        ProblemDetail problemDetail = readProblemDetail(response);
        if (problemDetail != null) {
            log.info("got {}", problemDetail);
            triggerProblemDetailType(problemDetail.getType().toString());
        }
    }
 
    private ProblemDetail readProblemDetail(ClientHttpResponse response) throws IOException {
        ProblemDetail problemDetail = MAPPER.readValue(response.getBody(), ProblemDetail.class);
        if (problemDetail == null) {
            log.warn("can't deserialize problem detail");
            return null;
        }
        if (problemDetail.getType() == null) {
            log.warn("no problem detail type in:\n" + problemDetail);
            return null;
        }
        return problemDetail;
    }
 
    private void triggerProblemDetailType(String type) {
        if (isJavadocUrl(type)) {
            String className = type.substring(36, type.length() - 5)
                .replace('.', '$').replace('/', '.');
            try {
                Class<?> exceptionType = Class.forName(className);
                if (RuntimeException.class.isAssignableFrom(exceptionType)) {
                    Constructor<?> constructor = exceptionType.getDeclaredConstructor();
                    throw (RuntimeException) constructor.newInstance();
                }
                log.warn("problem detail type [" + type + "] is not a RuntimeException");
            } catch (ReflectiveOperationException e) {
                log.warn("can't instantiate " + className, e);
            }
        } else {
            log.warn("unknown problem detail type [" + type + "]");
        }
    }
 
    private boolean isJavadocUrl(String typeString) {
        return typeString.startsWith("https://api.myshop.example/apidocs/")
            && typeString.endsWith(".html");
    }
 
    private static final ObjectMapper MAPPER = new ObjectMapper()
        .disable(FAIL_ON_UNKNOWN_PROPERTIES);
}
```

从URL恢复异常类型不是理想的做法，因为它将客户端与服务器紧密地耦合在一起，即，它假定我们在同一包中使用相同的类。对于演示来说已经足够好了，但是要正确地进行演示，您需要一种注册异常或对其进行扫描的方法，例如在[我的库中](https://github.com/t1/problem-details)，该方法还允许扩展字段和其他内容。

***\*JAX-RS\****

如果您不喜欢JAX-RS，则可能要跳到[Summary](#summary)。

这部分处理可点击标题见原文。

***\*总结\****

避免滥用http状态代码；那是个蛇坑。而是生成标准化的并因此可互操作的问题详细信息，这比您想象的要容易。为了不浪费业务逻辑代码，可以在服务器端和客户端使用异常。通过引入一些约定，大多数代码甚至可以通用，并可以在多个应用程序中重用。

[此](https://github.com/t1/problem-details)实现提供了注解@Type，@Title，@Status，@Instance，@Detail，并@Extension为您的自定义异常。它与Spring Boot以及JAX-RS和MicroProfile Rest Client一起使用。Zalando在[问题](https://github.com/zalando/problem)库和[Spring集成中](https://github.com/zalando/problem-spring-web)采用了不同的方法。[problem4j也](https://github.com/malczuuu/problem4j)看起来可用。有一些其他语言的解决方案，例如在GitHub [rfc7807](https://github.com/topics/rfc7807)和[rfc-7807上](https://github.com/topics/rfc-7807)。

 

 文章来源：[https://www.jdon.com/53707](https://www.jdon.com/53707)