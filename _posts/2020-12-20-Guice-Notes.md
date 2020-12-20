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

### Bootstrapping Guice
To start using Guice you create an instance of `Injector`. This central Guice type takes a collected set of Module or AbstractModule subclasses.
To create an injector use `createInjector` which is a simple static class that serves as a starting point. You can pass zero or more modules separated 
by a comma.

```java
//Main.java
public static void main(String[] args) {
    Injector injector = Guice.createInjector(new BillingModule());
    BillingService billingService = injector.getInstance(BillingService.class);
    ...
    billingService.chargeOrder(abc, xyz)
    ...
}
```

The recursive behaviour of guice allow you to use the injector somewhere high up in the stack, guice then creates an entire graph of dependencies below a requested
object recursively.
`createInjector` method also has an onverload that takes stage enumeration as the first parameter. Use `DEVELOPMENT` to have better error reporting, loggging, faster startup,
at the cost of slow runtime and `PRODUCTION` for production code. 

```java
Injectior injector = Guice.createInjector(Stage.PRODUCTION, new BillingModule());
```

### Choosing between Implementations
...





