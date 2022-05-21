# Kolejkowanie

W sieciach IP nagłówki pakietów mają taką samą długość, więc jednostką w kolejkach jest pakiet, a nie liczba bajtow. Ruter procesuje każdy pakiet tak samo długo, bo patrzy tylko na nagłówek.



Znając szybkość na łączu możemy policzyć czas obsługi, i dzięki temu można oszacować ile który pakiet będzie czekał w kolejce.

Musimy odpowiednio **zamodelować** napływ i wypływ, zeby policzyć średnio czas oczekiwania.

> Przy VoIP wymagania na opóźnienia 100ms, a jitter musi być 50ms, bo jeżel zmienność opóźnienia się wacha bardziej to głos staje się bełkotem.
>
> Czyli nie ma sensu robienia w sieci bufora większy niż 50ms. W sensie end-to-end nie ma sensu robic bufora wiekszego niz 50ms.
>
> W VoIP straty pakietów to 10^-3.
>
> To są zalecenia IETF do QoS 8 i te wartości wynikają z badań robionych z ITU-T na ludziach i ich subiektywne odczucia.
>
> Też są tolerancje ludzi na czaso oczekiwania na połączenie (wymagania co do sygnalizacji), jakdzwonimy to subiektywnie 3s czekamy, jak dzwonimy do USA to mamy w głowie ze to daleko i 6-7s jest tolerka.

Czyli to wszystko się kolejkuje w każdym elemencie sieci i to się bezpośrednio przekłada na jakość usługi.

W sieciach komórkowych są dwa łatwe modele, rozkład poissona i wzór erlanga

## Symulacja

---wstepne pojecia (1)

To nie jest jedyna technika zeby cos zbadac.

Mamy np. modele matematyczne, ale nie kazdy system da się zamodelować. Dlatego nie ma nic dokładniejszego niż symulacja.

**Symulacja** - technika numeryczna, pozwalająca na przeprowadzenie za pomoca komputera jakieś doświadczenie badawcze. Służy ona ocenie działania systemu, dlaczego podczas symulacji gromadzone są dane w celu oszacowania chrakterystyk opisujący badany system

To co badamy powinniśmy zamodelować jak najmocniej a to co jest dookoła jak czarną skrzynke ze świadomościa co tam jest.

Czasami system wgl nie istnieje bo go tworzymy.

Mamy system i chcemy zobaczyc jak radzi sobie w róznych ruchach. Nie potrzebujemy do tego 1000osób, tylko mozemy je zamodelować. Ważne jest też to co się składa na to co chcemy zbadać, jeśli chcemy zbadać opóźnienia to możemy zbadać tylko kolejki w tym systemie. 

> Np. mamy 4 węzły to nas nie interesuje ze tam jest jakis system zarządzania na kazdym węźle, tylko kolejki na wyjściowych interfejsach ruterów brzegowych. Więc tylko dwie kolejki, napływ i wypływ. A to ze co tydzień zmienia się tablica rutingu w systemach zarządzania to nas nie obchodzi z punktu widzeni połączenia VoIP
>
> Czyli to modelowanie pozwala zrobic ze nie musimy mieć całej rzeczywistości tylko kawałek a reszte modelować.
>
> Jak np. sa testy mobilności w 5G to trzeba by mieć samochód i jezdzic po wawie, a jak moge dobrze to zamodelować to odchodzi mega duzo kosztów.

Babka mówi, że systemy telekomunikacyjne to systemy kolejkowe. 

Bez symulacji nie przetestujemy systemów ktorych jeszcze nie ma.

## System

--wstepne pojecia (2)

Mamy zbiór elementów i nie wsytarczy topologia ale tez i powiazania logiczne miedzy elemetnami czyli protkoly itp.

Wymiarowanie - wiem ze na 10km łącza wprowadza mi 5ms opóźnienia. To mogę zamodelować w systemie ze w naszej symulacji łącze wprowadza np. 7ms opóźnienia i ez.

System  - elemeny i jego powiazania. A jak system przedstawiamy jakims opisem to **model**. Z tym, że **model symulacyjny** nie musi  mieć wszystkich aspektów tego systemu, ale tylko te które są potrzebne do badania systemu pod naszym kątem

> np. nie musimy miec procesów rutingu tylko wpisujemy sciezke na szytwo, no bo jesli badamy na poziomie nanosekund, a takie wpisywanie sciezek dzieje sie co godzine to to nie ma wplywu.

Dlatego model systemu jego odpowiednie przygotowanie powinno byc zrobione starannie i duzo czasu poswiecic. Trzeba wlasciwie zdecydować co jest instotne a co nie. Sztuka podjecia decyzji co wyeliminować.

--wstepne pojecia (3)

stan systemu to co jest potrzebne do przystapienia do symulacji

> np. jak badam sygnalizacje to nie musze uwzgledniac opoznien  na polaczeniach, no bo mimo ze wiadomosci sygnalizacyjne sa przenoszone pakietami to i tak to nie jest istotne.
>
> W sylwestrze system sygnalizacyjny cały czas dziala ale jest tak zapchanie ze nikt nie jest oblsugiwany, dlatego wazne jest zeby okreslic ze w najgorszym przypadku oblugiwane jest tyle i tyle userów. Określamy sygnalizacyjnie godziny najwiekszego ruchu, np. w nocy mało, ale juz w data plane gdzie sa przekazywane dane z calego swiata to nie da sie tak przewidziec (no teraz to sie zmienia bo serwery sa coraz blizej brzegu).

> Internet ma 50 lat. Ale ma takie kanony jak IP i TCP i one sa mimo SND i NFV itp. bez wątpienia to się złamie, były juz prace post-IP al still na tym sie opieramy. Wiemy ze zmiany są niezbędne. Lotniska cały czas mają ATM i UMTS.

---wtepne pojecia (4)

to ze system jest iagly nie znaczy ze mozemy go modelowac w sposb dyskretny i vice versa

W dyskretnym zmainy systemow wystepuja w dyskternych chwilach czasowych, czyli przychodzi oakiet i wychodzi to wtedy zmienia sie stan, nic wiecej nas nie ovhodzi

z ogolu modelujemy system tele jako dyskretne bo sam w swojej naturze jest dyskretny

w systemie ciaglym zmiany sa caly czas w casie np. leci samolot, co nie zmienia faktu ze mozesz go zamodeowac w sposb dyskretny, bo pewne rzeczy dzieja sie dyskretnie, zalezy co chemy modelowac. np. siec w samolocie z racji ze jest dykretna to modelujemy dyskretnie

---spoboy badani systemow

zawsze badamy albo system rzeczywisty albo modelowy, czyli nie zawsze musimy miec rzeczywisty system do badania np, jak go dopiero tworzymy.

W tele systemy musza byc globalne wiec ta skala jest ogromna. Mamy jedno urzadzenie ale musimy patrzec jak on sie zachowuje w wielkiej sieci.

Model fizyczny no to na zasadzie buduje elementy, jakies urzadzenia, a model matematyczny to taka pierwsza podstawa np. modele kolejkowe.

Są dwa matematyczne albo analityczne albo wynik symulacji. najlepiej jak mamy system ktory da sie opisac matematycznie, to wtedy analiza. Jak mamy rozkład possiona czy cos to wszystko daje sie zrobic ale jak mamy cos bardziej zlozonego to nie zawsze daje sie analitycznie, mozemy robic aproksyacje, jakies uproszczenia to warto zweryfikowac jak blisko jestesmy rzeczywistoci poprzez symulacje

bardzo wazna odnoga jest experimentally driven research, zeby od poczatku systemu  expretymujemy kolejne rozwiazania dla niego

---obszary zastosowania metod ....

USa finansje Darpa a Darpa opensorceowe symulatory, to najpierw wsystkie protokoly sa tam symulowana zanim trafia na swiat. 

Np. okno tcp jest ciezke do przewidzenia wiec symulejmy to, tworcy szukaja optymalnych rozwiazan na podstawie symulacji, najpierw byl slow time, potem cos tam dodaja reno itp. Dlatego badania w tcp są bezcenne.

Jak mamy protokoly ktore sa zaimplementowane juz w jakis symulatorach to ich uzyawmy. natomiast nowe technologie to czasem latiwje samemu napisac symulator niz sie uczyc isntiejacego. Wtedy tez mamy mozliwosc doimplementowania czegos

---wady uzycia metod symu...

to nie jest cos co nalezy przyjac ze symulacja daje tai wnik to jest gites

sa rozne problemy technologiczne, lub nie przewdzielismy czegos

naturalnym procesem jest to ze weryfikujemy to przez prototyp, siec pilotową

trzeba byc swiadomym wad

---przklady zastosowania symulacji

no i jakie mamypakieyt sumulacyjne i jak mozemy je zastosowac

---mechanizmy sterowania w sieci

model symulacyjny dla 

- data plane 
- sygnalizacj
- inzynieri ruhu
- zarzadzania sieci

mozemy wydzielic i tylko to modelowac

Np. symulujemy kolejki do przyjmowania polaczen VoIP to niemusimy miec sygnlizacji online od nowych zgloszen tylko mozemy zrobic offline model z rozkladem possiona np. 

lub system sygnalizacji ile moze naplywu polaczen przyjac zeby zapewnic te 3 sekund zestawiania polaczenia wymogu



---podstawowe dostepne pakity symu...

---przyklady zastosowania symulacji... NGN

> nie musimy calych pakietow, np. bez naglowkow robic abstrackcje



---qos (1)

---podstawowe dostepne pakity symu....

RIVERBED juz nie ma studenckiej dlatego korzystamy z NS3

> Wszystkie fimy produkujace urządzenie tele, mają swoje modele w RIVERBED czy czyms takim.

--- podstawowe dostepne (3)

ns-3 od poczatku model kolejkowy

# wYKŁAD 7

---modele symulacyjne (1)

statyczny - nie ma zmian w czasie, opis systemu nie zalezy od czasu ktory plynie

dynamiczny - opisujemy model pod katem co sie dzieje w systemie w czasie, zmiany systemu w czasie (np. są zdarzenia) kolejki pakietow ze przychodzi pakiet

deterministyczny - w okreslonych chwilach czasowych w systemie są określone zmiany (np. pakiety przychodzą równo co x ms)

stochastyczny - z pewnym p-stwem zachodza jakies zmiany w systemie 

---modele symulacyjne(1)

zmieniaja sie w czasie, bo pakiety przychodza, stochastyczne bo userzy robi losowy ruch, deterministyczne bo takie naprawde sa sieci teleinformatyczne



---modele symulacyjne (3)

"zasymulować można wszystko, nawet to czego nie ma" - wiec jak nas fantazja poniesie to zasymulujemy nieprawde, dlatego zawsze wynik symulacji to aproksymacja rzeczywsitego systemu

---Plan (Symulacja zdarzeń)

technika laczaca modele dynamiczne, stochastuczne, dyskretne

--symulacja zdarzen (1)

musimy okreslic ile ta symulacja będzie trwała

> jak mamy rozkład poissona a mamy za krótką symulacje, to tak jakby ten poisson nie "uaktywni się"

Nic oprócz wyniku nie moze nas zakoczyc, wszystko trzeba odpowiednio zaplanowac. 

---symu zda (2)

moze - nie musi, ale moze

trzeba zdefiniowac zdarzenia istotne  z ppunktu widzenia systemu (eliminujemy z rzeczywsitego tylko az zostana te co wplywaja)

np. przybycie pakietu zmienia stan kolejki, zakonczenie procesowania pakietu tez

---przykład(1)

serwer - kanon czy mówimy o serwerze obslugujace w ruterze kolejki czy serwer HTTP

--przyklad(2)

trzeba wiedziec kiedy przybywaja klienci zeby moc to zasymulowac oraz ile obslugiwany jest jeden klient

---przyklad(3)

mozemy tez robic zdarzenie przechodzenie klienta z kolejki do stanowiska, ale mozemy to tez wliczyc w czas obslugi, zeby zredukowac liczbe zdarzen	

Arrival -> Que -> Server -> Departure

w modelach kolejkowych mamy albo kolejki skonczone albo nieskonczone

jesli mamy tylko kolejke to klient przybywa do kolejki, ale jak mamy wiecej kolejek (elementow) to przychodzi do systemu

---mechanizmy zwiekszania czasu

zeby to wszystko ogarnac musimy dobrze kontrolowac czas

czas obslugi od czasu naplywu są niezależne,

czas obslugi pakietu moze zalezec od dlugosci pakietu

czas nie idzie jeden po drugiej, a nie ze robimy cos a potem sie wracamy w czasie, musi byc odpowiednia synchroznizacja

musimy utzymwać zmienna BIEŻACY_CZAS_ZEGARA_SYMULACJI, ktora cala czas ulega inkrementacji, ale nie zmniejszamy go

cala esencja polega na odpowiednim skonstruowania zega rysmulacji

jednostka zegara musi wynikac z rzeczywistosci danego systemu

czas symulacji jest niezalezny od rzeczywistego czasu, czyli mozemy np. 2 dni symulowania systemu, mozemy zrobic w 10min, bo mamy malo zdarzen, albo zasymulowanie 10s systemu trwa 1h, bo jest az tyle zdarzen

--mechanizm zwiekszania czasu

albo co jakis przedzial czasu zwiekszamy czas i patrzymy co sie dzieje (ale tu nie mamy odkladnosci co sie dzieje podczas tego odstępu)

dlatego lepiej patrzyc co sie dzieje jak zdarzy sie zdarzenie (przybycie lub koniec obsluzenia).

jezeli nic sie w systemie nie dzieje to nie ma sensu wtedy zwiekszac czas

---mechanizm zwiekszania czasu przyklad (2)

e1 = 0 + A1

---przyklad symulacja prostego systemu kolejkowego



--podstawowe moduły modelu symulacyjnego

Algorytm zdarzeniowy opisuje co sie dzieje po przybyciu zdarzenia w systemie (np. przybycie obsluzenie).

Lista zdarzen nie generujemy na poczatku wszystkich zdarzen w systemie na liscie, na liscie zdarzen jest po jednym zdarzeniu danego typu (tyle ile mamy typow zdarzen tyle mamy elementów na liście podczas dzialania symulacji, ofc na poczatku nie zawsze)

--schemat blokowy

po pobraniu zdarzenia z listy uaktualniamy czas zegara symulacji

uaktualnienie stanu systemu - jak przybywa klient to albo zajmuje serwer albo trafia do kolejki

wyegenrowanie kolejnego -- generujemy wartosc z jaka przyjdzie nastepne zdarzenie (nie odczytujemy ją z jakiejś tablicy)

na liscie zdarzen jest tylko zdarzenie i dopiero po pobraniu zdarzenia tworzymy tego klienta, ten pakiet a nie ze jego pobieramy z listy zdarzen



> Dodaj opcje ze mozna zatrzymac symulacje i podejrzec staty

//TODO zapytaj jak konczyc symulacje czy zdarzenie czy algorytm zdarzeniowy ma to sprawdzac czy czas nie przekroczony

--definicja problemy

oczasowujemy za pomocą symulacji

dodajemy czasy oczekiwania dla kazdego klienta i na koniec dzielimy przez ich liczbe

--pomiary dotycza oszacowania (2)

non stop zbieramy ile w kazdym czasie bylo pakietow w kolejce

i - liczba klientów

Ti - ile czasu kolejka ma dlugosc i

n - to jest wszystko w funkcji przybywania n-tego klienta.

czyli zebranie ile jest klientow w kazdej sekundzie czasu, zeby miec taki ciagly wykres

no i wtedy calke liczy sie izi, bo liczysz prostokaty `liczba_pakietow_w_kolejce * dlugosc przez ile czasu tak jest`



-- pomiary dotycza oszacowania (3)

zakladamy kolejke nieskonczona

--symulacja prostego systemu kolejkowego (2)

liczba opóźnień - liczba czasów oczekiwań nasze `n`

calkowite opoxnienie - D(n)/n

obszar ponizej Q(t) - 

obszar poniżej B(t) - na biezaco podajemy state zajetsoci serwera

--symulacja prostego systemu kolejkowego (3)

# Wykład 8

Bazowy system to M/M/1, od niego zacznijcie

planowanie zdarzen są różne sposoby nie ma sensu tworzyc zdarzenie przejście do obsługi



Czas symulacji powinien byc na tyle dlugi (nie zawsze okreslony przez czas), zeby pokazaly sie modele analityczne jakie oczkujemy



Obiążenie nie moze byc wieksze niz 1

--system M/M/1

mi - średnia obsluga czas

