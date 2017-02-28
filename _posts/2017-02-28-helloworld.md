---
layout: post
title:  "Inteligentne miasto - co to takiego?"
date:   2017-02-28 13:17:38 +0100
categories: inteligentne_miasto
excerpt_separator:  <!--more-->
---

**Inteligentne miasto** to zestaw kilku modeli służących do kontrolowania ruchu na przygotowanych ze specjalnych puzzli skrzyżowaniach, przejściach dla pieszych oraz przejazadach kolejowych. Jako, że jeden obraz wart jest więcej, niż tysiąc słów, poniższe zdjęcie z pewnością da lepszy pogląd na to, czym jest rzeczony zestaw:

![Zdjęcie zestawu]({{ site.url }}/assets/images/zestaw-czysty.jpg)

<!--more-->

[tl;dr](#programowanie-inteligentnego-miasta)

Twórcy - [Fundacja Rozwoju Branży Internetowej NETCAMP](http://fundacja.netcamp.pl) - określają **Inteligentne miasto** mianem zabawki skierowanej do dzieci w wieku 4+, która służyć ma rozwojowi kreatywności i myślenia algorytmicznego, nauce programowania i, przede wszystkim, dobrej zabawie. Wydaje mi się, że ten opis jest całkiem dobry, dodałbym tylko, że dostrzegam w tym potencjał dla osób o wiele starszych, niż "dzieci".

Typowy scenariusz _dydaktyczny_ wygląda tak: rozkładamy puzzle, budując makietę skrzyżowania lub przejazdu kolejowego, układamy modele sygnalizacji świetlnej, szlabanu i lampy, podłączamy dołączony komputerek, łączymy się z nim za pomocą komputera i oprogramowujemy nasze _miasto_. Do tego zadania doskonale sprawdza się Snap4Arduino - program, w którym algorytm sterowania miastem możemy stworzyć układając odpowiednie bloczki z atomowymi poleceniami ("zapal czerwone światło na sygnalizatorze nr 1", "zgaś zielone światło na sygnalizatorze dla pieszych" itp.). Gdy wszystko działa, do zestawu można dołożyć kilka innych zabawek, jak resoraki, pociągi, budynki i figurki, żeby miasto zaczęło tętnić życiem!

[![Klip promocyjny]({{ site.url }}/assets/images/klip_promo.jpg)](https://www.youtube.com/watch?v=yu2tCV-66X4)

_Uwaga: filmik powyżej powstał na potrzeby kampanii na wspieram.to, która już się zakończyła. Proszę, nie zwracajcie na to uwagi_

## Po co ten blog?

**Inteligentne miasto** nie jest profesjonalną zabawką. Chłopaki i dziewczyny z [Netcampu](http://fundacja.netcamp.pl) drukują modele na drukarkach 3d (które najczęściej wymagają jeszcze wielu szlifów), sami lutują elektronikę (w tym miejscu pozdrawiam brata - Macieju, dobra robota!) i montują puzzle - a te są całkiem pomysłowe, każdy puzzel ma takie małe nóżki, aby dało się sprytnie schować wszystkie kable pod makietą. Na koniec wgrywają soft na (klona) arduino i tak przygotowany zestaw jest gotowy do zabawy. I tu wkraczam ja, cały na biało.

## Programowanie Inteligentnego miasta

Jak już wspomniałem, jednym ze sposobów oprogramowania zabawki jest użycie Snapa. To świetne narzędzie dla dzieci oraz dla osób, które chcą zobaczyć, że to działa, ale po 20 latach programowania za pomocą klawiatury i edytora tekstu, nie wyobrażam sobie pisania bardziej skomplikowanego algorytmu przeciągając bloczki myszką. A skoro kontrolą nad wszystkimi modelami zajmuje się (klon) Arduino, to znaczy, że tak też się da. Dlatego postanowiłem pomóc [Netcampowi](http://fundacja.netcamp.pl) i w ramach tego projektu stworzyć zbiór bibliotek oraz tutoriali, które ułatwią start z bardziej "dorosłym" programowaniem Inteligentnego miasta. Moim celem jest stworzenie:

- biblioteki w C, która będize zawierała podstawowe funkcje do rozpoznawania podłączonych modeli oraz wykonywania na nich atomowych operacji,
- drugiej biblioteki w C, która będzie niejako frameworkiem zawierającym bardziej złożone operacje,
- wraperów w C# na powyższe (jako nuget package)
- CLI, które pozwoli sterować IM z poziomu wiersza poleceń

Każdy etap mojego projektu zamierzam opisać tak, by powstał z tego swojego rodzaju tutorial opisujący podstawy programowania mikrokontrolerów.

{% include about.md %}