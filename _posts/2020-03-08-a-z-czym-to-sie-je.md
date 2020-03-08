---
title: Hellbreath? A co to, a na co to?
published: false
comments: true
---

# Słowem wstępu
Wśród moich ziomków z dzieciństwa zawsze uchodziłem za tego który ma specyficzny gust do gier. Inni zachwycali się wspaniałymi
widokami w Gothic 2, błyszczącymi samochodami w NFS Underground, a ja pisałem kolejne komendy w znalezionych na necie MUDach.
Dlatego jak tylko sąsiad znalazł brzydką grę 2D w której można było grać z setkami innych graczy jednocześnie to od razu
wiedział do kogo iść. To było w czasach kiedy World of Warcraft pewnie jeszcze był planach, Metin nie istniał, a Tibia może i już była, ale nie
przyjęła się w moim towarzystwie. Wyobraźcie sobie więc jak ekscytujące było znaleźć grę która pozwalała na wspólne granie
całej paczce znajomych jednocześnie. Tak się rozpoczęła kilkuletnia przygoda w świecie Helbreath.

# Co to?
Helbreath to koreańskie MMO RPG z roku 1999 wydane przez nieistniejącą już firmę Siementech. W praktyce jest to typowy hack&slash
z dużym naciskiem na skrajny grind. Setki godzin ganiania za Slimami, Skorpionami, Golemami tylko po to, żeby spędzić kolejne
setki polując na graczy frakcji przeciwnej. W tym świecie mamy 2 miasta: Aresden i Elvine. Zasady są proste - jesteś aresem 
i spotykasz elva? To bijesz elva. Chyba że on cie szybciej ubije to spieprzasz ile sił w nogach.

# Ale po co to wszystko?
Nostalgiczny powrót do HB wiąże się głównie z niedawnym wydaniem tej gry w formie przeglądarkowej. Wydał ją znany w 
polskiej społeczności HB hacker Drajwer. Hacker? To ten typ co wydał Zestaw Małego Hackera i był zmorą małych prywatnych 
serwerów. Ale nie o tym chciałem napisać.
Po co w ogóle to wszystko? Gra ta ma w sobie pewien element. Element który wywołuje u mnie tę reakcję o której 
[wcześniej](2020-03-05-nierowna-walka.md) pisałem. Pewna nudna, powtarzalna czynność, która daje mi przewagę jeśli ją wypracuję.

# Manu
Manufacturing czyli typowy RPGowy crafting. Przerabiasz rudę na sztabki, sztabki na itemki, itemki na hajs i sławę w grze... 
no może tylko hajs. No może nie do końca spory hajs, ale przydaje się, ok? To co mnie tu zajarało to małpia powtarzalnośc - przeciągnij 
surowce na kowadło, kliknij przycisk, poczekaj 4sek i.. od nowa. I tak ~41'000 razy żeby wyćwiczyć 100% skilla. Zakładając,
że przeciagnięcie surowców zajmuje 2sek (bywa różnie), oczekiwanie na wynik zawsze trwa 4sek (lag?) i mamy dostępne wszystkie 
surowce (nie mamy) to daje nam to nakład ponad 70h ciągłego "grania". W praktyce zajmie to nawet 2-3 razy tyle. 
To nie tylko małpia robota, to czysta głupowa i szaleństwo robić to ręcznie.

Potrzebuję maszynkę która zrobi to za mnie. Jak wspomniałem - wyszła "odświeżona" wersja gry w przeglądarce, więc stare
makra trzeba by przynajmniej przerobić, żeby dalej spełniały swoje zadanie. Ale czemu miałbym w ogóle korzystać z gotowców?
Gdzie w tym zabawa? Zrobię to po swojemu! 

# Do roboty!
Pierwszy przedmiot jaki wytworzę to `Super Coal`. Składa się z 3x `Coal`. Wstępnie mogę przyjąć bardzo śliskie założenia:
- Okno ekwipunku jest zawsze, co do piksela, w tym samym miejscu.
- `Coal` znajduje się w ekwipunku i jest zawsze, co do piksela, w tym samym miejscu.
- Zawsze mam przynajmniej 3szt. `Coal`
- Jestem przygotowany do rozpoczęcia produkcji czyli kowadło jest otworzone
- Kowadło jest także, co do piksela, w tym samym miejscu

Bardzo ślisko, ale to i tak na razie pierwszy Proof of Concept. Narzędzie napiszę w Javie. Czemu Java? Z dwóch powodów:
1) Klasa `Robot` która jest w stanie sterować myszką i klawiaturą
2) Na codzień programuję w Javie, a ja jestem leniwą bułą i nie będę się do tego projektu uczył czegoś nowego
