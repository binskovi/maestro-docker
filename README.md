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
### Inspekcja kontenera

Podgląd konfiguracji ontenera, stanu zużycia zasobów oraz przeglądanie logów (docker container inspect, docker container stats, docker container logs)
Do sprawdzenia konfiguracji kontenera używay polecenia:
```
docker container inspect
```
Logi kontenera sprawdzamy poleceniem:
```
docker container logs
```
Zużycie zasobów kontenera poleceniem:
```
docker container stats
```
Przykłady:
Tworzymy kontener: alpine. -e: zmienna środowiskowa, -it: tryb interaktywny, sh to jest to samo co bash czyli shell
Następnie sprawdzamy jego konfigurację, otrzymujemy w formacie json
```
docker cotainer create -e TEST_ENV=test -it --name myalpine1 alpine:latest sh
docker container inspect myalpine1
```
Możemy parametryzować t oco chcemy zoabczyć:
```
docker container inspect --format='{{.Config.Image}}' myalpine1  // nazwa obrazu
docker container inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' alpine1  // sprawdza przypisany IP
```

Sprawdzamy logi kontenera ngnix:
```
docker container run -d -p 8088:80 --name mynginx1 nginx
docker container logs <container_name>
```
Możemy podglądać logi na żywo:
```
docker container logs -f <nazwa_kontenera> 
```

Sprawdzamy stan zużycia zasobów:
```
docker container stats <nazwa_kontenera>
docker container stats --no-stream <nazwa_kontenera>
```
### Uruchamianie usług – poziom 2
Apache
```
docker container run --rm -d --name myapache -p 8000:80 httpd:2.4
curl localhost:8000 // sprawdzamy czy odpowiada
docker container ls
```

MySql, flaga --restart always spowoduje automatyczny restart kontenera w przypadku błędu, ta flaga nie spowoduje restartu w przypadku świadomego zakończenia działania kontenera.
```
docker container run --name db -e MYSQL_ROOT_PASSWORD=mysecret-password --restart always -d -p 3306:3306 mysql:5.7
```
Wyświetlenie logów
```
docker container logs db
```
Zatrzymanie wszystkich kontenerów
```
docker container stop $(docker container ls -a -q) // wszystkie działające
```
Ponowne uruchomienie kontenera
```
docker container start db
docker container ls
```
## Moduł 03 : Praca z obrazami
### Docker Hub i repozytoria
https://hub.docker.com
Docker Hub to domyślne miejsce z którym łączy się Docker po wpisaniu komendy `docker image pull`. Możemy tam założyć konto oraz dodawać swoje obrazy publiczne lub prywatne. Oficjalne repo obrazów. Ważne aby zwrócić uwagę czy obraz posiada etykietę Oficialny.
```
docker image pull alpine:3.7
docker image pull alpine:3.6
```
### Tagowanie i publikowanie obrazów na Docker Hub
Aby publikować swoje obrazy musimy stworzyc sobie konto na DockerHub
Zadanie:
Dodaj nowy tag do obrazu alpine:3.9
```
docker image pull alpine:3.9
docker image tag alpine:3.9 <TWOJA_NAZWA_UŻYTKOWNIKA>/alpine:3.9
docker image ls
```
Zaloguj się do Docker Huba za pomocą danych, które zostały podane podczas tworzenia konta na Docker Hub.
```
docker login
```
Opublikuj obraz na Docker Hub
```
docker image push <TWOJA_NAZWA_UŻYTKOWNIKA>/alpine:3.9
```
Usuń lokalny obraz i pobierz go z Docker Huba
```
docker image rm <TWOJA_NAZWA_UŻYTKOWNIKA>/alpine:3.9
docker image rm alpine:3.9
docker image ls
docker image pull <TWOJA_NAZWA_UŻYTKOWNIKA>/alpine:3.9
docker image ls
```
Uruchom kontener na podstawie własnego obrazu
```
docker container run -it <TWOJA_NAZWA_UŻYTKOWNIKA>/alpine:3.9 sh
ls
exit
```
Publikowanie innej wersji obrazu
```
docker image pull alpine:3.7
docker image tag alpine:3.7 <TWOJA_NAZWA_UŻYTKOWNIKA>/alpine:3.7
docker image push alpine:3.7
```
### Prywatne repozytoria
Rozwiązania płatne
Docker Hub Pricing https://hub.docker.com/subscription?plan=individual&paid=true
Azure https://azure.microsoft.com/en-us/pricing/details/container-registry/
AWS https://aws.amazon.com/ecr/pricing/
GCP https://cloud.google.com/container-registry
Rozwiązania darmowe
Canister https://canister.io/
Gitlab https://docs.gitlab.com/ee/user/packages/container_registry/index.html

### Dockerfile
Utwórz plik o nazwie Dockerfile oraz plik text.txt.
Zawartość pliku Dockerfile
```
FROM alpine:3.9
COPY text.txt .
CMD ["cat", "text.txt"]
```
Zawartość pliku text.txt
```
Docker Maestro
```
Uruchom terminal i przejdź do katalogu, w którym zostały utworzone pliki Dockerfile oraz text.txt
```
docker image build -t myalpine .   // budowanie obrazu i wersji -t , . - domyślny katalog
docker container run --name alpine1 myalpine:latest
```
FROM - specyfikuje obraz bazowy. Cała zawartość obrazu trafia do finalnego obrazu
RUN - pozwala na wykonanie dowolnego polecenia dostępnego w obrazie bazowym (apt-get, mkdir, ln itp.)
COPY - kopiuje pliki lub katalogi z hosta, do określonej lokalizacji wewnatrz obrazu
CMD - określa jaka komenda ma zostać wykonana po uruchomieniu kontenera na podstawie obrazu

### Rozszerzenie oficjalnych obrazów
Kopiowanie plików z kontenera
```
docker container run -d --name nginx1 nginx  // uruchomienie
docker container cp nginx1:/usr/share/nginx/html/index.html index.html  // kopiowanie
```
Edytuj plik index.html za pomocą dowolnego edytora tekstu i zapisz zmiany.
Tworzenie Dockerfile:
Utwórz plik Dockerfile w tej samej lokalizacji, w której znajduje się plik index.html
```
FROM nginx:latest

COPY index.html /usr/share/nginx/html
```
Aktualizacja 12.12.2022: Usuń komendę CMD ["nginx", "-g", "daemon off;"]
Budowanie obrazu i uruchamianie kontenera
```
docker image build -t mynginx .
docker container run -d -p 8081:80 --name mynginx mynginx:latest
curl localhost:8081
```
