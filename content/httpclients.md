---
title: "Battle of HTTP Clients in the Spring Framework: RestClient, RestTemplate, WebClient, and Feign"
date: 2023-12-28T22:00:58+04:00
draft: false
---

![WebClient Image](https://writebyhumans.com/images/webclient.webp)

**Do you want to call an HTTP URL in the Spring Framework? If so, you need an HTTP client.**

The Spring Framework provides several HTTP client options, depending on your needs you can pick them.

1. **RestTemplate (Deprecated)**

*   A synchronous HTTP client that has been widely used but is now in maintenance mode.
*   Spring recommends using `WebClient` instead.

> _Spring officially marked_ `RestTemplate` _as_ **_deprecated in Spring Framework 6_**_, which was released in_ **_November 2022_**_. The deprecation was part of the transition toward using_ `WebClient`_, which offers better support for modern, reactive programming._
> 
> _Although_ `RestTemplate` _is still available, it is now in_ **_maintenance mode_**_, meaning no new features will be added, and Spring recommends migrating to_ `WebClient`.

## 2. WebClient (Preferred)

*   More modern and flexible than `RestTemplate`.
*   Introduced in Spring WebFlux.
*   Supports both synchronous and reactive (asynchronous) programming.

**3. Feign Client (It’s not native)**

*   **Declarative HTTP Requests** — Define interfaces, and Feign generates the implementation.
*   **Integration with Spring Boot** — Works seamlessly with `@FeignClient` and `@RequestMapping`.
*   **Load Balancing (with Ribbon/Eureka)** — Can work with service discovery.
*   **Interceptor Support** — Allows adding custom headers, authentication, etc.
*   **Retry Mechanism** — Configurable for better resilience.

> _Feign is_ **_not part of the Spring Framework itself_**_, but it is widely used in_ **_Spring Boot applications_** _for declarative REST clients. It was originally developed by Netflix and later integrated into the_ **_Spring Cloud_** _ecosystem as_ **_Spring Cloud OpenFeign_**_._

**4. RestClient (A Modern Synchronous HTTP Client)**

*   **Synchronous HTTP clien**t — `RestClient` is a synchronous HTTP client built upon the foundation of Java's `java.net.http.HttpClient`, introduced in Java 11.
*   **Extensibility** — Offers mechanisms for interceptors and error handling to customize request/response processing.
*   **Spring Integration** — Seamlessly integrates with the broader Spring ecosystem.
*   **Leverages** `java.net.http.HttpClient` - Benefits from the performance and modern protocol support of the standard Java HTTP client.


## Comparison with Spring’s HTTP Clients

**Code Samples**

**1. RestTemplate (Deprecated) — Synchronous Request**

```java
RestTemplate restTemplate = new RestTemplate();  
String url = "https://jsonplaceholder.typicode.com/todos/1";   
ResponseEntity<String> response = restTemplate.getForEntity(url, String.class);   
System.out.println(response.getBody());
```

## 2. WebClient (Preferred) — Asynchronous Request

* **_Recommended_** _for both synchronous and reactive programming._

```java
WebClient webClient = WebClient.create("https://jsonplaceholder.typicode.com");  
String response = webClient.get()  
    .uri("/todos/1")  
    .retrieve()  
    .bodyToMono(String.class)  
    .block();  // Blocking call, use \`.subscribe()\` for fully async  
System.out.println(response);
```

**3.Feign Client — Declarative HTTP Client**

### Define the Feign Client Interface

```java

@FeignClient(name = "todoClient", url = "https://jsonplaceholder.typicode.com")   
public interface TodoClient {   
    @GetMapping("/todos/1")   
    String getTodo();  
 }
```

### Call the Feign Client in a Service

* **_Best for Microservices_** _using_ **_Spring Cloud OpenFeign_**

```java
@Autowired  
private TodoClient todoClient;  
  
public void fetchTodo() {  
    System.out.println(todoClient.getTodo());  
}
```

**4. RestClient**

```java
@Autowired  
 private TodoClient todoClient;  
  
 public void fetchTodo() {   
    System.out.println(todoClient.getTodo());  
 }

import org.springframework.web.client.RestClient;  
import org.springframework.http.ResponseEntity;  
import org.springframework.http.MediaType;  
  
public class RestClientExample {  
    public static void main(String[] args) {  
        RestClient restClient = RestClient.create();  
  
        ResponseEntity<String> response = restClient.get()  
                .uri("https://api.example.com/users/1")  
                .accept(MediaType.APPLICATION\_JSON)  
                .retrieve()  
                .toEntity(String.class);  
  
        System.out.println("Status Code: " + response.getStatusCode());  
        System.out.println("Body: " + response.getBody());  
    }  
}
```

## Conclusion

Each HTTP client in the Spring ecosystem serves a different purpose:

*   **RestTemplate** is simple but deprecated, making it unsuitable for new projects.
*   **WebClient** is the modern, flexible choice, supporting both synchronous and reactive programming.
*   **RestClient** is Spring’s modern, fluent, and synchronous HTTP client, ideal for straightforward blocking communication and a natural successor to RestTemplate. While powerful, it lacks built-in reactive or declarative features and requires external integration for load balancing in microservices.
*   **Feign Client** is the best option for microservices, offering a declarative approach to REST APIs.

For new projects, **WebClient** is the recommended choice unless you’re working with **Spring Cloud**, where **Feign** might be a better fit.
