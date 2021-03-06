---
author: Grizwold
title: HAS#4 Prosty bot z przeszukiwaniem obrazu
published: true
comments: true
tags: polish helbreath-automation-series
---

Mając już algo z [poprzedniego wpisu]({% link _posts/2020-03-16-przeszukiwanie-obrazu.md %}), bardzo łatwo jest przeimplementować nasz 
[siermiężny kod]({% link _posts/2020-03-08-helbreath.md %}) z zahardkodowanymi wartościami na coś bardziej zjadliwego.

Zamiast klikania w konkretne punkty na ekranie - zróbmy klikanie w obrazki. Czyli dokładnie tak jak my robimy na codzień - 
patrzysz w ekran, wyszukujesz wzrokiem obrazki i je klikasz, nie? 

Skoro już mamy opracowany ogólny algorytm to opracujmy interfejs naszego systemu automatyzacji starej koreańskiej gry. 
Na razie wystarczą nam te 3 metody:

```java
interface ScreenAutomation {
    void waitFor(BufferedImage icon);
    void clickOn(BufferedImage icon);
    void drag(BufferedImage fromIcon, BufferedImage toIcon);
}
```

Używszy powyższe i przeimplementawszy wytwór z drugiego [wpisu]({% link _posts/2020-03-08-helbreath.md %}) serii otrzymujemy:

```java
public class ManuAutomation {
    public static void main(String[] args) {
        ScreenAutomation screen = new ScreenAutomationImpl();

        BufferedImage superCoalEntryIcon = readImage(...);
        BufferedImage anvilSuperCoalCraftingIcon = readImage(...);
        BufferedImage anvilDropAreaIcon = readImage(...);
        BufferedImage coalItemIcon = readImage(...);
        BufferedImage anvilManufactureActiveButtonIcon = readImage(...);
        BufferedImage anvilBackButtonIcon = readImage(...);

        screen.clinckOn(superCoalEntryIcon);
        screen.waitFor(anvilDropAreaIcon);

        for (int i = 0; i < 3; i++) {
            screen.drag(coalItemIcon, anvilDropAreaIcon);
            Thread.sleep(50);
        }

        screen.clinckOn(anvilManufactureActiveButtonIcon);
        screen.waitFor(anvilBackButtonIcon);
        screen.clinckOn(anvilBackButtonIcon);
    }
}
```

Jak widać dodało nam to trochę kodu do ładowania obrazków, ale całokształt się uprościł i co najważniejsze - reaguje na 
to co jest na ekranie zamiast klikać na ślepo. Ale jak są zaimplementowane te magiczne metody? I co przedstawiają nasze ikony? 
O tym w kolejnym.... akapicie!

W poprzednich wpisach wspomniałem o klasie `Robot` i o algorytmie przeszukiwania obrazu który [zaimplementowaliśmy]({% link _posts/2020-03-16-przeszukiwanie-obrazu.md %}).
Składając to do kupy otrzymujemy bardzo prostą implementację chyba najważniejszej metody z trzech zaproponowanych: `waitFor`.

Jak by tu czekać na pojawienie się ikony... hmm..? Zapętlić robienie screenshotów i przeszukiwania nowego obrazu az do skutku!

```java
void waitFor(BufferedImage icon) {
    BufferedImage screen = null;
    do {
        screen = refresh();
    } while (!visible(icon, screen))
}

private BufferedImage refresh() {
    return robot.createScreenCapture(workingArea);
}

private boolean visible(BufferedImage icon, BufferedImage screen) {
    return !new ColorMapImageLocator(screen).locate(icon).isEmpty();
}
```

Wiem wiem... jeśli ikona się nigdy nie pojawi to appka się zawiesi i nigdy nasz bot nie zakończy swojej pracy. Ale na 
tym etapie - prostota... prostota... prostota...! Kolejna śliska sprawa to tworzenie instancji naszego algorytmu wyszukiwania
obrazu za każdym przekręceniem się pętli oczekiwania na daną ikonę. To jest między innymi powód dlaczego piszę ten wpis 
niemal pół roku później. Ale to już omówię w kolejnym [wpisie]({{ page.next.id }}) .
Dwie pozostałe metody naszej podstawowej trójcy są już bardzo proste bo mocno bazujące na tym co nam daje algorytm przeszukiwania.

Teraz jedynie zostało odpowiednio dobrać ikony któych szukamy i TA DA! Super prosty bot działa!

![](assets/post4/iron_ingot_finding.png)

Ale nad algorytmem musimy nadal popracować. I rozpocząć...

# [>> ...poszukiwanie najlepszego algorytmu <<]({{ page.next.id }}) 
## Wpis ten jest częścią serii Helbreath Automation Series - w skrócie <a href="/tags#helbreath-automation-series">#HAS</a>
