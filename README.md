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
Twórcą Dockera jest Solomon Hykes.
### Czym jest Docker i dlaczego jest tak popularny
Docker jest narzędziem do uruchamiania kontenerów, jest to narzędzie Open-Source, pakuje aplikację do kontenera. Można je przenosić na różne systemy operacyjne.
Głównym zamysłem Dockera jest uruchamianie aplikacji czy usług w kontenerach i wyizolowanie aplikacji niezlaeżnie od tego w jakim systemie się zanjduje. Przycznił się mocno do przyspieszenia wdrażania aplikacji na wiele środowisk.
### Podstawowe definicje: obraz, kontener, Docker Hub
Kontener - paczka zawierajaca aplikację i jej zależności, kontenery są od siebie odizolowane
Różnice Obraz vs Kontener - Obraz to inaczej szablon na jakiej podstawie zostanie stworzony kontener a kontener to jakby działająca instancja obrazu.
Docker Hub - centralne miejsce przechowujące bazowe obrazy, możemy też tam przechowywać swoje obrazy
### Aplikacja w kontenerze vs aplikacja w wirtualnej maszynie
Kontenerowi nie musimy na sztywno przypisywać zasobów sprzętowych, pozwala nam to na uruchomienie znacznie większej ilości kontenerów niż moglibyśmy uruchomić maszyn wirtualnych. Porównanie Domu jako Maszyny wirtualnej która musi mieć wszystko dla siebie na stałe, do mieszkania jako kontenera w bloku gdzie niektóre elementy są współdzielone ale jest zachowana pewna odrębność.
### Docker na różnych systemach operacyjnych
Docker dla Linuxa jest najbardizej stabilny.
Docker Desktop for Windows
Docker Desktop for Mac
### Kontenery Linux i Windows
Kontenery to linux, Microsoft z czasem wproadził kontenery Windowsowe. Kontenery Linuxowe mogą działać na Windowsie ale nie odwrotnie.

## Moduł 02 : Uruchamianie kontenerów
### Pierwszy kontener

Utoworzenie kontenera z nazwie obrazu hello-world
```
docker container run hello-world
```
Utworzenie kontenera będzie się opierać o obraz Ubuntu, 'latest' inaczej tag obrazu, oznacza najnowsza wersja tego obrazu
```
docker container run ubuntu:latest
```
Polecenie wyświetla wszystkie istniejaće kontenery, z flagą -l wyświetli wszystkie które były uruchomione wcześniej i zatrzymane
```
docker container ls -a
```
Po uruchomieniu kontenera otrzymujemy również listę kontenerów
```
docker container run ubuntu:latest ls -l
```
Chcę aby kontener był uruchomiony w trybie interaktywnym -it, mogę przekazać ddatkowy argument i przekazać listę strumieni z kontenera na lokalny terminal. Flaga -d oznacza, że chce aby kontener działał w tle. 'bash' wskazuje jako proces kontenera. Flagi spowodowały uruchomienie procesu bash.
```
docker container run -it ubuntu:latest bash
docker container run -it -d ubuntu:latest bash
```
Sprawdzamy stan kontenerów:
```
docker container ls
```
Aby wejść do kontenera, czyli wykonaj polecenie na działajacym kontenerze (-it tryb interaktywny), zamiast nazwy można też podać ID lub pierwsze trzy znaki ID kontenera
```
docker container exec -it <nazwa> bash
```
### Uruchamianie usług w kontenerze

Pobranie obrazu jaki określimy
```
docker image pull nginx:latest
```


Nginx - uruchomienie kontenera,
[-d w tle]
[-p przekieorwnaie ruchu z kontenera z portu 8080 na 80]
[--name mynginx - ustawiamy nazwę kontenera na mynginx]
```
docker container run -d -p 8080:80 --name mynginx nginx:latest
curl localhost:8080
```

PostgreSQL - najpierw ściagamy obraz
[-e - ustawia nam zmienną środowiskową]
[-p - przekieorwanie portu z 5444 na 5432]
obraz wymaga podania użytkownika i hasła
```
docker image pull postgres
docker container run -d -e POSTGRES_USER=user1 -e POSTGRES_PASSWORD=pass123 -p 5444:5432 postgres
```

Nginx - tworzenie i start kontenera
[create - tworzenie kontenera bez uruchamiania]
```
docker container create -p 8081:80 --name nginx2 nginx:latest
docker container ls -a
```
Aby wystartować kontener:
```
docker container start nginx2
curl localhost:8081
```
### Czyszczenie środowiska
Zatrzymywanie i usuwanie pojedynczych kontenerów
```
docker container stop <id_lub_nazwa_kontenera>
docker container rm <id_lub_nazwa_kontenera>
```
Zatrzymanie wszystkich działających w danej chwili kontenerów
```
docker container stop $(docker container ls -q) // <- wstrzyknięcie id kontenerów które działajaobecnie
```
Usuwanie wszystkich kontenerów
UWAGA: trwale usuwa wszystkie kontenery – zarówno działające jak i zatrzymane. Stosuj z rozwagą!
opcja --force wymusza usunięcie kontenerów, bez tej flagi napierw należy zatrzymać kontenery przed usunięciem
-aq -> oznacza wszysktie
```
docker container rm $(docker container ls -aq) --force
```
### Container Run Deep Dive
Co dzieje się po wpisaniu ponizszej instrukcji?
Na początku sprawdzamy czy obraz istenieje lokalnie, jeśli nie to poszukaj go w zdalnym repozytorium.
Następnie sprawdza czy obraz istnieje w repozytorium jeśli tak to pobierz go, rozpakuj i zapisz lokalnie
Potem tworzony jest kontener i trwa przygotowanie do uruchomienia, kontener otrzymuje wirtualny adres IP w prywatnej sieci Dockera,
Dostaje sygnał, że ruch z portu 80 kontenera będzie przekeirowany na port 8080 na hoście.
W ostatnej fazie kontener startuje z pomocą instukcji CMD która jest określona w pliku Dockerfile (Zbiór poleceń służących do zdefiniowania obrazu)
nginx -T <- nadpisanie z linii poleceń instrukcji CMD, czyli np zamiast uruchomienia serwera www zostanie wyswietlona tylko jego konfiguracja
```
docker container run -d -p 8080:80 --name mynginx nginx:latest nginx -T
```
```
docker container ls
docker container logs <id_lub_nazwa_kontenera>
```
### Kontener czyli proces

Wewnątrz kontenera działa tylko jeden główny proces, taka jest rekomendacja. Procesy w kontenerze nie różnią się niczym od procesów na hoście (poza metadanymi mówiącymi że jest proces w kontenerze). Procesy kontenera domyślnie są odizolowane od innych procesów na hoście i kontenerów (można to zmienić)

Przykład 1
```
docker container run ubuntu
docker container ls
docker container run -itd --name myubuntu ubuntu:latest bash
docker container ls
```
Uruchomiony proces w kontenerze widnieje bezpośrednio jako proces na systemie hosta
Sprawdzenie procesów działających w kontenerze, inaczej kolumna PID
```
docker container top myubuntu

ps -aux | grep <PID HERE>
ps -aux | grep bash
```
Przykład 2
```
docker container run -d --name mymongo mongo:latest
docker container top mymongo

ps -aux | grep mongo

docker exec -it mymongo bash
ps -aux   // lista wszystkich procesów
```












