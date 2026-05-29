# Modulo 7: Patterns de Fluxo e Extensao

## Pergunta central

Como organizar etapas, comandos e extensoes de comportamento sem deixar o fluxo invisivel ou burocratico demais?

## Papel deste modulo no curso

Este modulo trata patterns que encapsulam operacoes, etapas e variacoes de fluxo. Eles sao especialmente uteis quando o sistema precisa compor regras, criar pipelines, separar responsabilidades de execucao ou permitir extensao por pontos bem definidos.

## Patterns centrais

- command - objeto vira comando (via RPC, cabos, bibliotecas);
- chain of responsibility - stack de middleware pra serem resolvidas; ipfilter;
- iterator - for each - stream.map;
- generator - gerar um stream ou lista a partir de um gerador de itens;
- template method - irmão feio do strategy, focado em herança;

## Intuicao do modulo

Esses patterns costumam aparecer quando o problema deixa de ser apenas "quem colabora com quem" e passa a ser "como o fluxo acontece".

Perguntas tipicas:

- quero transformar uma acao em objeto?
- quero encadear regras ou handlers?
- quero padronizar um algoritmo com pontos de variacao?
- quero separar traversal de estrutura?
- quero adicionar operacoes sem mexer continuamente na estrutura?

## Padões

### Command

```
// endereco e path
    POST /api/v1/usuarios HTTP/1.1
    Host: api.exemplo.com.br

// configuracao de execucao

    Content-Type: application/json
    Content-Length: 60
    User-Agent: Mozilla/5.0
    Authorization: Bearer abc123xyz

    {
    "nome": "Diego",
    "email": "diego@email.com",
    "idade": 30
    }
```

Transformar uma acao em objeto para solicitar que outra parte do codigo, outra biblioteca, software ou servidor execute a operacao.

// rabbitmq, jms, amqp, kafka
// mensagem
{
    type: 'save-user`
    payload: {...}
}

#### Mensageria

- processamento sincrono / sequencial
    request -> google -> select no db -> leitura de disco

// leitura sequencial é sensivel a quebra em qualquer das etapas

- processamento assincrono / concorrente
    request -> ?
    push notification / ticket / future / promise

// gerencia melhor os recursos porque delega decisao de onde executar para quem esta realizando a operacao
// se uma das partes quebrar, voce pode dar replay na operacao

request -> google
    msg -> listener de requests -> select no db -> leitura de disco
    msg -> listener de requests -> select no db -> leitura de disco
response <-

// gerar disponibilidade e tolerancia a falhas

#### arquitetura de software orientada a eventos

[
    {
        action: 'deposit`
        payload: {...}
    }
    {
        action: 'withdraw`
        payload: {...}
    }
    {
        action: 'withdraw`
        payload: {...}
    }
    {
        action: 'withdraw` // esse cara deu erro
        payload: { amount : -1000, uuid }
    }
    {
        action: 'deposit` // esse cara é a compensacao / estorno
        payload: { amount : +1000, uuid }
    }
]

#### Scanners e impressoras

// print
// parametros

lib (supplier)
    scanner

// serio problema de acoplamento semantico
quem escreve tem que escrever no formato de quem le;
A -- contrato 1.0.0 ---> B
A -- contrato 1.0.1 ---> B


websocket (request de abertura de canal)
```
// endereco e path
    POST /api/v1/usuarios HTTP/1.1
    Host: api.exemplo.com.br

// configuracao de execucao

    Content-Type: application/json
    Content-Length: 60
    User-Agent: Mozilla/5.0
    Authorization: Bearer abc123xyz

    { autenticacao }
```

switching:
[
    {action, payload},
    {action, payload},
    {action, payload},
    {action, payload},
    {action, payload},
]

#### TODO topicos, filas, e roteamento (exchanges)

topico -> entrega a mesma mensagem pra todo mundo que pedir
fila -> garante que apenas uma aplicacao receba uma dada mensagem

- topico (recebe as transferencias entre contas )
    - fila processamento
    - fila de logs
    - fila de auditoria

#### SAGA

### Chain of responsibility - stack de middleware pra serem resolvidas; ipfilter;

CoR

Lista de comportamentos, que voce organiza e apenas um objeto responde a execucao;


```js
(ctx) -> {
    if (/*match criteria*/) {
        return X;
    }
    next(ctx);
}
```


request

```
192.* --> redirect
10.* -> dropo
127.* -> dropo
allow all *
```

```
var app = new express()

app.use(())

app.use((request, response, next) => {
    if (request.headers["Authorization"] !== null)
        next()
    else
        response.send(401);
})
app.use((request, response, next) -> response.send(404));
```

```
CalculaImposto(papel);
    use(CalculaImpostoTituloPublico)
    use(CalculaImpostoAcaoRV)
    use(CalculaImpostoDebenture)
    use(CalculaImpostoGenerico) // terminador
``` 

```
catch(RuntimeExeception ex){

}
catch(Exeception ex){

}
```


### iterator - for each - stream.map; (historico)

foreach
antes do foreach // stream.map
for (i = 0; i < x; i++) {
    // do something
}

item, item, item, item, item
while (item.hasNext()) {
    //do something
}

### generator - gerar um stream ou lista a partir de um gerador de itens;


- objeto/funcao que gera valores;

- gerador de numeros aleatorios;
    - seed(1000);
        1001;
        1002;
        1003;
        1004;

    - seed(1000);
        1001;
        1002;
        1003;
        1004;


yeld;

```
    var users = db.listusers();
    while (var user = users.next()) {
        // do something
    }
```

```
var x = [x^2 of x for i in [1....1000000000]]
```

> backpressure