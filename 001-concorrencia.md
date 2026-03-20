# Concorrência e paralelismo

- O mundo é paralelo e concorrente

```
1 salvaCadastroDoUsuario(); <future nao é serial>
2 criaOPerfilDoUsuarioQueFoiSalvo();
```

```
1 salvaCadastroDoUsuario()
    .entaoFaca( () -> criaOPerfilDoUsuarioQueFoiSalvo());
```

```
faca1()
    .depoisFaca2()
    .depoisFaca3()
    .SeDerPauDesfacaTudo();


Stream().map( (parametro) -> fazAlgoComOParametro()).collect();
```

```
FacaEsseMonteDeCoisas(
    faca1(),
    Faca2(),
    Faca3(),
)
.depoisFaca4()
.SeDerPauDesfacaTudo();

```


Técnicas controle de concorrencia:

- Lock (semaforo); Volatile;
    - evitar conflito;

- barreira; CountDownLatch, PromiseAll;

- sinais, mensagens;

Estratégias pra evitar problemas na Escrita da área compartilhada;

Objetos imutáveis não são afetados pela escrita paralela;

Problemas de concorrência:
- Dead lock (lock)
- Starvation (lock)
- Race condition


