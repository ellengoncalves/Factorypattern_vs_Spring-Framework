# Factory pattern vs SpringsFramework

Sistema de Pagamento com Injeção de Dependência em Java e Spring
Autores: Ariane Sanga, Ellen Gonçalves

### Introdução ao problema
Desenvolvimento de um sistema de pagamentos para um e-commerce, onde ele precisa oferecer suporte a pagamentos por três métodos:
- Cartão de crédito 
- PIX; ou 
- PayPal. 

A ideia é que cada instância do sistema use apenas um dos métodos de pagamento, que deve ser definido no instante em que é gerado o build do projeto.

#### O projeto precisa conter:
- Uma interface PaymentProcessor com um método process(double amount).
- Três implementações dessa interface, uma para cada meio de pagamento.
- Um PaymentService que não conhece as implementações concretas – ele apenas depende da interface.

#### Requisitos:
- Manter o baixo acoplamento entre os componentes.
- Resolver esse problema usando Java puro e padrões de projeto, como o Factory, mas também está avaliando adotar o padrão de injeção de dependências implementado pelo Spring Framework.
- Desenvolver uma prova de conceito (Proof of Concept – PoC) do uso do Spring e compará-la com uma implementação tradicional, usando Java e padrões de projeto. Assim, vocês terão que desenvolver duas versões do sistema

### Explicação sobre injeção de dependência



### Implementação em Java puro

##### Classe PaymentProcessor
```java
public interface PaymentProcessor {
    void process(double amount);
}
```

##### Classe para os métodos de pagamento

```java
public class CreditCardPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Credit Card]");
        System.out.println("Payment value: $ " + amount);
    }
}
```

```java
public class PixPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Pix]");
        System.out.println("Payment value: $ " + amount);
    }
}
```

```java
public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with PayPal]");
        System.out.println("Payment value: $ " + amount);
    }
}
```
##### Classe PaymentProcessorFactory

```java
public class PaymentProcessorFactory {
    public static PaymentProcessor getProcessor(String type) {
        switch (type.toLowerCase()) {
            case "credit": return new CreditCardPaymentProcessor();
            case "pix": return new PixPaymentProcessor();
            case "paypal": return new PayPalPaymentProcessor();
            default: throw new IllegalArgumentException("Invalid payment type: " + type);
        }
    }
}
```
##### Classe PaymentService

```java
public class PaymentService {
    private final PaymentProcessor processor;

    public PaymentService(PaymentProcessor processor) {
        this.processor = processor;
    }

    public void pay(double amount) {
        processor.process(amount);
    }
}
```

### Implementação com Spring

##### Interface

```java
public interface PaymentProcessor {
    void process(double amount);
}
```
##### Classe para os métodos de pagamento

```java
@Component
@Qualifier("credit")
public class CreditCardPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Credit Card]");
        System.out.println("Payment value: $ " + amount);
    }
}
```

```java
@Component
@Qualifier("pix")
public class PixPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Pix]");
        System.out.println("Payment value: $ " + amount);
    }
}
```
##### Classe PaymentProcessor

```java
@Component
@Qualifier("paypal")
public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with PayPal]");
        System.out.println("Payment value: $ " + amount);
    }
}
```
##### Classe PaymentService

```java
@Service
public class PaymentService {

    @Autowired
    @Qualifier("pix") // Pode ser "credit" ou "paypal"
    private PaymentProcessor paymentProcessor;

    public void pay(double amount) {
        paymentProcessor.process(amount);
    }
}
```
##### Classe principal (Main)

```java
@SpringBootApplication
public class Application implements CommandLineRunner {

    @Autowired
    private PaymentService paymentService;

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        paymentService.pay(350.00);
    }
}
```

### Comparação entre as abordagens


### Conclusão

