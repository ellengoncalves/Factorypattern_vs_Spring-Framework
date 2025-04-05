# Factorypattern_vs_SpringsFramework

💳 Factory Pattern vs Spring Framework
Sistema de Pagamento com Injeção de Dependência em Java e Spring
Autores: Ariane Sanga, Ellen Gonçalves

📝 Introdução ao Problema
Em um sistema de e-commerce, o pagamento é uma parte crítica.
O desafio foi criar uma solução com três métodos de pagamento: Cartão de Crédito, PIX e PayPal.

Cada versão do sistema usa um único método de pagamento, definido na construção, e não em tempo de execução.

❓ O que é Injeção de Dependência?
Injeção de Dependência (Dependency Injection - DI) é um padrão que:

🔄 Reduz o acoplamento entre classes

🔧 Aumenta a flexibilidade

🧪 Facilita os testes

🛠️ Torna a manutenção mais simples

O Spring Framework é um dos frameworks mais usados para aplicar esse padrão de forma robusta.

🔧 Implementação com Java Puro (Factory)
Interface
java
Copiar
Editar
package com.example.factory.service;

public interface PaymentProcessor {
    void process(double amount);
}
Implementações
Cartão de Crédito:

java
Copiar
Editar
public class CreditCardPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Credit Card]");
        System.out.println("Payment value: $ " + amount);
    }
}
PIX:

java
Copiar
Editar
public class PixPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Pix]");
        System.out.println("Payment value: $ " + amount);
    }
}
PayPal:

java
Copiar
Editar
public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with PayPal]");
        System.out.println("Payment value: $ " + amount);
    }
}
Factory
java
Copiar
Editar
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
Serviço de Pagamento
java
Copiar
Editar
public class PaymentService {
    private final PaymentProcessor processor;

    public PaymentService(PaymentProcessor processor) {
        this.processor = processor;
    }

    public void pay(double amount) {
        processor.process(amount);
    }
}
🌱 Implementação com Spring Framework
Interface
java
Copiar
Editar
public interface PaymentProcessor {
    void process(double amount);
}
Implementações
Cartão de Crédito:

java
Copiar
Editar
@Component
@Qualifier("credit")
public class CreditCardPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Credit Card]");
        System.out.println("Payment value: $ " + amount);
    }
}
PIX:

java
Copiar
Editar
@Component
@Qualifier("pix")
public class PixPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with Pix]");
        System.out.println("Payment value: $ " + amount);
    }
}
PayPal:

java
Copiar
Editar
@Component
@Qualifier("paypal")
public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with PayPal]");
        System.out.println("Payment value: $ " + amount);
    }
}
Serviço de Pagamento com Spring
java
Copiar
Editar
@Service
public class PaymentService {

    @Autowired
    @Qualifier("pix") // Pode ser "credit" ou "paypal"
    private PaymentProcessor paymentProcessor;

    public void pay(double amount) {
        paymentProcessor.process(amount);
    }
}
Classe Principal
java
Copiar
Editar
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
📊 Comparação entre as Abordagens
Critério	Java Puro (Factory)	Spring (Injeção de Dependência)
🔗 Acoplamento	Médio (depende da Factory)	Baixo (injeção direta)
🔄 Flexibilidade	Média (alteração manual)	Alta (muda o @Qualifier)
🧪 Testabilidade	Média	Alta (mock fácil)
🧠 Complexidade Inicial	Baixa	Média (requer estrutura Spring)
📈 Escalabilidade	Limitada	Alta (ideal para sistemas grandes)
✅ Conclusão
A injeção de dependência torna o sistema mais modular, escalável e fácil de testar.

A versão com Java puro é ideal para sistemas simples e didáticos.

Já a versão com Spring é melhor para projetos maiores, com mais flexibilidade e organização.

