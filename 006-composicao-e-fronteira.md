# Design Patterns - estratégias comuns para resolver problemas recorrentes

# Patterns de Composicao e Fronteira

Este modulo agrupa patterns que compartilham uma mecanica importante: delegacao por composicao.

Todos mantem um contrato relativamente estavel enquanto deslocam, enriquecem, controlam ou adaptam comportamento.


```
classe {
    T delegate;

    public X doSomething() {
        /// talvez tenha comportamento antes
        var res =  delegate.doSomething();
        // talvez tenha comportamento depois
        return res;
    }
}
```

### Diretivas de boas práticas

- **honre o contrato**;
- **codificar para a abstracao**;
- deixa publico so o que precisa;

## Patterns centrais e suas intenções

- strategy: variar algoritmo;
- adapter: tornar compativel o que não era;
- decorator: adicionar comportamento mantendo a interface;
- facade: simplificar acesso a subsistema mais complexo;
- proxy: controlar acesso, cache, lazy load ou fronteira remota;

## Mecanica comum

- o objeto principal guarda referencia para outro;
- o cliente continua falando com uma interface relativamente estavel;
- o comportamento muda sem exigir mudanca simetrica no cliente.

### Strategy

cara do problema que o strategy resolve
```java
methodo () {
    if () {
        muitas linhas
    }
    else if () {
        muitas linhas
    }
    else {
        muitas linhas
    }
}
```


calculo de imposto na das Custas
```java
calcularImpostoDasCustasDeUmImovel () {
    // muito codigo
    if (/* caso do imposto para PR */) {
        muitas linhas
    }
    else if (/* caso do imposto para SP */) {
        muitas linhas
    }
    else if (/* caso do imposto para RN */) {
        muitas linhas
    }
    else {
        /* caso geral para demais federacoes */
        muitas linhas
    }
    x....
    muito codigo
}
```

```java

interface CalculadoraDeImpostosDaFederacao {
    X calculoDeImposto();
}

public class CalculadoraDeImpostosDaFederacaoPR implements CalculadoraDeImpostosDaFederacao {
    public X calculoDeImposto() {
        muitas linhas do PR
    }
}

public class CalculadoraDeImpostosDaFederacaoRN implements CalculadoraDeImpostosDaFederacao {
    public X calculoDeImposto() {
        muitas linhas do RN
    }
}

public class CalculadoraDeImpostosDaFederacaoSP implements CalculadoraDeImpostosDaFederacao {
    public X calculoDeImposto() {
        muitas linhas do SP
    }
}

public class CalculadoraDeImpostosDaFederacaoDefault implements CalculadoraDeImpostosDaFederacao {
    public X calculoDeImposto() {
        muitas linhas do caso geral de demais federacoes
    }
}

public class CalculadoraDeImpostosDeIImovel {

    // tambem da pra fazer via injecao de dependencia sem a factory
    CalculadoraDeImpostos delegate;

    void calcularImpostoDasCustasDeUmImovel (Imovel imovel) {
        // muito codigo
        // usando factory
        var delegate = CalculadoraDeImpostosDaFederacao.ByUF(imovel.endereco.estado);
        var x = delegate.calculoDeImposto();
        x....
        muito codigo
    }

}
```

### Adapter

enquanto o strategy é defintivo, o adapter é temporario

```java

interface Login {
    User login(username, password);
}

class loginV1ComUserPassword implements Login {
    User login(username, password) {};
}

class loginV2ComJwt {
    User login(String jwt) {
        ///
        return user;
    };
}

class LoginAdapterV2ParaV1 implements Login {
    loginV2ComJwt delegate;

    User login(username, password) {
        //var jwt = sso.login(username, password)
        //var jwt = RequestAuthenticationHolder.getAuthenticationHeader()
        return delegate.login(jwt);
    };
}


// usage
// User x = l.login(username, password);
```



```
GeradorRelatoriosXML {
    Report compile(Record.. records){}
}

GeradorRelatoriosPosicinais {
    Report compile(Record.. records){}
}

GeradorAdapter {
    GeradorRelatoriosXML delegate;
    Report compile(Record.. records) {
        report = delegate.compile()
        //
        return transformer(xml);
    }
}
```


### Decorator



``` possivelmente um strategy
classe {
    T delegate;

    public X doSomething() {
        /// codgo aqui
        var res =  delegate.doSomething();
        // codigo aqui
        return res;
    }
}
```

``` possivelmente um decorator
classe {
    T delegate;

    public X doSomething() {
        /// o codgo esta aqui
        var res =  delegate.doSomething();
        // ou o codigo esta aqui
        return res;
    }
}
```

exemplo do uso do decorator pra adiconar formatacao
```html
<b>
    <i>
        <p>batatinha</p>
    </i>
<b/>
```

```
var x = calculoGeralDaFatura()
// if cliente.PossuifreteGratis
var x = cliente.PossuifreteGratis ? x : aplicaCobrancaDeFrete(x);
// if cliente.pagaComissao
var x = cliente.pagaComissao ? x : aplicaComissao(x);
// if possueVoucherDeDesconto
var x = voucher ? aplicaDesconto(x, voucher) : x;

if () {
    if () {

    }
    else {

    }
}
else {
    if () {

    }
    else {

    }
}
```


var (T) calculadoraFatura = new CalculadoraDeFatura();
var (T) AdicionaFrenteDecorator = new AdicionaFrenteDecorator(calculadoraFatura);
var (T) AdicionaComissaoDecorator = new AdicionaComissaoDecorator(AdicionaFrenteDecorator);
var (T) AdicionaVoucherDecorator = new AdicionaVoucherDecorator(AdicionaComissaoDecorator);


calculadoraFatura || 
    AdicionaFrenteDecorator(calculadoraFatura) || 
    AdicionaComissaoDecorator(AdicionaFrenteDecorator(calculadoraFatura)) ||
    
interface CalculoDeFatura {
    X calcular();
}


AdicionaFrenteDecorator implements CalculoDeFatura {
    CalculoDeFatura delegate;

    X calcular() {
        var x = delegate.calcular();
        aplicaCobrancaDeFrete(x);
        return x;
    }
}



/// var calculadoraFatura = CalculadoraFaturaFactory.byCliente(cliente);
// var fatura = calculadoraFatura.calcular(pedido)

### Proxy

ORM

class User {
    String userName;
    String userRole;

    @Eager
    List<Permission> permissons; // proxy
}

var user = repostory.findByd(userId);
// user.permissons <--- assim que o usuario é carregado, esse objeto nao é uma lsita de permissions >
// lazy loading






### Facade (fato historico)

muito pouco usado hoje em dia;

// delimitar os boundaries do user flow

module
    facade (entry points) // god class
        service-login
        service-signup
        service-profile
        service-checkout

```
class Facade {
    private LoginService loginService;
    private SignupService signupService;
    private ProfileService profileService;

    public User Login (username, password) {
        return loginService.login(username, password);
    }
}
```

UserController (limite user flow)
    service
        repository

OrderController
    service
        repository

///
module
    service (deixe publico apenas o necessario)
    controller (deixe publico apenas o necessario)