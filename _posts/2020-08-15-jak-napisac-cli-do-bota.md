---
author: Grizwold
title: HAS#6 Jak zaimplementować interfejs CLI dla swojej aplikacji
published: true
comments: true
tags: polish helbreath-automation-series
---

Dzisiaj trochę z innej beczki i troche mniej bot-owato. Mianowicie - jak zaimplementować CLI dla swojej aplikacji? Słowem 
(lub kilkoma) wstępu ogarnijmy czym jest CLI - Command Line Interfejs. Weźmy na przykład takiego Gita i obsługę tegoż z 
linii komend (bo o tym tu mówimy).

Gita możemy używać za pomocą aplikacji `git` i przykładowe wywołanie będzie wyglądało następująco `git help`
Wyświetli to skrócony tekst helpa gita. 

![](assets/2020-08-15-jak-napisac-cli-do-bota/git-help.png)

Mamy tu nazwę aplikacji (nazwa pliku binarnego) `git` jak i podkomendę `help` 
wyświetlającą tekst helpa. Ta podkomenda może przyjmować dodatkowe przełączniki jak na przykład `git help -a` który 
wyświetli pełną treść helpa ze wszystkimi podkomendami. 

Przełączniki mogą także przyjmować swoje parametry wejściowe jak
na przykład `git commit -m "my sweet commit message"` gdzie możemy zdefiniować opis swojego commita.

W naszym bocie także by się przydały takie podkomendy i parametry wejściowe. Weźmy chociażby bota do manufacturingu. 
Musimy mieć jak zdefiniować jaki itemek chcemy stworzyć i w jakiej ilości, nie? Powiedzmy, że chciałbym mieć taką
przykładową komendę, gdzie tak uruchomiony bot powinien nam stworzyć 10szt. Super Coal.

`helgame-bot manu --item "super coal" --amount 10`

Także w przyszłości będę chciał móc łatwo stworzyć wiele innych komend:

`helgame-bot fishing --amount 10`

`helgame-bot archer --weapon "composite bow"`

`helgame-bot rep --nick "Grizwold"`

`helgame-bot delivery --destination aresden 100 100 --item "big red potion" --amount 30`

`helgame-bot collect --item "slime jelly"`

itd...

Biblioteką Javową do takich cudów jest miedzy innymi `JCommander` i oryginalnie ją użyłem w konstruowaniu bota. Alternatywą 
do JCommandera jest `picocli` na którego planuję przejść bo ma [pewne](https://github.com/remkop/picocli/wiki/picocli-vs-JCommander) 
interesujące mnie plusy w porównaniu do jego konkurenta. Jednak w tej chwili opiszę jak w prosty sposób skonfigurowałem
CLI z użyciem JCommandera.

W aplikacjach Java parametry wejściowe aplikacji podaje się po podaniu ścieżki do pliku JAR jak w tym przypadku:

`java -jar helgame-bot.jar manu --item "super coal"`

Kiedy w taki sposób uruchomimy wykonywalny plik JAR to w metodzie `main` otrzymamy tablicę `args` z taką zawartością:

![](assets/2020-08-15-jak-napisac-cli-do-bota/parameters.png)

Jak widać każdy kolejny parametr oddzielony spacją ląduje w innym elemencie tablicy z wyjątkiem parametru podanego w 
cudzysłowie, gdzie spacja nie spowodowała rozdzielenia go na 2 parametry. Oczywiście możemy zinterpretować je ręcznie,
ale to głupie mająć wygodne gotowce na Githubie.

Co więcej, my nie chcemy gołej obsługi parametrów wejściowych a cały system [podkomend](https://jcommander.org/#_more_complex_syntaxes_commands) 
jak w gicie.

Wracając do przykładu komendy dla manufacturingu - będziemy potrzebować coś co nam przechowa możliwe wartości tej komendy:
"item name" i "amount".

```java
public class ManufacturingCommand {
    public String item;
    public int amount = -1;
}
```

Teraz potrzebujemy coś co zinterpretuje nasze parametry wejściowe. Zakładam że mamy już JCommandera w naszych zależnościach, 
więc zwyczajnie go użyję do wczytania parametrów.

```java
public class Starter {

    public ManufacturingCommand parse(String[] args) {
        ManufacturingCommand manufacturingCommand = new ManufacturingCommand();

        JCommander jCommander = JCommander.newBuilder()
                .programName("Helbreath automation toolset")
                .addCommand(manufacturingCommand)
                .build()
                .parse(args);

        System.out.println(jCommander.getParsedCommand()); //prints name of the parsed command

        return (ManufacturingCommand) jCommander.getCommands()
                .get(jCommander.getParsedCommand())
                .getObjects().stream()
                .findFirst()
                .orElseThrow(IllegalStateException::new);
    }
}
```

Teraz musimy połączyć zmienne z naszego obiektu komendy z parametrami wejściowymi, a w dodatku skonfigurować nazwę naszej
komendy, żeby została odpowiednio rozpoznana kiedy wołamy `helgame-bot manu --item "super coal"`. Żeby to zrobić edytujemy
klasę z parametrami:

```java
@Parameters(commandNames = {"manu"})
public class ManufacturingCommand {
    @Parameter(names = {"--item"}, required = true)
    public String item;
    @Parameter(names = {"--amount"})
    public int amount = -1;
}
```

Niestety jak widać na wcześniejszym przykładzie - kod obsługuje tylko jedną podkomendę, mimo że mieliśmy na celu tworzenie
wielu kolejnych. Na przykład takich:

```java
public class CommandParser {

    public Command parse(String[] args) {
        ManufacturingCommand manufacturingCommand = new ManufacturingCommand();
        StoreItemCommand storeItemCommand = new StoreItemCommand();
        DropCommand dropCommand = new DropCommand();
        FishingCommand fishingCommand = new FishingCommand();
        ArcherCommand archerCommand = new ArcherCommand();
        TesterCommand testerCommand = new TesterCommand();

        JCommander jCommander = JCommander.newBuilder()
                .programName("Helbreath automation toolset")
                .addCommand(manufacturingCommand)
                .addCommand(storeItemCommand)
                .addCommand(dropCommand)
                .addCommand(fishingCommand)
                .addCommand(archerCommand)
                .addCommand(testerCommand)
                .build()
                .parse(args);

        return (Command) jCommander.getCommands()
                .get(jCommander.getParsedCommand())
                .getObjects().stream()
                .findFirst()
                .orElseThrow(IllegalStateException::new);
    }
}
``` 

Wszystkie komendy teraz współdzielą ten sam interfejs, czyli mogą być efektywnie rzutowane na wspólny typ. Co więcej!
Nasze worki na zmienne parametrów teraz mogą być również traktowane jako wykonywalne komendy. Wszystko dzięki polimorfizmowi.

Zdefiniujmy ten interfejs jako:

```java
public interface Command {
    void execute();
}
```

Wtedy nasza przykładowa `ManufacturingCommand` może implementować podany interfejs wraz z metodą która ją uruchamia.

```java
@Override
public void execute() {
    Helbreath helbreath = new Helbreath();
    helbreath.manufacturing().manufacture(item, amount);
}
```

Finalnie nasz parser zamyka się w powyższej klasie `CommandParser`, którą można używać prosto z `main` w następujący sposób:

```java
public class Starter {
    public static void main(String[] args) {
        new CommandParser().parse(args).execute();
    }
}
```

TADA! I mamy wygodny sposób na stworzenie dosyć zaawansowanego CLI dla bota czy innej dowolnej aplikacji.

Mimo, że opisałem tutaj tworzenie takowego interfejsu za pomocą biblioteki `JCommander` z którą jestem dosyć mocno zaznajomiony
to jednak po lekturze dokumentacji `picocli` jestem zmuszony stwierdzić że pojedynek `JCommander` vs. `picocli` wygrywa
młodszy zawodnik. `picocli` został stworzony wzorując się na `JCommander`, ale brał pod uwagę takie nowoczesne narzędzia 
jak chociażby `GraalVM`, dzięki czemu pozwala na stworzenie wydajnego, natywnego obrazu aplikacji, który uruchamia się jak każda
aplikacja standalone - czyli nie bedzie wymagana do tego maszyna javy. 

`java -jar helgame-bot.jar manu --item "super coal"` staje się -> `helgame-bot manu --item "super coal"`

Następnie zastanowimy się jak zamodelować wsparcie dla tworzenia sporej bazy różnych przedmiotów, czyli będziemy rozszerzać 
bota do manu o kolejne itemki!
 
# [>> Struktury drzewiaste a manufacturing? <<]({{ page.next.id }}) 
## Wpis ten jest częścią serii Helbreath Automation Series - w skrócie <a href="/tags#helbreath-automation-series">#HAS</a>
