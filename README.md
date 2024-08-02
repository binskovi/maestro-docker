# Docker Maestro
## Moduł wstępny - Docker w 1h
### Uruchamianie kontenerów
Uruchamianie kontenera za pomocą docker run:
```
docker run -d dnaprawa/2048:1.0
```
d – uruchamia kontener w tle:
```
docker run -d dnaprawa/2048:1.0
```
p – przekierowanie portów. -p [port komputera]:[port kontenera]:
```
docker ps -a
```
a – pokazuje wszystkie kontenery, domyślnie samo docker ps pokazuje tylko uruchomione kontenery:
```
docker ps -a
```
### Obsługa i udostępnianie kontenerów
–name – możemy nadać nazwę swojemu kontenerowi:
```
docker run -d -p 8080:80 --name gra-2048 dnaprawa/2048:1.0
```
docker logs [id]/[names] – pokazuje logi kontenera możemy użyć ID lub nazwy kontenera:
```
docker logs -f gra-2048
```
-f – follow log output, pokazuje pojawiające się logi na bierząco:
```
docker logs -f gra-2048
```
### Podtrzymanie działania kontenera
wyczyszczenie wszystkich dotychczas uruchomionych kontenerów Polecenie jest nieodwracalne!:
```
docker rm $(docker ps -aq) --force
```
uruchamianie kontenera Ubuntu w wersji 20.04:
```
docker run ubuntu:20.04
```
wykonuje polecenie ls -l i kończy swoje działanie:
```
docker run ubuntu:20.04 ls -l
```
Kontener musi mieć proces ktory będize potrzymywał jego działanie.
```
docker run ubuntu:20.04 /bin/bash -c "sleep 60"
```
### Obrazy, co gdzie i jak
Na docker hubie można przeglądć obrazy i je uruchamiać hub.docker.com
Polecenie docker images pokazuje listę wszytkich obrazów w systemie:
```
docker images
```
Jeśli chcemy uruchomić kontener musimy posiadać jego obraz, można go ściągnąć bezpośrednio lub oczekiwać aż docker ściągnie go automatycznie po podaniu nazwy obrazu której nie posiadamy w systemie:
```
docker pull nazwa/obrazu
```
### Budowanie własnego obrazu
docker build – buduje kontener -t nadajemy nazwę naszemu obrazowi :1.0 – nr wersji obrazu . – build context
```
cd apps/dwg-web
docker build -t dwg-web:1.0 .
```
FROM – podajemy na podstawie jakiego obrazu budujemy nasz obraz
LABEL – etykieta
RUN – podajemy jakie polecenia mają być uruchomione podczas budowania naszego kontenera(muszą być dostępne w obrazie bazowym)
COPY – kopiuje lokalne pliki do kontenera
EXPOSE – informacja w metadanych obrazu od programisty dla np. admina
CMD – polecenie jakie ma być wykonane przy starcie kontenera. Te instrukcję można nadpisać uruchamiając kontener.
```
docker image ls
docker run -d -p 8081:80 dwg-web:1.0
docker ps
docker build -t dwg-web:2.0 .
docker run -d -p 8082:80 dwg-web:2.0
```
### Publikowanie obrazu na Docker Hub
docker login – logowanie do Docker Huba
docker tag – przypisanie odpowiedniego tagu do naszego obrazu
docker push – publikuje nasz obraz do repozytorium
```
docker login -u USERNAME
docker image ls
docker tag dwg-web:1.0 dnaprawa/dwg-web:1.0
docker image ls
docker tag dwg-web:2.0 dnaprawa/dwg-web:2.0
docker image ls
docker push USERNAME/dwg-web:1.0
docker push USERNAME/dwg-web:2.0
```
### Komunikacja kontenerów
docker network create -d bridge wp – tworzy nową sieć kontenerową
–network=wp – przy uruchamianiu kontenera musi wskazać, aby korzystał z tej sieci
–restart=always – kontener będzie restartowany za każdym razem, nawet przy restarcie dockera czy komputera
docker-compose.yml – plik konfiguracyjny docker compose (uruchamianie wielu kontenerów naraz)
```
docker compose up -d  // uruchomienie obrazu za pomocą pliku yml
docker volume ls
docker compose down   // zatrzymanie kontenerów
docker compose up -d
docker compose logs   // logi kontenerów
docker compose logs db   // logi tylko kontenera bazy danych
docker compose ps
docker compose down -v  // usuń wszystko, sieć i volumeny.
```
docker compose down -v – zatrzyma nam wszystkie kontenery z compose oraz usunie wszystko, co było stworzone na potrzeby ich uruchomienia (np. sieć)

## Moduł 01 : Podstawy konteneryzacji
### Historia konteneryzacji
Początki roku 2000, twórcy systemu FreeBSD wprowadzili polecenie `Jail` podzielili system na mniejsze niezależne od siebie mniejsze systemy. Dzięki temu była możliwośćizolowania systemu plików, przestrzeni użytkowników czy izolowania sieci. Każdy Jail mógł mieć własny IP i włąsne oprogramowanie, ale aplikacje miały ograniczone funkcjonalności i takie rozwiązanie się nie ostatecznie nie przyjęło.
W 2004 Solaris Zones, system operacyjny, który działa w kontenerze (zone) ma mo zliwość dostępu do przestrzeni użytkownika, procesów, systemu plików a nawet sprzętu. Dostęp tylko do tego, co znajduje się we własnej strefie.
Była już możliwe współdzielenie zasobów sprzętowych i działanie bezpośrednio na kernelu hosta.
W roku 2006 Google zaprezentowało Control Groups, mechanizm do przydzielania i zarządzania zasobami sprzętowymi (CPU, RAM, dysk, sieć) Wtedy nastąpiła rewolucja w świecie konteneryzacji używana do dziś między innymi orzez Dockera.
W 2008 cgroup zostało włącozne do Linux Kernel, dzięki czemu powstałnowy projekt o nazwie LinuX Containers - LXC.
Były to pierwsze pełnoprawne kontenery, wykorzystują mechanizm cgroups oraz Linux namespaces, kontener posiada odizolowane drzewo procesów, sieci i przestrzeni dyskowej.
W 2013 roku narodizny Dockera, zostałzaprezentowany przez założyciela frmy dotCloud. Początkowo wykorzystywał kontenery LXC, dopiero w 2014 roku zastąpił je własnym rozwiązaniem - libcontainer.
### Czym jest Docker i dlaczego jest tak popularny
Docker jest narzędziem do uruchamiania kontenerów, jest to narzędzie Open-Source, pakuje aplikację do kontenera. Można je przenosić na różne systemy operacyjne.
Głównym zamysłem Dockera jest uruchamianie aplikacji czy usług w kontenerach i wyizolowanie aplikacji niezlaeżnie od tego w jakim systemie się zanjduje. Przycznił się mocno do przyspieszenia wdrażania aplikacji na wiele środowisk.



















