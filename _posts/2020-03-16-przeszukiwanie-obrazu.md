---
author: Grizwold
title: Przeszukiwanie obrazu
published: false
comments: true
tags: polish helbreath-automation-series
---

W poprzednim [wpisie]({{ page.previous.previous.id }}) o pisaniu bota do Helbreath uzyskaliśmy prostackie klikanie na ślepo
w zahardkodowane punkty. Czas to zmienić. Stworzę tu prostą klasę do przeszukiwania obrazu ekranu. Załóżmy, że ma ona nam 
podać listę punktów gdzie znajduje się szukany mniejszy obraz w układzie współrzędnych większego obrazu.

![](assets/post4/iron_ingot_finding.png)

A sygnatura tegoż będzie następująca: `List<Point> locate(BufferedImage screen, BufferedImage icon)`

# (Bardzo) Naiwny algorytm wyszukiwania ikon na ekranie
Pierwsze co wpada do głowy kiedy myśli się o wyszukiwaniu ikon na ekranie to lecieć piksel po pikselu i sprawdzać czy
kolor sprawdzanego piksela zgadza się z kolorem pierwszego piksela na ikonie. Jeśli tak to sprawdz kolejny piksel na ekranie
i w ikonie. Jeśli nie to sprawdź kolejny piksel na ekranie i porównaj znowu z pierwszym na ikonie.

```java
List<Point> locate(BufferedImage screen, BufferedImage _icon) {
    final BufferedImage icon = icon_.getImage();
    final List<Point> foundLocations = new ArrayList<>();

    for (int screenX = 0; screenX < screen.getWidth(); screenX++) {
        for (int screenY = 0; screenY < screen.getHeight(); screenY++) {
            if(screen.getRGB(screenX, screenY) == icon.getRGB(0, 0)) {
                for (int iconX = 1; iconX < icon.getWidth(); iconX++) {
                    for (int iconY = 0; iconY < icon.getHeight(); iconY++) {
                        // compare every icon pixel with corresponding screen pixel
                    }
                }
                // if every icon pixel matches: add tested location to foundLocations list
            }
        }
    }

    return foundLocations;
}
```

Od razu można powiedzieć, że tak zaimplementowany algorytm to jest bardzo zły pomysł. 4-krotnie zagnieżdżony for, z if'em 
po środku. Czas wykonywania tegoż cuda to:
- `O(N)` kiedy `N`-pikselowy obraz nie zawiera ani jednego piksela w kolorze pierwszego piksela ikony
- `O(N + M)` kiedy `N`-pikselowy obraz zawiera dokładnie jedno wystąpienie szukanej `M`-pikselowej ikony i nie zawiera 
innych pikseli w kolorze pierwszego piksela ikony
- `O(N * M)` Kiedy `N`-pikselowy obraz zawiera `M`-pikselową ikonę na każdym punkcie ekranu (na przykład czarna ikona 
szukana na czarnym ekranie)

Czyli wyszukanie na ekranie 4K (4096 × 2160) ikony 100x100 to: od ‭8'847'360‬ operacji do ‭88'473'600'000‬ (sic!).

Algorytm do kosza, idziemy dalej.

# Mniej naiwny algorytm wyszukiwania ikon na ekranie
A co jakby odwrócić role i zamiast sprawdzania koloru w danym punkcie ekranu to sprawdzać wszystkie punkty ekranu o danym 
kolorze? Wtedy bierzemy wszystkie takie punkty na ekranie o kolorze pierwszego piksela ikony bez iterowania po reszcie 
niepasujących. Następnie porównujemy każdy kolejny piksel ikony z odpowiadającymi (odpowiednio przesuniętymi) pikselami
ekranu.

W różnych sytuacjach uzyskamy tu złożoności:
- `O(1)` kiedy `N`-pikselowy obraz nie zawiera ani jednego piksela w kolorze pierwszego piksela ikony
- `O(M)` kiedy `N`-pikselowy obraz zawiera dokładnie jedno wystąpienie szukanej `M`-pikselowej ikony i nie zawiera 
innych pikseli w kolorze pierwszego piksela ikony
- `O(N * M)` Kiedy `N`-pikselowy obraz zawiera `M`-pikselową ikonę na każdym punkcie ekranu (na przykład czarna ikona 
szukana na czarnym ekranie)

Poprawę widać od razu, ale coś mi tu śmierdzi. Jaki format obrazu pozwala na pobranie wszystkich punktów ekranu na podstawie
koloru? Niestety żaden mi znany. Potrzebny jest tutaj preprocessing obrazu ekranu. Zamienię tutaj `BufferedImage` na 
`Map<Integer, List<Point>>` gdzie Integer to zakodowany binarnie kolor (w takim formacie jest zwracany kolor z metody
`BufferedImage::getRGB`).

```java
Map<Integer, List<Point>> colourMap = new HashMap<>();
for (int x = 0; x < screen.getWidth(); x++) {
    for (int y = 0; y < screen.getHeight(); y++) {
        int pixel = screen.getRGB(x, y);
        List<Point> points = colourMap.computeIfAbsent(pixel, k -> new ArrayList<>());
        points.add(new Point(x, y));
    }
}
```

Powyższa metoda ma stałą złożoność `O(N)` dla `N`-pikselowego obrazu. Plus złożoność operacji dodawania nowego elementu do mapy,
która wynosi od `O(1)` do `O(log N)`. 

Ale to nam psuje wszystko co osiągnęliśmy w naszej wspaniałej optymalizacji, c'nie? Otóż nie drogi padawanie!

Słowo klucz: preprocessing - daje nam to możliwość wielokrotnego, taniego wyszukiwania w raz przeliczonym obrazie.  

# Implementacja
To takim wytłumaczeniu to chyba napisanie kodu to już formalność. Moja implementacja znajduje 
się [tutaj](https://github.com/tomekbielaszewski/screen-automation/blob/master/src/main/java/pl/grizwold/screenautomation/ImageLocator.java).

I jak widać sygnatura rozbiła się na konstruktor: `ImageLocator(BufferedImage base)` i główną metodę `List<Point> locate(Icon icon)`

Zaimplementowane funkcjonalności które skutecznie zaciemniają kod to:
- gwiazda naszego odcinka: wyszukiwanie podanej ikony na bazowym obrazie
- ignorowanie pikseli na ikonie które są w kolorze maski (użyłem pięknego koloru magenta) - bardzo przydatne do wyszukiwania 
nieregularnych ikon ze zmiennym tłem jak napisy wyświetlane na ekranie.
- wizualne debugowanie które zapisuje serię obrazów z wizualizacją działania algorytmu - pokazuje jak odfiltrowywane były
prawdopodobne lokalizacje ikony w kolejnych iteracjach algorytmu. Pomagało mi to znaleźć nieoptymalnie dobrane ikony, a do
tego jest poprostu fajne.

[Kilka obrazków debugowania]

W kolejnym odcinku użyję tego algorytmu w praktyce i przeimplementuję to co popełniłem we [wcześniejszym]({{ page.previous.previous.id }}) wpisie.

# [>> Helbreath z rozpoznawaniem obrazu <<]({{ page.next.id }}) 
