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

Na pierwszy strzał idzie algorytm który już opisałem. Polegał na odwróceniu zależności "współrzędne pixela" vs "kolor pixela".
Przez co po kosztownym preprocessingu pozwalał na super szybkie wyszukiwanie podanych mu części obrazu. Jest to świetne
rozwiązanie i nadal uważam że pozwala na najszybszy sposób wielokrotnego przeszukiwania statycznego obrazu. Ale doświadczenie
mi mówi co innego. W kodzie bota bardzo często czekam na obecność zadanej ikony: może to być czekanie na zakończenie 
manufacturingu, wyłowienie ryby, pokazanie się logu w lewym dolnym rogu ekranu etc. Czekanie na coś powoduje ciągłe 
powtarzanie preprocessingu bez żadnych korzyści z szybkiego wyszukiwania po nim. Kolejna sprawa to już wspomniany problem
z zaimplementowaniem tolerancji kolorów - to znaczy żeby pozwalał na niedokładne odwzorowanie kolorów ikony na obrazie. 

# [>> tutaj tytul kolejnego odcinka <<]({{ page.next.id }}) 
## Wpis ten jest częścią serii Helbreath Automation Series - w skrócie <a href="/tags#helbreath-automation-series">#HAS</a>
