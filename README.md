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

#### O que é Injeção de Dependência? 

Injeção de Dependência (ou DI) é um padrão de design que basicamente faz com que um objeto receba de fora os recursos (ou dependências) que ele precisa, em vez de ele mesmo criar tudo internamente. Isso deixa o código mais organizado e traz várias vantagens, como: 

- Menor acoplamento entre as partes do sistema 

- Código mais fácil de testar 

- Melhor manutenção no futuro 

Um exemplo bem conhecido de uso desse padrão é o Spring Framework, que é famoso justamente por oferecer um sistema super flexível e eficiente de injeção de dependência.

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
>##### Extensibilidade e Manutenibilidade
A versão com Spring Framework facilita a extensão e a manutenção, pois a injeção de dependência permite adicionar ou modificar funcionalidades sem alterar o código existente.

>##### Concisão e Boilerplate
A versão Java puro com Factory Pattern possui mais código boilerplate (termo utilizado para quando a seções de código que devem ser incluídas em muitos lugares com pouca ou nenhuma alteração), enquanto a versão com Spring é mais concisa devido à gestão automática de dependências.

>##### Facilidade para Adicionar Novas Funcionalidades
Com Spring, basta criar uma nova implementação da interface e anotá-la; com Factory, seria necessário alterar a lógica da fábrica.

>##### Manutenção e Expansão
O Spring torna a manutenção e expansão mais simples, já que o código é modular e as dependências são gerenciadas automaticamente.

>##### Complexidade com o Tempo
A versão com Factory tende a se complicar conforme novos métodos são adicionados, enquanto o Spring distribui a complexidade e preserva a organização do código.

>##### Preservação da Manutenibilidade
A abordagem com Spring preserva melhor a manutenibilidade, reduzindo o acoplamento e facilitando a substituição de componentes.


### Conclusão
Após desenvolver e analisar as duas versões do sistema, concluimos que a versão usando o Spring é mais vantajosa, principalmente quando pensamos em projetos maiores ou que vão evoluir com o tempo. Isso porque facilita questões como manter e expandir o sistema, já que o Spring cuida automaticamente da injeção de dependências e evita aquele monte de código repetitivo, que é mais visível na versão utilizando o Java puro.

Sendo assim, apesar da implementação com Factory funcionar bem em projetos mais simples, ela acaba ficando mais confusa e difícil de manter conforme o sistema cresce. No geral, achamos que usar o Spring traz mais organização, economia de tempo e evita problemas futuros.
