## Intervalos

19:00 - 20:25
20:35 - 22:00

### estratégias que funcionam em algum contexto ---> boas práticas de codificação ---> Pilares da orientação a objetos


# Boas Praticas

## limites, boundary, fronteira, estado interno / externo, esconder complexidade, facilitar uso.

- todos os metodos publicos / urls da api que voce disponibiliza;
- reduzir o que fica exposto;
- boiler plate (codigo repetido varias vezes só de infraestrutura ou setup);

COE <--- lib expunha alguns detalhes internos (release 1 / mes)
Squad A <----- release 2 semanas

<version>1.1.1</version> --> <version>1.2.1</version> // exemplo ruim

* Na duvida, deixa privado; So fica publico se realmente precisar;

@Preterido
@Depreciado
@Deprecated

- limites (fronteira), pra preserva minha liberdade de mudar;
- limites, preserva o comportamento esperado de quem codificou o artefato;
- estado interno vs estado externo
- facilita o uso (reduzir a complexidade de uso)
- extensoes,

```java

var Usuario = CriadorDeUsuarios.para("jose teodoro", "jteodoro.dev@gmail.com", "guest");
    // var sql = "insert into user (nome, email, senha) values ('jose teodoro', 'jteodoro.dev@gmail.com', 'guest')";
    // bancoDeDados.insert(sql)
    // mkdir("/users/jteodoro")
    // copy("profiletemplates", /profiles/jteodoro)

```

** vamos honrar os contratos (não surpreender os amiguinhos)
    - reduzir tudo que é publico, ajuda a honrar o contrato

contrato: nome de metodo + parametros;
contrato exposto chamada: tudo que voce deixar publico;

```java
    var Usuario = CriadorDeUsuarios.CriaUsuarioEProfile("jose teodoro", "jteodoro.dev@gmail.com", "guest");
    // var sql = "insert into user (nome, email, senha) values ('jose teodoro', 'jteodoro.dev@gmail.com', 'guest')";
    // bancoDeDados.insert(sql)
    mkdir("/users/jteodoro")
    copy("profiletemplates", /profiles/jteodoro)
```

Versionamento Semantico: bibliotecas OSS
<version>1.1.1</version> --> <version>1.2.1</version> // exemplo ruim
<version>1.1.1</version> --> <version>2.0.0</version> // exemplo correto

major quebra contrato;
minor adiciona funcionalidade, mas nao quebra contrato;
fix só corrige problema, nao adiciona funcionalidade nem quebra contrato;

<version>major.minor.fix</version>
10.1.10



```javascript

//v1
function Batatinha(id, name) {

}

//v2
function Batatinha(id, name, dob = null) {

}
batatinha(id, nome)

```

```java
// v2
// quando vou tirar e uma alternativa para o chamador usar
// vou tirar isso na v3, alternativa use Save(user, transaction);
@Deprecated
public void Save(user) {
    Save(user, valorPadraoQueNaoQuebraOComportamento);
};

//v2
public void Save(user, transaction);

```

## difícil usar artefatos com muitos métodos públicos, fácil usar artefatos com poucos métodos públicos. esconde detalhes, facilita uso, esconde complexidade, reduz contrato exposto

## reuso, extrair funções, reusar comportaments, artefatos  gigantes, artefatos bem coesos bem enxutos bem claros, que fazem poucas coisas, que delegam comportamento pra outros artefatos.

```java

Usuario.salvar(nome, email, role) {
// var sql = "insert into user (nome, email, senha) values ('jose teodoro', 'jteodoro.dev@gmail.com', 'guest')";
    // bancoDeDados.insert(sql)
}

Profile.setup(email) {
// mkdir("/users/jteodoro")
    // copy("profiletemplates", /profiles/jteodoro)
}

var Usuario = CriadorDeUsuarios.para("jose teodoro", "jteodoro.dev@gmail.com", "guest");
    // Usuario.salvar(nome, email, role)
    // profile.setup(email)

// API
    // Usuario.salvar(nome, email, role)

// desktop
    // Usuario.salvar(nome, email, role)
    // profile.setup(email)
```

- delegar detalhes para outros artefatos

* deixa publico so o que precisa;
** honre o contrato;
*** criar blocos menores de código, mais enxutos;