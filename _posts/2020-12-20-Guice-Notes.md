---
layout: post
title: Learning Google Guice 
date: 2020-12-20
categories: Learning
---

### Types of injections
Tagging a constuctor with @Inject is essentially telling Guice where you want a dependency to be provided for you. 
You can also apply it for methods and fields. 
Which one you choose solely depends on class's requirements.

Constuctor injection provides immutability. That also means thread safety. Only one @Inejct is allowed in this case. 

### Other cases during injection
Guice works in case of inheritance as well. If you subclass a class that features @Inject annotations, injection works as expected. 
First the superclass gets injected and the subclass.

Guice can inject into static members as well. 

Guice also doesn't care about targets visiblity. It will inject anything tagged with @Inject regardless of whether is public, private or protected. 
However, injection into private members is usually not needed and probably a bad idea. This leads to crippling the classes testability.
Field injections are frequent offenders in this case since they are usually private members in a class. 


### Specifying an implementation
You can implement `Module` or extend `AbstractModule` to tell Guice which implementations to use for the dependencies of the class.
`AbstractModule` is an abstract class (duh!) that implements `Module` itself and exposes a no-argument configure method.


### Example

```java

// BillingService.java

public class BillingService {
    private final CreditCardProcessor processor;
    private final TransactionLog transactionLog;

    @Inject
    public BillingService(CreditCardProcessor processor, TransactionLog transactionLog) {
        this.processor = processor;
        this.transactionLog = transactionLog;   
    }

    public String chargeOrder(Order order, CreditCard creditCard){
        try {
            ChargeResult result = processor.charge(creditCard, order.getAmount());
            transactionLog.logChargeResult(result);
            return "Order Charged!";
        } catch (Exception e) {
            transactionLog.logException(e);
            return "Order declined";
        }
    }
}


// BillingModule.java
public class BillingModule extends AbstractModule {
    protected void configure() {
        bind(TransactionLog.class).to(DatabaseTransactionLogImpl.class);
        bind(CreditCardProcessor.class).to(PaypalCreditCardProcessorImpl.class)
    }
}  

```
