# DraganWorld
Parametry populacji
Liczba początkowa osobników Stała lub losowa wartość określająca wielkość populacji startowej.
Czas życia osobnika (lifespan) Parametr przypisany każdemu osobnikowi, określający maksymalną liczbę kroków czasowych, po których osobnik ulega usunięciu z symulacji.
Wiek osobnika Zmienna inkrementowana w każdym kroku czasowym; porównywana z parametrem czasu życia.

Parametry środowiska
Przestrzeń symulacji Obszar, w którym poruszają się osobniki (np. siatka, płaszczyzna, graf sąsiedztwa).
Zasięg interakcji lokalnych Odległość lub kryterium sąsiedztwa, w obrębie którego możliwe jest wykrycie innego osobnika.
Dynamika ruchu Reguły określające przemieszczanie się osobników w przestrzeni (np. losowe, ograniczone prędkością).

Parametry reprodukcji
Warunek reprodukcji Konieczność jednoczesnej obecności dwóch osobników w zasięgu interakcji.
Czas potrzebny do reprodukcji Minimalna liczba kroków czasowych, jakie muszą upłynąć od momentu narodzin osobnika do możliwości skutecznego rozmnożenia.
Efekt reprodukcji Utworzenie nowego osobnika z własnym zestawem parametrów czasu życia.

Parametry dziedziczenia
Dziedziczenie czasu życia Mechanizm przekazywania parametru czasu życia potomstwu (np. kopia wartości rodzica lub wartość losowa w zadanym zakresie).
Zmienność (mutacja) Opcjonalna modyfikacja parametru czasu życia przy narodzinach nowego osobnika.

Reguły selekcji
Usuwanie osobników bez potomstwa Osobniki, które kończą czas życia bez skutecznej reprodukcji, nie wpływają na pulę parametrów następnych generacji.
Brak zewnętrznej kontroli populacji Brak arbitralnego limitu populacji poza wynikającym z reguł reprodukcji i śmierci.

Parametry eksperymentalne
Liczba iteracji symulacji Całkowita liczba kroków czasowych lub warunek zatrzymania.
Warunki początkowe Identyczne parametry startowe przy kolejnych uruchomieniach w celu testowania powtarzalności.
