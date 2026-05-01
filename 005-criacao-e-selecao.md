# Design Patterns - estratégias comuns para resolver problemas recorrentes

## Criacao e Selecaos - Esconder complexidade e selecionar implementação polimórfica

Os patterns deste bloco tentam responder perguntas como:

### Intuição deste modulo:

- quem decide qual tipo concreto sera usado?
- onde a logica de criacao deve ficar?
- como evitar if e switch por tipo no cliente?
- quando faz sentido ter uma unica instancia?

### Ganhos esperados

- reducao de acoplamento com implementacao concreta;
- centralizacao de logica de criacao;
- mais extensibilidade por contrato;
- melhor organizacao de dependencias;
- possibilidade de isolar configuracao complexa.

### Singleton  - reuso de instancias em tempo de execucao

Singletons para reusar uma mesma instancia em todo e qualquer fluxo de execucao da aplicacao, reusando as instancias.

ConnectionPool().getConnection()

Invocamos a mesma instancia nao importa onde estamos chamando ela no código (reuso da instancia)

```
controller() <-- `RequestContextHolder`>
    service()
        bibliotecaCWSMainframe() `RequestContextHolder.getCurrentAuthorization()`
        apiParceira(Authorization) `RequestContextHolder.getCurrentAuthorization()`
```


o que esta definido nos livros e que nao funciona no multi thread
```java
public class SingletonRequestContextHolder {

    private static SingletonRequestContextHolder instance; <-- sema>

    public static SingletonRequestContextHolder getInstance() {
        if (instance == null) { [1 e 2]
            instance = new SingletonRequestContextHolder(); [1 e 2 ]
        }
        return instance;
    }

    private SingletonRequestContextHolder() {}


    private Map<String, User> users;

    public User getCurrentUser(String id) {}

}
```

multi thread com semaforo, on demand
```java
public class SingletonRequestContextHolder {

    private static SingletonRequestContextHolder instance = null;

    public static synchronized SingletonRequestContextHolder getInstance() { <-- tempo amortizado --> <-- semaforo -->
        if (instance == null) { [1 e 2]
            instance = new SingletonRequestContextHolder(); [1 ou 2, um deles vai esperar]
        }
        return instance;
    }

    private SingletonRequestContextHolder() {}


    private Map<String, User> users;

    public User getCurrentUser(String id) {}

}
```

multi thread com semaforo, eager
```java
public class SingletonAIModelEager { <-- 1 GB do disco pra RAM>

    private static SingletonAIModelEager instance = null;

    static { <-- safe thread by design -->
        instance = new SingletonAIModelEager(); <-- executa no startup da aplicacao>
    }

    public static SingletonAIModelEager getInstance() {
        return instance;
    }

    private SingletonAIModelEager() {}

    private GPTModel model;

    public Stream<String> promt(String prompt) {
        this.model.prompt(prompt);
    }

}
```

```
multi thread: {
    SingletonRequestContextHolder.getInstance() <-- SingletonRequestContextHolder() <-- mesma lista >
    SingletonRequestContextHolder.getInstance() <-- SingletonRequestContextHolder() <-- mesma lista >
    SingletonRequestContextHolder.getInstance() <-- SingletonRequestContextHolder() <-- mesma lista >
    SingletonRequestContextHolder.getInstance() <-- SingletonRequestContextHolder() <-- mesma lista >
    SingletonRequestContextHolder.getInstance() <-- SingletonRequestContextHolder() <-- mesma lista >
}
```

### factory method - fábrica - passar um parametro que me dá uma impelentacao especifica
    geralmente esta junto com esses padres:  <strategy, template method>

- facilitar o uso pelos amiguinhos, mediante um parametro de escolha seleciona uma implementacao especifica

```
User user = new UserFactory().GetByID(id); <-- guest, paidUser, admin -->
user.login()
user.setupProfile()
```

```java
//tipos
public enum UserType {
    ADMIN, PAID_USER, PREMIUM, GUEST;
}
//contrato
public interface User {
    public void login();
    public void setupProfile();
}
//implementacao (segue o Open/closed do solid)
public class GuestUser implements User {}
//implementacao (segue o Open/closed do solid)
public class PaidUser implements User {}
//implementacao (segue o Open/closed do solid)
public class AdminUser implements User {}
//implementacao (segue o Open/closed do solid)
public class PremiumUser implements User {}

// factory que esconde a complexidade da decisao
public class UserFactory {  // (viola o Open/closed toda vez que eu tenho uma classe nova)
    public User GetByID(String id) {
        var loadedUser = DB.loadUser(id);
        var type = loadedUser.getType();
        if (ADMIN.equals(type)) {
            return new AdminUser();
        }
        else if (PAID_USER.equals(type)) {
            return new PaidUser();
        }
        else if (PAID_USER.equals(type)) {
            return new PremiumUser();
        }
        else {
            return new GuestUser();
        }
    }
}
```

```java
@Configuration
public RESTConfiguration { <-- factory -->
    @Bean("requestTemplateMTLS")
    public RequestTemplate setupMTLSRequestTemplate() {
        // MTLS
        var certificate = Certificate.loadFromDisk();
        return new RequestTemplate(certificate);
    }

    @Bean()
    public RequestTemplate setupDefaultRequestTemplate() {
        return new RequestTemplate();
    }
}

// usage
@Autowired <-- default -->
RequestTemplate requestTemplate;

@Autowired("requestTemplateMTLS") <-- default -->
RequestTemplate requestTemplateMTLS;

```

### builder - facilitar o uso pelos amiguinhos


- facilitar o uso pelos amiguinhos, esconde complexidade de criacao

```java
//lombok
@Builder
public class User {
    private String name;
    private String email;
    private String role;

    public User(String name, String email, String role) {}

}

public class UserBuilder {
    private String name;
    private String email;
    private String role;

    public UserBuilder setName(String name) {
        this.name = name;
        return this;
    }

    //...
    public User build() {
        return new User(name, email, role);
    }
}

// usage
var user = userbuilder
    .withName("batatinha")
    .withEmail("batatinha@gmail.com")
    .withRole("admin")
    .build();

```

Exemplo de impressao automatizada de matriculas de imoveis em paginas legais:
---
// papel impresso (matricula do seu imovel)
[
    -----
    -----
    -----
    signature
    -----
    -----
    -----
    signature
    (vazio / em branco)
]
---
[

]

PaginaEmbrancoBuilder
    .setusuarioQuerNovaPagina(usuarioQuerNovaPagina) //
    .setUltimaPaginaDaMatricula(ultimaPaginaDaMatricula) //
    .setPosicaoEmCentimetros(xcentrimetros) //
    .setPosicaoDaReguaImpressa(xBarras) //
    .build(); <-- PaginaEmBrancoDaMatricula -->

// metodo .build() decide como construir o objeto baseado nos inputs

### abstract factory;

Familias de objetos, significa que sao compativeis (existe o polimorfismo, mas as classes concretas precisam ser compativeis)

JDBC < implementacoes de bancos de dados >
    interfaces - query, sql, sqlexception, sqltransaction

    nao posso:
        query (oracle), sql (postgres), sqlexception, sqltransaction (postgres)

    posso:
        query (postgres), sql (postgres), sqlexception(postgres), sqltransaction (postgres)
        query (oracle), sql (oracle), sqlexception(oracle), sqltransaction (oracle)

query.execute(); <-- oracle -->
query.execute(); <-- postgres -->

OracleAbstractFactory.newTransaction() <-- transaction || oracle>
OracleAbstractFactory.newQuery() <-- Exception || oracle>

