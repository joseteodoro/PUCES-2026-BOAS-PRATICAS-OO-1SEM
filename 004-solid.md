# SOLID

## Nossas diretivas mais simples

* deixa publico so o que precisa;
** honre o contrato;
*** criar blocos menores de código, mais enxutos;
**** isolar o muda;

## Pilares da orientação a objetos

- Encapsulamento (esconde detalhe);
- Contrato = metodo(tipo); (na literatura chamam de herança)
- abstracao = tipos compativeis, porem diferentes
- polimorfismo = diferentes comportamentos que tem contrato compativel

## Princípios de desenvolvimento

KISS => keep it simple
DRY => dont repeat yourself
YAGNI => you arent gonna need it (evita overengineering)
Isole o que mude;

## SOLID (manutencao sem quebrar nada)

- SRP - Single Responsability Principle (um artefato tem apenas um motivo para mudar)
    - uma manutencao nao deve gerar dor de cabeça ou quebrar qualquer coisa no caminho;
    - fazendo blocos menores;

- OCP - Open Closed Principle  (aberto pra adaptação, mas fechado pra mudança interna)
    - adicionar nova funcionalidade sem ter que adicionar ifs;
    - programar nao deve ser dificil e nao deve quebrar outras coisas no caminho;

```java
class ReportExporter {
    public Report export(List<Records> items, ReportType type) {
        if (type == ReportType.PDF) {
            // pdf
            return pdf;
        else if (type == ReportType.CSV){
            // csv
            return csv;
        }
    }
}


```

```java

interface ReportExporter {
    Report export(List<Records> items);
}

class PDFExporter implements ReportExporter {
    public Report export(List<Records> items) {
        // pdf
        return pdf;
    }
}

class CSVExporter implements ReportExporter {
    public Report export(List<Records> items) {
        // CSV
        return CSV;
    }
}

class ExcelExporter implements ReportExporter {
    public Report export(List<Records> items) {
        // Excel
        return Excel;
    }
}

class BatatinhaExporter implements ReportExporter {
    public Report export(List<Records> items) {
        // Batatinha
        return Batatinha;
    }
}

// design pattern (factory)
class ReportExporterBuilder {
    public ReportExporter build(ReportType type) {
        if (type == ReportType.PDF) {
            return new PDFExporter();
        }
        else if (type == ReportType.CSV){
            return new CSVExporter();
        }
        else {
            return new ExcelExporter();
        }
    }
}

/// var exporter = ReportExporterBuilder.build(type)
/// var report = exporter.export(items);
```

- LSP - Liskov Substitution Principle (LISKOV - tipagem que força obedecer o contrato)
    - honre o contrato com os amiguinhos;
    - não surpreenda com uma excecao se seu contrato nao preve uma excecao;

// nao vai acontecer
```java
interface ReportExporter {
    Report export(List<Records> items);
}

// compiler nao deixa porque voce nao esta honrando o tipo
class PDFExporter implements ReportExporter {
    public Report export(List<Records> items) throws RuntimeException {
        // pdf
        if (x) {
            throw new RuntimeException();
        }
        //
        return pdf;
    }
}
```

// pode acontecer
```java
interface ReportExporter {
    Report export(List<Records> items) throws RuntimeException;
}

// compiler deixa porque voce nao esta honrando o tipo
class PDFExporter implements ReportExporter {
    public Report export(List<Records> items) {
        // pdf
        return pdf;
    }
}
```

- ISP - Interface Segregration Principle (contratos pequenos, enxutos, justos)
    - honre o contrato com os amiguinhos;
    - criar blocos menores, potencializar reuso;

```java
// exemplo ruim
public class MouseEventDemo implements MouseListener {

    public void mousePressed(MouseEvent e) {
       // not implemented
    }

    public void mouseReleased(MouseEvent e) {
       // not implemented
    }

    public void mouseEntered(MouseEvent e) {
       // not implemented
    }

    public void mouseExited(MouseEvent e) {
       // not implemented
    }

    public void mouseClicked(MouseEvent e) {
       // do something
    }
}

// exemplo um pouco melhor
interface MouseClickListener {
    public void mouseClicked(MouseEvent e);
}

interface MouseHoverListener {
    public void mouseEntered(MouseEvent e);
    public void mouseExited(MouseEvent e);
}

interface MouseListenerDragAndDropgListener extends MouseClickListener, MouseHoverListener;

```

- DIP - Dependency Inversion Principle (inversao de dependencias, partes do seu comportamento obedecem contratos que sao configurados fora do seu código)
    - composicao guiada por contrato;
    - confie no contrato dos amiginhos;

```java
// springboot
//.net core
// nest.js
@lombok
public class UserCreator {

    UserRepository repository;
    ProfileCreator profileCreator;
    
    public User para("jose teodoro", "jteodoro.dev@gmail.com", "guest") {
        repository.salvar(nome, email, role)
        profileCreator.setup(email)
    }

}

// var repository = new UserRepository();
// var profileCreator = new ProfileCreator();
// new UserCreator(repository, profileCreator);



```