# Factorypattern_vs_SpringsFramework

Sistema de Pagamento com Injeção de Dependência em Java e Spring
Autores: Ariane Sanga, Ellen Gonçalves

Introdução ao Problema
Em um sistema de e-commerce, o pagamento é uma parte crítica. O desafio foi criar uma solução com três métodos de pagamento: Cartão de Crédito, PIX e PayPal. Cada versão do sistema utiliza apenas um método de pagamento, definido durante a construção, e não em tempo de execução.

O que é Injeção de Dependência?
Injeção de Dependência (Dependency Injection - DI) é um padrão de design usado para reduzir o acoplamento entre classes e melhorar a testabilidade e a manutenção do código.

Ao invés de uma classe instanciar diretamente suas dependências, elas são injetadas de fora, promovendo:

Menor acoplamento;

Maior flexibilidade;

Testes mais fáceis;

Manutenção facilitada.

O Spring Framework é um dos exemplos mais conhecidos de uso desse padrão, oferecendo um sistema robusto e flexível de injeção de dependência.

Implementação com Java Puro (com Factory)
Na primeira versão, utilizamos Java puro, com o padrão Factory para escolher qual processador de pagamento será usado.

Interface PaymentProcessor
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
package com.example.factory.service;

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
package com.example.factory.service;

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
package com.example.factory.service;

public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with PayPal]");
        System.out.println("Payment value: $ " + amount);
    }
}
Classe PaymentProcessorFactory
java
Copiar
Editar
package com.example.factory.service;

public class PaymentProcessorFactory {
    public static PaymentProcessor getProcessor(String type) {
        switch (type.toLowerCase()) {
            case "credit":
                return new CreditCardPaymentProcessor();
            case "pix":
                return new PixPaymentProcessor();
            case "paypal":
                return new PayPalPaymentProcessor();
            default:
                throw new IllegalArgumentException("Invalid payment type: " + type);
        }
    }
}
Classe PaymentService
java
Copiar
Editar
package com.example.factory.service;

public class PaymentService {
    private final PaymentProcessor processor;

    public PaymentService(PaymentProcessor processor) {
        this.processor = processor;
    }

    public void pay(double amount) {
        processor.process(amount);
    }
}
Implementação com Spring Framework
Nessa versão, usamos o Spring, que se encarrega de instanciar e injetar o processador de pagamento correto, sem o uso de new.

Interface PaymentProcessor
java
Copiar
Editar
package com.example.spring.service;

public interface PaymentProcessor {
    void process(double amount);
}
Implementações
Cartão de Crédito:

java
Copiar
Editar
package com.example.spring.service;

import org.springframework.stereotype.Component;
import org.springframework.beans.factory.annotation.Qualifier;

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
package com.example.spring.service;

import org.springframework.stereotype.Component;
import org.springframework.beans.factory.annotation.Qualifier;

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
package com.example.spring.service;

import org.springframework.stereotype.Component;
import org.springframework.beans.factory.annotation.Qualifier;

@Component
@Qualifier("paypal")
public class PayPalPaymentProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("[Processing payment with PayPal]");
        System.out.println("Payment value: $ " + amount);
    }
}
Classe PaymentService
java
Copiar
Editar
package com.example.spring.service;

import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;

@Service
public class PaymentService {

    @Autowired
    @Qualifier("pix") // Pode ser alterado para "credit" ou "paypal"
    private PaymentProcessor paymentProcessor;

    public void pay(double amount) {
        paymentProcessor.process(amount);
    }
}
Classe Principal Application
java
Copiar
Editar
package com.example.spring;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import com.example.spring.service.PaymentService;

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
Comparação entre as abordagens
Critério	Java Puro (Factory)	Spring (Injeção de Dependência)
Acoplamento	Médio – depende da Factory	Baixo – injeta diretamente
Flexibilidade	Média – exige alteração manual	Alta – basta mudar o @Qualifier
Testabilidade	Média – exige simulação manual	Alta – fácil mock de dependências
Complexidade Inicial	Baixa – código simples	Média – exige estrutura do Spring
Escalabilidade	Limitada para projetos grandes	Alta – ideal para sistemas enterprise
Conclusão
A injeção de dependência é essencial para construir sistemas mais modulares, testáveis e fáceis de manter.

A versão em Java puro é adequada para projetos menores e didáticos. No entanto, o uso do Spring se destaca em projetos maiores, oferecendo uma arquitetura mais robusta, flexível e escalável, com menos esforço manual para alternar comportamentos de execução.

