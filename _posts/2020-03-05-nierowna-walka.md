---
author: Grizwold
title: HAS#1 Nierówna walka
published: true
comments: true
tags: polish helbreath-automation-series
---

Kiedy po latach przewalania JSONów z jednego kubła w drugi dopada cię wypalenie zawodowe. Już nie możesz patrzeć na kolejne DTO,
Factory czy inne Strategy, ale mimo wszystko kochasz kodzenie. Zaczynasz walczyć z tą chorobą kodząc **WIĘCEJ**. Więcej ciekawych
rzeczy. Czegoś czego nie da ci codzienna praca. Moje repozytoria na githubie mnożą się, tyją, rosną i nigdy nie zostają
w żaden sposób opublikowane dalej niż kod źródłowy na githubie. Koduję nieprzydatne userscripts do chrome'a, boty 
analizujące ruch na znanej stronie ze śmiesznymi obrazkami, więcej skryptów, silnik gier, jakieś przykłady i inne code kata.
Bawię się jak dziecko, wreszcie, po pracy. Jedni hodują koty, inni dłubią w elektronice, jeszcze insi sadzą krzaki jagód.
Ja robię wszystkie z tych rzeczy, ale nic nie daje mi takiego spełnienia jak wieczorne zakodzenie bota do Ogame, silnika 
gier tekstowych czy uśmiechniętej twarzy mihaiła biaukowa wysuwającej się zza logo jegoż dzieła życia.

Żeby nie było - praca 9-17 daje mi satysfakcję. Spędzam czas z ludzmi którzy kochają kod podobnie jak ja (no może nie aż 
tak), rozwiązuję nietrywialne problemy jak distributed transactions, cache invalidation czy off-by-one errors. To ważne 
problemy które należy z pełnym profesjonalizmem roztrzaskać. Ale gdzie tu eksperymentowanie? Gdzie tu dziecięca zabawa? 
Gdzie tu posrany humor?

Czasami trochę wdaję się w większość społeczeństwa. Siądę do konsoli czy lapka. Włączę gierkę i gram. Zbieram itemki, nabijam
punkty, pokonuję bossów. Nie wciągam się jednak w fabułę, nie rozkminiam strategii, nie czuję tego. Zazwyczaj typowe granie
porzucam po pierwszym lub drugim tygodniu. Czasami jednak, nie za często, coś mi w głowie kliknie. Znajdę w grze jakiś
maleńki element który dotknie tych konkretnych zwojów mojego mózgu, odpali odpowiednie neurony i wystrzeli mi maleńka dawka 
dopaminy. Zaczynam rozkminiać jak oszukać system. Jak wykorzystać tę jedną mechanikę.

## Ogame - podgląd mapy galaktyki
Ogame - podgląd mapy galaktyki. Znaleźć tam można coś co zwą Polem Zniszczeń. Jest to pozostałość po walce dwóch graczy.
Większa czy mniejsza kupka surowców która tylko czeka na moje Recyklery, które mi ją przyniosą w moje chciwe łapska. 
Przeglądanie mapy galaktyki jest męczące. 9 galaktyk, 499 układów, 15 slotów na planety. Daje to przynajmniej 4990 kliknięć 
i 4990 spojrzeń na 15-to rzędową tabelkę.

Cholera! Jestem programistą! Nie będę odwalac małpiej roboty w grze!

3 dni roboty i jest! [ugamela-galaxy-scanner](https://github.com/tomekbielaszewski/ugamela-galaxy-scanner)

## Codzienność w Ogame
Moja codzienna gra w Ogame wyglądała następująco:
- zaatakuj znane mi farmy
- prześlij zgrabione surowce na kolonie
- rozbuduj kolonie
- zbierz nadwyżkę surowców z kolonii
- kup coś droższego na planecie matka

Nie brzmi to trochę... Małpio? Jak tylko o tym więcej pomyślałem to od razu zaświeciły mi się oczy, pojawił się chytry uśmiech
i wziąłem się do roboty. Znowu 3 dni później miałem gotową nową zabawkę.
[ugamela-automation](https://github.com/tomekbielaszewski/ugamela-automation)

## Czy ja już nie umiem normalnie grać?
Nudzę się, to zaczynam grać. Gra zaczyna mnie nudzić, to zaczynam automatyzować. Automatyzacja zabiera mi grę, to zaczynam się nudzić. 
Czy ja już nie potrafię normalnie grać? Pewnie nie. Ale zauważyłem jedno - prawdziwą przyjemność daje mi opracowanie narzędzia
oszustwa. Zakodzenie skryptu przeszukującego galaktykę, stworzenie skryptów gherkina atakujących pobliskie farmy.
To specyficzne uczucie przewagi nad innymi. 

## Spróbuję czegoś nowego
Spróbuję czegoś nowego, czego chciałem spróbować od dawna. Powyższe przykłady to gierki przeglądarkowe które można łatwo
oszukać userscriptem w Tamper Monkey czy czymś jak Selenium Web Driver. A co z grami standalone? A co z grami przeglądarkowymi, 
ale nie opartymi na drzewie DOM? Flash, Canvas, Native? Czas na mniej trywialne zadanie.

# [>> Napiszmy bota do Helbreath <<]({{ page.next.id }})
## Wpis ten jest częścią serii Helbreath Automation Series - w skrócie <a href="/tags#helbreath-automation-series">#HAS</a>