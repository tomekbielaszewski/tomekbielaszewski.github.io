---
author: Grizwold
title: HAS#5 W poszukiwaniu najlepszego algorytmu
published: false
comments: true
tags: polish helbreath-automation-series
---

Kilka tygodni mojego wesołego developmentu Healbreathowego bota spędziłem w podobnym trybie jak za moich studenckich lat. 
Zarwałem kilka nocy, czytałem zawiłe dokumentacje matematycznej analizy obrazów, próbowałem doprowadzić do działania 
stare akademickie algorytmy napisane w C++, skompilowane i wrappowane w Javowe native API, którego nie zaimplementowano 
z zachowaniem jakichkolwiek nowoczesnych standardów. To nie były najprzyjemniej spędzone godziny i nie mają bardzo odkrywczych 
rezultatów - także nie spodziewajcie się plot twista. A czemu to wszystko? Bo pierwszy algorytm przeszukiwania obrazu ssie 
pałkę w większości przypadków do jakich go stosowałem. W dodatku nie dało się w nim zaimplementować w stosunkowo prosty 
sposób tolerancji kolorów, przez co bot nie działał wieczorem kiedy `f.lux` zmieniał paletę kolorów na bardziej żółtawą 
lub kiedy odpaliłem go na AirMacu który domyślnie ma ustawioną nieznacznie cieplejszą paletę kolorów niż Windows. 

Ale po kolei. Opiszę tutaj co sprawdziłem i czemu to się nie sprawdziło.

Na pierwszy strzał idzie [algorytm](https://github.com/tomekbielaszewski/screen-automation/blob/master/src/main/java/pl/grizwold/screenautomation/processing/ColorMapImageLocator.java) 
który już [opisałem]({% link _posts/2020-03-16-przeszukiwanie-obrazu.md %}). Polegał na odwróceniu zależności "współrzędne pixela" vs "kolor pixela".
Przez co po kosztownym preprocessingu pozwalał na super szybkie wyszukiwanie podanych mu części obrazu. Jest to świetne
rozwiązanie i nadal uważam że pozwala na najszybszy sposób wielokrotnego przeszukiwania statycznego obrazu. Ale praktyka
mi mówi co innego. W kodzie bota bardzo często czekam na obecność zadanej ikony: może to być czekanie na zakończenie 
manufacturingu, wyłowienie ryby, pokazanie się logu w lewym dolnym rogu ekranu etc. Czekanie na coś powoduje ciągłe 
powtarzanie preprocessingu bez żadnych korzyści z szybkiego wyszukiwania po nim. Kolejna sprawa to już wspomniany problem
z zaimplementowaniem tolerancji kolorów - to znaczy żeby pozwalał na niedokładne odwzorowanie kolorów ikony na obrazie. 

Następnie postanowiłem iść w trochę bardziej naukową stronę, bo napewno już mądrzejsze głowy siedziały nad takim problemem
który obecnie ja mam - jak najszybsze przeszukiwanie screenshota. Padło na OpenCV i wyliczanie współczynnika korelacji 
(eng. correlation coefficient). Po wielu próbach i failach uzyskałem w miarę działający fragment kodu.
Użyłem template matchingu z metodą CV_TM_CCORR_NORMED. Co na wejściu akceptuje czarno-biały obraz i po obliczeniu daje w 
wyniku macierz współczynników korelacji. Na koniec zostaje jedynie znaleźć maximum macierzy i jest znaleziona lokalizacja ikony.
Niestety ze względu na kosztowny preprocessing (zamiana kolorowego screenshota na czarno-biały), jeszcze bardziej 
kosztowne obliczenia ([template matching](https://docs.opencv.org/2.4/modules/imgproc/doc/object_detection.html)) i dosyć 
tanie wyszukiwanie maximum macierzy - daje to sumaryczne najbardziej posysający algorytm jakie próbowałem.
Dodatkowo w tym algorytmie nie dostanę jednoznacznej odpowiedzi, czy dana ikona znajduje sie na ekranie czy nie. Chyba,
że bym poexperymentował i metodą prób i błędów wyznaczył minimalną wartość maximum macierzy, ale nie uznaję tego za faktycznie
jednoznaczne rozwiązanie.

Na koniec zostawiłem to co początkowo nazwałem najbardziej naiwnym i słabym algorytmem który mi wpadł do głowy - zwykłe
wyszukiwanie [pixel-by-pixel](https://github.com/tomekbielaszewski/screen-automation/blob/master/src/main/java/pl/grizwold/screenautomation/processing/PixelByPixelImageLocator.java). 
Odrzuciłem go z miejsca jeszcze przed zaimplementowaniem algorytmu color-map bo uznałem że będzie miał zbyt dużą złożoność. 
Jednak okazuje się, że jest najszybszy. Widocznie najłatwiej jest go zoptymalizować w JIT. Dodatkowo pozwolana na banalną 
implementację tolerancji kolorów dla ekranów z `f.lux` lub ze zmienioną natywnie paletąkolorów jak AirMac - wystarczy 
założyć maksymalną odległość między dwoma pixelami RGB, traktując składowe jak współrzedne 3D.
Tak wiem, że to tak samo niejednoznaczne jak wyznaczanie współczynnika we wcześniejszym algorytmie, ale jest mi go o wiele 
łatwiej zrozumieć i wyznaczyć go na czuja.

Tak więc moi mili - znowu się okazało że mądre głowy warto posłuchać, bo jak to mówił Donald Knuth, znany pionier informatyki
i profesor Uniwersytetu Stanforda (tak, googlałem):

### "Premature optimization is the root of all evil"

I przez odrzucenie na początku najprostrzego algorytmu który by spełnił swoje zadanie "just enough" dodałem sobie ogrom 
pracy jak i bólu głowy i pleców.

Następnie zrobimy coś prostego, a dokładniej dowiesz się...

# [>> Jak zaimplementować interfejs CLI dla swojej aplikacji <<]({{ page.next.id }}) 
## Wpis ten jest częścią serii Helbreath Automation Series - w skrócie <a href="/tags#helbreath-automation-series">#HAS</a>
