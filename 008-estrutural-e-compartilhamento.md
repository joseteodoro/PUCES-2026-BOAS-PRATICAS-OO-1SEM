# Modulo 6: Patterns de Estrutura e Compartilhamento

## Pergunta central

Como modelar hierarquias e reaproveitar estrutura ou estado sem esconder custo, recursao ou vazamento de contexto?

## Papel deste modulo no curso

Este modulo trabalha duas dores diferentes, mas relacionadas:

- representar estruturas compostas de forma uniforme;
- economizar ou compartilhar estrutura e estado com cuidado.

Aqui a turma precisa perceber que elegancia estrutural pode vir acompanhada de riscos sutis, principalmente quando o reuso de estado passa a ser agressivo demais.

## Patterns centrais

- prototype; // reuso de memoria
    - copy on construtor
- flyweight; // reuso de memoria
- object pool; // reuso de recurso

================

- composite;

## Intuicao do modulo

### Composite

- ajuda quando partes e todo precisam ser tratados pela mesma interface;
- simplifica arvores e hierarquias;
- pode esconder custo recursivo ou diferencas semanticas importantes.

### Flyweight

- ajuda quando ha muitas instancias muito parecidas;
- economiza memoria ao separar estado intrinseco de extrinseco;
- exige disciplina forte para nao misturar contexto compartilhavel com contexto exclusivo.

### Prototype

- permite criar novos objetos por copia;
- parece simples, mas copia rasa pode compartilhar mais do que deveria.

### Object pool

- centraliza controle de recurso caro;
- pode virar gargalo, disputa e fonte de leak se acquire e release forem mal tratados.

## Padrões

### Prototype

```java
var x = object.clone();
var x = {...obj}


var safeList = new LinkedList(existentList); // nao é threadsafe, sem tem escrita compartilhada
safelist.Stream().forEach((us) -> {
    var copy = us.clone();
    // do something with us
})

class User {
    String username;
    String password;
    String role;

    // prototype
    public User clone() {
        return new User(username, password, role);
    }

    public User(String username, String password, String role) {}

    // copy on constructor   
    public User(User user) {
        this.username = user.username;
        this.password = user.password;
        this.role = user.role;
    }
}


// clone - shallow copy / copia rasa
class User {
    String username;
    String password;
    String role;

    List<Login> logins; // nao me importo de reusar essa lista

    // prototype
    public User clone() {
        return new User(username, password, role, logins);
    }
}

// clone - deep copy
class User {
    String username;
    String password;
    String role;

    List<Login> logins; // me importo de usar essa lista

    // prototype
    public User clone() {
        return new User(username, password, role, new LinkedList(logins));
    }
}

// shadow dll / lib
.exe
    cp db.dll -> tmp/
    cp crypt.dll -> tmp/
    exec tmp/db.dll
    exec crypt/db.dll
```

### Flyweight, object register

compartilhar objetos pesados

```java
// arquivo do modelo de IA 1GB

Singleton{} // deixar o recurso num singleton
Flyweight{} // compartilhar objetos internos

// problemas de escrita na area compartilhada

// potencialmente ocupa muita memoria ram
class ObjectRegisterSingleton {

    WeakMap<Class, Object> registered;

    public static ObjectRegisterSingleton getInstance() {
        if (instance == null) {
            instance = new ObjectRegisterSingleton();
        }
        return instance;
    }

    public Object get(Class type, Loader<Class> loader) {
        if (registered.containsKey(type)) {
            return registered.get(type);
        }
        // time to live - tempo ate eu expurgar esse valor da memoria
        registered.put(type, loader.load(type));
        return registered.get(type);
    }

}

import java.util.LinkedHashMap;
import java.util.Map;

public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true);
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > this.capacity;
    }
}

```

### Object pool // compartilhando recursos limitados

pool de conexoes do banco de dados;

lista de recursos disponiveis (pool):
    - puxo um recurso da lista;
    - uso o recurso;
    - devolvo o recurso para a lista;

setup do pool, no inicio da aplicacao
    - ttl
    - min size = 5
    - max size = 20

setup pool postgres
    max size 100

HPA // autoscaling horizontal
    min 2;
    max 4; -> 20







