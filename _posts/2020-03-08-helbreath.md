---
title: Helbreath?
published: false
comments: true
---

# Słowem wstępu
Wśród moich ziomków z dzieciństwa zawsze uchodziłem za tego który ma specyficzny gust do gier. Inni zachwycali się wspaniałymi
widokami w Gothic 2, błyszczącymi samochodami w NFS Underground, a ja pisałem kolejne komendy w znalezionych na necie MUDach.
Dlatego jak tylko sąsiad znalazł brzydką grę 2D w której można było grać z setkami innych graczy jednocześnie to od razu
wiedział do kogo iść. To było w czasach kiedy World of Warcraft pewnie jeszcze nie był planach, Metin nie istniał, 
a Tibia może i już była, ale nie przyjęła się w moim towarzystwie. Wyobraźcie sobie więc jak ekscytujące było znaleźć grę 
która pozwalała na wspólne granie całej paczce znajomych jednocześnie. Tak się rozpoczęła kilkuletnia przygoda z Helbreath.

# Co to?
Helbreath to koreańskie MMO RPG z roku 1999 wydane przez nieistniejącą już firmę Siementech. W praktyce jest to typowy hack&slash
z dużym naciskiem na skrajny grind. Setki godzin ganiania za Slimami, Skorpionami, Golemami tylko po to, żeby spędzić kolejne
setki polując na graczy frakcji przeciwnej. W tym świecie mamy 2 miasta: Aresden i Elvine. Zasady są proste - jesteś aresem 
i spotykasz elva? To bijesz elva. Chyba że on cie szybciej ubije to spieprzasz ile sił w nogach.

<iframe width="560" height="315" src="https://www.youtube.com/embed/W3yz9ZxbKHQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen="1"></iframe>

# Ale po co to wszystko?
Nostalgiczny powrót do HB wiąże się głównie z niedawnym wydaniem tej gry w formie przeglądarkowej. Wydał ją znany w 
polskiej społeczności HB hacker Drajwer. Czemu hacker? Ten typ wydał Zestaw Małego Hackera i był zmorą małych prywatnych 
serwerów. Ale nie o tym chciałem napisać.
Po co w ogóle to wszystko? Otóż gra ta ma w sobie ten pewien mechanizm. Element który wywołuje u mnie reakcję o której pisałem [wcześniej](2020-03-05-nierowna-walka.md). 
Jest to pewna nudna, powtarzalna czynność, która daje mi przewagę jeśli ją wypracuję.

# Manu
Manufacturing czyli typowy RPGowy crafting. Przerabiasz rudę na sztabki, sztabki na itemki, itemki na hajs i sławę w grze... 
no może tylko hajs. No może nie do końca spory hajs, ale przydaje się, ok? To co mnie tu zajarało to małpia powtarzalnośc - przeciągnij 
surowce na kowadło, kliknij przycisk, poczekaj 4sek i.. od nowa. I tak ~41'000 razy żeby wyćwiczyć 100% skilla. Zakładając,
że przeciagnięcie surowców zajmuje 2sek (bywa różnie), oczekiwanie na wynik zawsze trwa 4sek (a lag?) i mamy dostępne wszystkie 
surowce (nie mamy) to daje nam to nakład ponad 70h ciągłego "grania". W praktyce zajmie to nawet 2-3 razy tyle. 
To nie tylko małpia robota, to czysta głupota i szaleństwo robić to ręcznie.

Potrzebuję maszynkę która zrobi to za mnie. Jak wspomniałem - wyszła "odświeżona" wersja gry w przeglądarce, więc stare
makra trzeba by przynajmniej przerobić, żeby dalej spełniały swoje zadanie. Ale czemu miałbym w ogóle korzystać z gotowców?
Gdzie w tym zabawa? Zrobię to po swojemu! 

# Do roboty!
Pierwszy przedmiot jaki wytworzę to `Super Coal`. Składa się on z trzech grudek `Coal`.

Wstępnie mogę przyjąć bardzo śliskie założenia:
- Okno ekwipunku jest zawsze, co do piksela, w tym samym miejscu.
- `Coal` znajduje się w ekwipunku i jest zawsze, co do piksela, w tym samym miejscu.
- Zawsze mam przynajmniej 3szt. `Coal`
- Jestem przygotowany do rozpoczęcia produkcji czyli kowadło jest otworzone
- Kowadło jest także, co do piksela, w tym samym miejscu

Bardzo ślisko, ale to i tak na razie pierwszy Proof of Concept. Narzędzie napiszę w Javie. Czemu Java? Z dwóch powodów:
1) Klasa `java.awt.Robot` która jest w stanie sterować myszką i klawiaturą
2) Na codzień programuję w Javie, a ja jestem leniwą bułą i nie będę się do tego projektu uczył czegoś nowego

Na szybko przeglądając publiczny interfejs klasy znajduję przydatne mi metody:
- `mouseMove(int x, int y)`
- `mousePress(int buttons)`
- `mouseRelease(int buttons)`

Pozwolę sobie na tym etapie nie wchodzić w szczegóły (a jakieś by były?) i zapostuję gotowca. "Bot" zadziała na ekranie HD
gdzie gra działa w oknie Chrome'a, który to ma widoczny pasek zakładek, a pasek "start" Windowsa jest na dole i ma standardową
wielkość. Wszystko to na pierwszym, głównym monitorze. Wszystko lokalizowane dzięki zahardkodowanym wartościom. Nie denerwujcie się - 
naprawię to w kolejnym poście gdzie wreszcie zrobimy coś ciekawego.

```java
import java.awt.*;
import java.awt.event.InputEvent;

public class ManuAutomation {
    public static void main(String[] args) throws AWTException, InterruptedException {
        Robot robot = new Robot();

        robot.mouseMove(790, 242); //mouse over SuperCoal on list
        robot.mousePress(InputEvent.BUTTON1_DOWN_MASK); //click it
        Thread.sleep(50);
        robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);

        Thread.sleep(100); //wait for anvil to open

        for (int i = 0; i < 3; i++) {
            robot.mouseMove(1051, 463); //mouse over Coal
            robot.mousePress(InputEvent.BUTTON1_DOWN_MASK); //start dragging coal
            robot.mouseMove(933, 407); //mouse over anvil
            robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK); //finish dragging coal
            
            Thread.sleep(50);
        }

        robot.mouseMove(934, 525); //mouse over Manufacture button
        robot.mousePress(InputEvent.BUTTON1_DOWN_MASK); //click it
        Thread.sleep(50);
        robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);

        Thread.sleep(4500); //wait 4.5 sec for manufacturing to finish

        robot.mouseMove(782, 526); //mouse over Back button
        robot.mousePress(InputEvent.BUTTON1_DOWN_MASK); //click it
        Thread.sleep(50);
        robot.mouseRelease(InputEvent.BUTTON1_DOWN_MASK);
    }
}
```

Kod wygląda gorzej niż prostacko. Aż wstyd mi coś takiego tu postować. Brakuje obsługi błędów, wykrywania itemków i przycisków, 
wykrywania sukcesu/faila całego procesu, wartości są zahardkodowane. Jeśli źle podałem koordynaty przycisków/itemków, to postać będzie
tańczyć po całym magazynie (LWH - jeden z dwóch Warehouse'ów w Aresden, mamy lewy i prawy), a okna znajdą się w pseudolosowych miejscach.

Ogarniemy to wszystko w kolejnych odcinkach. Ale najpierw, na rzecz powyższych usprawnień opracujmy:

# >> Przeszukiwanie obrazu << 
