# Evolucao ate o O.O

* deixa publico so o que precisa;
** honre o contrato;
*** criar blocos menores de código, mais enxutos;

## Estruturado

Comportamento é separado do estado;

```java
    Repositorio.save(connection, usuario)
```

Records, funcao estática você está codificando estruturado;

```C
function A (compativel com User?)
function B (compativel com User?)

===
// record é compativel com as funcoes (respeitar o contrato)
// esconder detalhes da implementacao
Record Repository {
    *Record Connection;
    *Function save;

    public save(usuario) {
        &save(&connection, usuario);)
    }
}

// setup do repository
Repository(usuario)
```


```java
public class Repository (Tipo) {
    atributos estaticos;
    comportamentos estaticos;

    atributos/campos/estado;
    comportamento;
}
```

```java

// estruturado
Repository.save(connection, usuario);

// tipagem
var repository = new Repository(connection);
repository.save(usuario);


tipagem + estatico


```

Programação com Estado + Comportamento juntos

Tipo = conjunto de contratos agrupados num modelo, estado;

### Pilares da orientação a objetos
- Encapsulamento (esconde detalhe);
- Contrato = metodo(tipo); (na literatura chamam de herança)
- abstracao = tipos compativeis, porem diferentes
- polimorfismo = diferentes comportamentos que tem contrato compativel

###

* deixa publico so o que precisa;
** honre o contrato;
*** criar blocos menores de código, mais enxutos (reuso);

**** codificar para a abstracao

```java
storage.save(usuario); // complexidade

BancoDeDados.[save(usuario)]; // comportamento de banco de dados
Api.[save(usuario)]; // comportamento de api

xxxx.save(usario);

var x = BancoDeDados || Api
x.save(usuario) // porque eles sao compativeis, porque codificou para a abstracao
```
s
