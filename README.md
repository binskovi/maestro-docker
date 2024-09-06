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

### 01.01 : Historia konteneryzacji

Początki roku 2000, twórcy systemu FreeBSD wprowadzili polecenie `Jail` podzielili system na mniejsze niezależne od siebie mniejsze systemy. Dzięki temu była możliwośćizolowania systemu plików, przestrzeni użytkowników czy izolowania sieci. Każdy Jail mógł mieć własny IP i włąsne oprogramowanie, ale aplikacje miały ograniczone funkcjonalności i takie rozwiązanie się nie ostatecznie nie przyjęło.
W 2004 Solaris Zones, system operacyjny, który działa w kontenerze (zone) ma mo zliwość dostępu do przestrzeni użytkownika, procesów, systemu plików a nawet sprzętu. Dostęp tylko do tego, co znajduje się we własnej strefie.
Była już możliwe współdzielenie zasobów sprzętowych i działanie bezpośrednio na kernelu hosta.
W roku 2006 Google zaprezentowało Control Groups, mechanizm do przydzielania i zarządzania zasobami sprzętowymi (CPU, RAM, dysk, sieć) Wtedy nastąpiła rewolucja w świecie konteneryzacji używana do dziś między innymi orzez Dockera.
W 2008 cgroup zostało włącozne do Linux Kernel, dzięki czemu powstałnowy projekt o nazwie LinuX Containers - LXC.
Były to pierwsze pełnoprawne kontenery, wykorzystują mechanizm cgroups oraz Linux namespaces, kontener posiada odizolowane drzewo procesów, sieci i przestrzeni dyskowej.
W 2013 roku narodizny Dockera, zostałzaprezentowany przez założyciela frmy dotCloud. Początkowo wykorzystywał kontenery LXC, dopiero w 2014 roku zastąpił je własnym rozwiązaniem - libcontainer.
Twórcą Dockera jest Solomon Hykes.

### 01.02 : Czym jest Docker i dlaczego jest tak popularny

Docker jest narzędziem do uruchamiania kontenerów, jest to narzędzie Open-Source, pakuje aplikację do kontenera. Można je przenosić na różne systemy operacyjne.
Głównym zamysłem Dockera jest uruchamianie aplikacji czy usług w kontenerach i wyizolowanie aplikacji niezlaeżnie od tego w jakim systemie się zanjduje. Przycznił się mocno do przyspieszenia wdrażania aplikacji na wiele środowisk.

### 01.03 : Podstawowe definicje: obraz, kontener, Docker Hub

Kontener - paczka zawierajaca aplikację i jej zależności, kontenery są od siebie odizolowane
Różnice Obraz vs Kontener - Obraz to inaczej szablon na jakiej podstawie zostanie stworzony kontener a kontener to jakby działająca instancja obrazu.
Docker Hub - centralne miejsce przechowujące bazowe obrazy, możemy też tam przechowywać swoje obrazy

### 01.04 : Aplikacja w kontenerze vs aplikacja w wirtualnej maszynie

Kontenerowi nie musimy na sztywno przypisywać zasobów sprzętowych, pozwala nam to na uruchomienie znacznie większej ilości kontenerów niż moglibyśmy uruchomić maszyn wirtualnych. Porównanie Domu jako Maszyny wirtualnej która musi mieć wszystko dla siebie na stałe, do mieszkania jako kontenera w bloku gdzie niektóre elementy są współdzielone ale jest zachowana pewna odrębność.

### 01.05 : Docker na różnych systemach operacyjnych

Docker dla Linuxa jest najbardizej stabilny.
Docker Desktop for Windows
Docker Desktop for Mac

### 01.06 : Kontenery Linux i Windows

Kontenery to linux, Microsoft z czasem wproadził kontenery Windowsowe. Kontenery Linuxowe mogą działać na Windowsie ale nie odwrotnie.

## Moduł 02 : Uruchamianie kontenerów

### 02.01 : Pierwszy kontener

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

### 02.02 : Uruchamianie usług w kontenerze

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

### 02.03 Czyszczenie środowiska

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

### 02.04 Container Run Deep Dive

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

### 02.05 Kontener czyli proces

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

### 02.06 Inspekcja kontenera

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

### 02.07 Uruchamianie usług – poziom 2

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

### 03.01 Docker Hub i repozytoria

https://hub.docker.com
Docker Hub to domyślne miejsce z którym łączy się Docker po wpisaniu komendy `docker image pull`. Możemy tam założyć konto oraz dodawać swoje obrazy publiczne lub prywatne. Oficjalne repo obrazów. Ważne aby zwrócić uwagę czy obraz posiada etykietę Oficialny.

```
docker image pull alpine:3.7
docker image pull alpine:3.6
```

### 03.02 Tagowanie i publikowanie obrazów na Docker Hub

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

### 03.03 Prywatne repozytoria

Rozwiązania płatne
Docker Hub Pricing https://hub.docker.com/subscription?plan=individual&paid=true
Azure https://azure.microsoft.com/en-us/pricing/details/container-registry/
AWS https://aws.amazon.com/ecr/pricing/
GCP https://cloud.google.com/container-registry
Rozwiązania darmowe
Canister https://canister.io/
Gitlab https://docs.gitlab.com/ee/user/packages/container_registry/index.html

### 03.04 Dockerfile

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

### 03.05 Rozszerzenie oficjalnych obrazów

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

### 03.06 Budowanie własnego obrazu – czyli konteneryzacja aplikacji

W wersji uproszczenej

- Znajdujemy obraz bazowy
- Ładujemy zależności
- Kopiujemy kod źródłówy
- Ustawiamy punkt startowy kontenera
- Budujemy obraz z określoną wersją

Dockerfile:

```
FROM node:17.7.2-buster-slim  <-- Obraz bazowy dla aplikacji
WORKDIR /code  <-- Określenie katalogu bieżącego, do tego katalogu kopiujemy pliki
COPY package.json package-lock.json /code/  <-- kopiujemy najpierw listę zależności
RUN npm install   <-- instalujemy zależności
COPY src /code/src  <-- Kopiujemy kod źródłowy
EXPOSE 8080   <-- Aplikacja powinna działać na tym porcie
CMD [ "npm", "start" ]  // punkt startowy, polecenie uruchamiajace.
```

Przejdź do katalogu /03/03.06 a następnie zbuduj obraz za pomocą polecenia:

```
docker image build -t myapp:1.0 .
```

Uruchomienie kontenera

```
docker container run -d -p 8081:8080 --name myapp1 myapp:1.0
```

Zmodyfikuj zawartość pliku src/server.js a następnie zbuduj obraz ponownie, ale z nową wersją:

```
docker image build -t myapp:2.0 .
```

Uruchom drugi kontener na podstawie obrazu myapp:2.0

```
docker container run -d -p 8082:8080 --name myapp2 myapp:2.0
```

### 03.07 Warstwowa budowa obrazu

manifest - plik konfiguracyjny obrazu
FAT manifest - zbiór manifestów
Od wersji Dockera 1.10 warstwy są hashowane za pomocą algorytmu SHA256
W Docker Registry przechowywane jest archiwum obrazu powstałe po skompresowaniu plików danej warstwy
Po pobraniu `docker image pull` archiwum jest rozpakowane

```
docker image pull ubuntu

docker image inspect ubuntu

docker image history ubuntu
```

Narzędzie do przeglądania zawartości warstw obrazów

```
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock wagoodman/dive:latest ubuntu:latest
```

### 03.08 Multi-stage builds

Więcej niż jedna instrukcja `FROM`
Zapewnia spójność niezależnie od lokalizacji gdzie obraz jest budowany (CI/CD vs lokalnie )
Końcowy obraz jest lekki i nie posiada zbędnych plików / paczek powstałych podczas budowania wersji produkcyjnej

Dockerfile z lekcji:

```
# Stage 1
FROM node:17.7.2-buster-slim as development  // Określamy 1 etap Stage

ARG NODE_ENV=production    // Wartość tego zostanie przekazana do zmiennej środowiskowej ENV
ENV NODE_ENV $NODE_ENV

WORKDIR /code
COPY package.json package-lock.json /code/   // najpierw kopiujemy zależności
RUN npm ci
COPY src /code/src       // kopiujemy kod aplikacji
COPY public /code/public

CMD [ "npm", "start" ]   // Określamy pkt startowy tego Stage

# Stage 2 - builder
FROM development AS builder   // dziedziczy development z Stage 1 linijka 1, utwórz mi nowy stage o nazwie Builder

RUN npm run build   // Budujemy app

# Stage 3 - the production environment    // tworzenie finalnego obrazu
FROM nginx:1.21.6 AS production    // obraz uruchiamiany z poziomu serera nginx
COPY --from=builder /code/build /usr/share/nginx/html   // określamy z którego etapu chcemy skopiować (from=builder)

CMD ["nginx", "-g", "daemon off;"]    // pkt startowy kontenera
```

Lekcje w katalogu /03/03.08/reactjs a następnie uruchom polecenie:

```
docker image build -t myreact:latest .
```

Po zbudowaniu obrazu uruchom kontener

```
docker container run -d -p 8082:80 --name react-prod  myreact:latest
```

Budowanie tylko konkretnego stage'a, nadpisujemy prod i dajemy dev

```
docker image build --target development --build-arg  NODE_ENV=development -t react-dev:latest .
```

Uruchomienie wersji developerskiej

```
docker run -d -p 3000:3000 --name react-dev react-dev:latest
docker container ls
```

Podgląd konfiguracji obrazu

```
docker image inspect react-dev:latest
```

### 03.09 Dockerfile Tips & Tricks

#### COPY vs ADD

Przykład:

```
COPY [--chown=<user>:<group>] <src>... <dest>
COPY --chown=damian /src/app.js /home/app.js
```

ADD też kopiuje, ale ma dwie dodatkowe funkcje:

- Pozwala na kopiowanie plikó bezpośrednio z adresu URL
- Pozwala na rozpakowanie archiwum bezpośrednio do wskaanego katalogu

```
ADD http://example.com/file.png /home
ADD myfile.tar.gz /home/extracted_myfile
```

COPY jest rekomendowane do kopiowania plików
Jedyne zasadne zastosowanie instrukcji ADD : `ADD rootfs.tar.gz`
ADD może być niebezpieczne `ADD http://example.com/big.tar.xz /home`
Zamiast tego użyj RUN & curl `RUN curl -SL https://example.com/big.tar.xz | tar -xJC`

#### ENTRYPOINT vs CMD

Oba polecenia pozwalająokreślić na pkt startowy kontenera
Rekomendowana forma:

```
ENTYPOINT ["executable", "param1", "param2"]
CMD ["executable", "param1", "param2"]
```

Przykład:

```
ENTYPOINT ["nginx", "-g", "daemon off;"]
CMD ["nginx", "-g", "daemon off;"]
```

Połączenie ENTRYPOINT i CMD
ENTRYPOINT to funkcja, której argumenty wykonają się zawsze

```
ENTRYPOINT ["/bin/echo", "Docker"]
CMD ["Maestro"]
```

W przypadku CMD możemy to nadpisać

```
docker container run -it <image>
docker container run -it <image> Meastro - kurs online
```

`ENTRYPOINT` wykona się zawsze, `CMD` można "nadpisać"
Często stosuje sięobie instrukcje jednocześnie
Rekomendowana forma w obu przypadkach to "exec" `ENTRYPOINT ["executable", "param1", "param2"]`

#### ARG vs ENV

Instrukcja `ARG` jest dostępna z poziomu budowania obrazu, do momentu kiedy obraz nie zostanei zbudowany to można wykorzystać tą instrukcję.
W momencie gdy obraz jest zbudowany pojawia nam się instrukcja ENV

1. Często stosowane razem

```
ARG NODE_ENV=production
ENV NODE_ENV $NODE_ENV
```

2. Nie można bezpośrednio zmienić wartości `ENV` podczas budowania obrazu

```
docker image build --build-arg NODE_ENV=development ...
docker container run -e NODE_ENV=development // z parametrem -e można nadpisać wartość
```

#### FROM SCRATCH

Tak pwostają pliki bazowe, ale zawyczaj korzystamy w gotowych obrazów na dockerHub
Tworzymy obraz od zera https://hub.docker.com/_/scratch
Przykład Debiana:

```
FROM scratch
ADD rootfs.tar.xz /
CMD ["bash"]
```

### 03.10 Docker Linter

Automatyczne dbanie o jakość plików Dockerfile

Weryfikuje składnię plików Dockerfile i pomaga w ich optymalizacji
Można go uruchomić z poziomu Dockera
Możłiwość integracji w pipelinach CI / CD (Gitlab, Jenkins, Bitbucket)
Wtyczka do VSC: Hadolint
Instalacja dla WSLa:

```
sudo wget -O /usr/local/bin/hadolint https://github.com/hadolint/hadolint/releases/latest/download/hadolint-Linux-x86_64
sudo chmod +x /usr/local/bin/hadolint
hadolint --version
hadolint Dockerfile  <- Odpalamy plik do sprawdzenia czy zawiera błędy.
```

### 03.11 Najlepsze praktyki tworzenia Dockerfile

#### Kontener to pojedyncza usługa / aplikacja

Zasada SRP - Zalecane jest aby wewnątrz kontenera uruchomiony był jeden główny proces == jedna usługa /aplikacja
Czasami tworzone są procesy pochodne i to jest ok, przykłą z ngnxa uruchamia master process oraz worker process

#### Nie wrzucaj frontendu i backendu do tego samego obrazu / kontenera

(Zdarzają się wyjątki, ale na ogół łątwiej będzie tym zarządzać gdy kontener ma pojedynczą odpowiedizalność)

#### Dodaj do obrazu tylko to co niezbędne. Nie wrzucaj plików konfiguracyjnych czy plików README

Jak unikać zbędnych plików? `.dockerignore` wykluczamy to co nie chcemy aby znalazło sięw obrazie.

```
.git
node_modules
# ignore .md
!README*.md
README-secret.md
# ignore node_modules and all subdirections
node_modules/
# ignore Dockerfile & .dockerignore
Dockerfile
.dockerignore
```

#### Korzystaj z oficjalnych obrazów

Kiedy nie korzystać z oficjalnych obrazów?

1. Gdy oficjalny obraz niespełnia naszych wymagań
2. W oficjalnym obrazie występują krytyczne podatności

#### Tag "latest" to zło

Unikaj tagu : latest

- Tag :latest to anty-wzorzec w każdej dziedzinie oprogramowania
- W każdej chwili obraz pod tagiem :latest może się zmienić
- Nikt nie zagwarantuje Ci stabilności obrazu z tagiem :latest

#### Kopiuj pliki do obrazu z "głową"

COPY ./app <- zły przykład
ENTRYPOINT ["dotnet", "aspnetcore-app.dll"]

COPY /target/aspnetcore-app.dll /app <- dobry przykład
ENTRYPOINT ["dotnet", "aspnetcore-app.dll"]

#### Kolejność instrukcji w pliku Dockerfile ma duże znaczenie

Kod źródłówy powinniśmy na stosie dodawać jak najpóźniej bo on zmienia się najczęściej, inaczej tracimy możliwość skorzystania z mechanizmu cachowania

#### Załaduj zależności aplikacji najwcześniej jak to możliwe

np w node, najpierw kopiujemy plik package.json a potem uruchamiamy jego isntalacje

#### Ogranicz ilość warstw

Im mniej tym lepiej, ALE nie na siłę. Każda kolejna instrukcja RUN jakby dodaje kolejną warstwę.
Lepiej stosować jedną instrukcję RUN z && niż wiele osobnych, wtedy zamiast 5 warstw mamy jedną.

#### Dbaj o jakość swoich Dockerfile i korzystaj z linterów

np. Hadolint

#### Stosuj podejście multi-stage builds

## Moduł 04 : Komunikacja pomiędzy kontenerami

### 04.01 : Sieć typu bridge

#### Sterownik typu bridge

1. Domyślny typ sterownika seciowego
2. Najprostszy i najpopularniejszy typ sterownika sieciowego
3. Służy do komunikacji pomiędzy kontenerami uruchomionymi na tym samym hoście
4. Dostęp zewnętrzny nadawany jest poprzez mapowanie portów kontenera na port hosta . Przykład: -p 80:80

#### Domyślna sieć bridge

1. Każdy nowy kontener jest podpinany do sieci o nazwie bridge (dopóki tego nie wyspecjalizujemy)
2. Kontenery w domyślnej sieci bridge mogą komunikować się tylko po adresie IP (---link jest traktowany jako legacy)
3. Słabszy poziom izolacji -> wszystkie kontenery są w stanie ze sobą się komunikować
4. Aby odłączyć kontener od sieci należy go najpierw zatrzymać i ponownie uruchomić z nowymi ustawieniami sieci.

#### Tworzenie nowych sieci typu bridge

```
docker network create -d bridge mybridge
docker run -d --net mybridge --name db postgres:9.6
docker run -d --net mybridge -e DB=db -p 8080:3000 --name web mywebapp1.0
```

1. Każdy nowy kontener jest podpinany do sieci o nazwie bridge (dopóki tego nie wyspecjalizujemy)
2. KOntenery mogą komunikowasię po nazwch i aliasach
3. Lepszy poziom izolacji - tylko kontenery w tej samej sieci sąw stanie się ze sobą komunikować
4. Kontenery można podłączać i odłączać w dowolnym momencie (bez konieczności zatrzymywania)

Uruchom dwa kontenery o nazwie `ubuntu1` oraz `ubuntu2`

```
docker container run -itd --name ubuntu1 ubuntu bash
docker container run -itd --name ubuntu2 ubuntu bash
```

Sprawdź sieć kontenera i odczytaj jego adres IP

```
docker container inspect ubuntu1
```

Przejdź do terminala kontenera `ubuntu2`

```
docker container exec -it ubuntu2 bash
```

Zainstaluj ping i spróbuj skomunikować się z kontenerem ubuntu

```
apt-get update && apt-get install -y iputils-ping
ping <wcześniej odczytany adres IP kontenera ubuntu1>
ping ubuntu1
exit
```

Sprawdź listę sieci kontenerów:

```
docker network ls
```

Sprawdźmy jakie kontenery należą do sieci domyślnej bridge

```
docker network inspect bridge
```

#### Tworzenie nowej sieci

```
docker network create -d bridge custombridge
```

Komunikacja kontenerów w nowej sieci

```
docker container run -itd --name ubuntu3 --net custombridge ubuntu bash
docker container run -itd --name ubuntu4 --net custombridge ubuntu bash
docker container exec -it ubuntu4 bash
apt-get update && apt-get install -y iputils-ping
ping ubuntu3
ping ubuntu1
```

Podłączanie i odłączanie kontenerów

```
docker network connect custombridge ubuntu1
docker network disconnect custombridge ubuntu4
```

### 04.02 : Komunikacja pomiędzy kontenerami – WordPress i MySQL

Utwórz nową sieć typu bridge

```
docker network create -d bridge wp
```

Uruchom MySQL oraz WordPressa

```
docker run -d -p 3306:3306 --name db -e MYSQL_DATABASE=exampledb -e MYSQL_USER=exampleuser -e MYSQL_PASSWORD=examplepass -e MYSQL_RANDOM_ROOT_PASSWORD=1 --network=wp --restart=always mysql:5.7

docker run -d -p 8080:80 -e WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_USER=exampleuser -e WORDPRESS_DB_PASSWORD=examplepass -e WORDPRESS_DB_NAME=exampledb --network=wp --restart=always wordpress:latest
```

### 04.03 : Sterownik sieciowy HOST

- Interfejs sieciowy kontenera NIE jest odizolowany od interfejsu hosta (kontener współdzieli namespace hosta)
- Kontener nie otrzymuje adresu IP
- Kontener uruchomiony na porcie 80, z automatu będzie widoczny na porcie 80 adresu hosta
- Przekeirowanie portów nie dostępne (-p, --publish)
- Dostępny tylko dla Docker For Linux

```
docker run -d --name db -e MYSQL_DATABASE=exampledb -e MYSQL_USER=exampleuser -e MYSQL_PASSWORD=examplepass -e MYSQL_RANDOM_ROOT_PASSWORD=1 --network=host mysql:5.7

docker run -d --name wp -e WORDPRESS_DB_HOST=127.0.0.1:3306 -e WORDPRESS_DB_USER=exampleuser -e WORDPRESS_DB_PASSWORD=examplepass -e WORDPRESS_DB_NAME=exampledb --network=host wordpress:latest
```

### 04.04 : Sterownik sieciowy MACVLAN

- Łączy interfejs sieciowy kontenera bezpośrednio do interfejsu sieciowego hosta
- Kontenery otrzymują adresy IP z zewnętrznej podsieci
- Adres MAC widoczny dla innych urządzeń w podsieci
- KOntenery mogą siękomunikować z zewnętrznymi usługami
- Nie do końca działa dla większości dostawców chmuury
- Dostępny tylko dla Docker For Linux

Tworzenie sieci typu MACVLAN

```
docker network create -d macvlan \
  --subnet=ADRES_TWOJEJ_PODSIECI \
  --gateway=BRAMA_DOMYŚLNA \
  -o parent=eth0 \
  mymacvlan
```

Tworzenie kontenera:

```
docker container run -itd --name ubuntu1 --net mymacvlan ubuntu bash
```

### 04.05 : Podłączanie kontenerów do sieci innego kontenera

```
docker network create -d bridge mynet
docker container run -itd --name ubuntu1 --net mynet ubuntu bash
docker container run -it --name debian1 --network container:ubuntu1 debian bash
```

## Moduł 05 : Trwałóść danych kontenera

### 05.01 : Dane kontenera oraz zapisywanie zmian zachodzących w kontenerze

Warstwa do przechowywania danych

1. Każdy kontener posiada warstwę w ktorej przechowywane sązmiany zachodzące w kontenerze - writeable layer
2. Warstwa tworzy się automatycznie wraz z tworzeniem kontenera, usuwana po usunięciu kontenera
3. Jeżeli "nie chcący" usuniemy kontener - wszystkie dane przepadają

Docker commit - słyżu do zapisania stanu

- Zapisujemy stan kontenera jako obraz
- Wszystkie zmiany w kontenerze będą dostepne w nowo utworzonym obrazie
- Składnia: `docker container commit <container_name> <image_name:tag>`
- Przykład: `docker container commit my_ubuntu myubuntu:1.0`

```
docker container run -d -p 8080:80 --name mynginx nginx:latest
```

Zmodyfikuj zawartość pliku /usr/share/nginx/html/index.html

```
docker container exec -it mynginx bash
apt-get update && apt-get install -y nano
nano /usr/share/nginx/html/index.html
exit
curl localhost:8080
```

Zapisywanie zmian - docker commit

```
docker container commit mynginx mynginx:1.0
docker container run -d -p 8081:80 --name mynginx1 mynginx:1.0
```

### 05.02 : Volumeny

Volumeny:

1. Zapewniają trwałość danych po usunięciu kontenera
2. Volumenty są tworzone i w pełni zarządzane przez Dockera
3. Dane są odizolowane od systemu hosta
4. Dwa typy volumenów
   - Domyślne (w Dockerze) `VOLUME /var/lib/postgresql/data`
   - Nazwane `docker volume create <volume_name>`

Uruchom PostgreSQL z domyślnym volumenem

```
docker container run -d -p 5432:5432 -e POSTGRES_PASSWORD=test123 --name db postgres:9.6
```

Uruchom PostgreSQL z nazwanym volumenem

```
docker container run -d -p 5433:5432 -e POSTGRES_PASSWORD=test12 --name db1 -v db1-data:/var/lib/postgresql/data postgres:9.6
```

`docker volume inspect db1-data`
`docker container rm db1 --force` <- zatryzmanie i usunięcie kontenera
Sprawdź różnicę

```
docker volume ls
```

Po usunięciu kontenera volume (dane) powinien zostać, możemy wyczyścić dane poleceniem:

```
docker volume prune
```

### 05.03 : Współdzielenie volmenów

Współdzielenie danych

- Czasami istnieje potrzeba współdzielenia danych pomiędzy kontenerami, ale należy podchodzićdo tego ostrożnie.
- Jedna z opcji to współdzielenie volumenów
  a. Pełne prawa (modyfikacja)
  b. Dostęp tylko do odczytu

```
docker volume create myvolume1
docker run -it --name=u1 -v myvolume1:/myvolume1 ubuntu
docker run -it --name=u2 --volumes-from u1 ubuntu   // będzie miałprawo odczytu i modyfikowania z volumenu u1
```

Współdzielenie danych w trybie readonly

```
docker run -it --name=u3 --volumes-from u1:ro ubuntu    // :ro -> read only
```

### 05.04: Bind Mounts

Podmontowywanie katalogów/plików na hoście do katalogów/plików wewnątrz kontenera

Korzyść z tego jest taka, że każda zmiana plikó na hoście od razu jest widoczna w kontenerze.

1. Alternatywa dla volumenów
2. Fizyczny plik lub katalog na dysku hosta jest podmontowany do kontenera
3. Podmontowując katalog do kontenera, mamy możliwość zarządzania nim: modyfikowanie, usuwanie, tworzenia plików (ryzyko bezpieczeńśtwa)
4. Brak możliwości tworzenia Bind Mounts z poziomu Docker CLI

PostgreSQL

```
sudo mkdir -p /var/db/pgdata <- tworzymy katalog
// Za pomocą parametru -v wskazujemy ten katalog czyli katalog lokalny : katalog znajdujacy się w kontenerze
docker container run -d -p 5432:5432 --name db -e POSTGRES_PASSWORD=test12 -v /var/db/pgdata:/var/lib/postgresql/data postgres:9.6
```

Podmontowanie plików i edycja z poziomu hosta
Sklonuj repozytorium i przejdź do katalogu first_app_in_docker

```
git clone https://github.com/dnaprawa/first_app_in_docker
cd ~/first_app_in_docker
```

Uruchomienie kontenera:

```
docker container run -d -p 8080:80 --name first_app_container --mount type=bind,source="$(pwd)",target=/usr/share/nginx/html first_app_in_docker:1.0
```

Zamiana plików

```
cp index_changed.html index.html
```

05.05: Zadanie:

1. Stworzyć volumen mysqldata
2. Uruchomić bazę danych MySQL w wersji 5.6.47
   a. Przekazać zmienną środowiskową -e MYSQL_RANDOM_ROOT_PASSWORD=yes
   b. Odczytać hasło ROOT’a z logów (GENERATED ROOT PASSWORD)
   c. Podmontować volumen mysqldata do katalogu /var/lib/mysql kontenera
3. Usunąć kontener i uruchomić nowy, ale w wersji 5.6.48 + korzystając z volumena mysqldata
4. Upewnij się że kontener działa (sprawdź logi)
5. Wykonać dump bazy danych (docker exec + mysqldump)

-d // działanie w tle
-v // podmontowujemy volumen

```
docker volume create mysqldata
docker image pull mysql:5.6.47
docker run -d --name db -e MYSQL_RANDOM_ROOT_PASSWORD=yes -v mysqldata:/var/lib/mysql mysql:5.6.47
docker container logs db  // szukamy hasła w logach i kopiujemy z fragmentu (GENERATE ROOT PASSWORD:)

docker container rm db --force
docker volume ls <- sprawdźmy czy volume istnieje

docker run -d --name db2 -e MYSQL_RANDOM_ROOT_PASSWORD=yes -v  mysqldata:/var/lib/mysql mysql:5.6.48
docker container logs db2
```

WAŻNE: Pamiętaj, by odczytać hasło do bazy z logów i ustawić jego wartość do zmiennej środowiskowej MYSQL_ROOT_PASSWORD.
Dump bazy danych:

```
docker container exec db2 sh -c 'exec mysqldump --all-databases -u root -p "$MYSQL_ROOT_PASSWORD"' > /<YOUR_PATH_HERE>/all-db.sql
```

### 06.01: Wprowadzenie do docker-compose

1. Konfiguracja zależności między kontenerami
2. Zapisuje naszą konfigurację do pliku
3. Za pomocą jednego polecenia możemy uruchomić cały projekt
   a. YAML - typ pliku
   b. COMPOSE - (compose format) - składnia, w której definiujemy zachowanie Kontenerów, Seici, Volumenów
   c. Docker Compose - narzędzie CLI do uruchamiania kontenerów na podstawie pliku YAML (wbudowany w Docker Desktop, osobne narzędzie dla Docker For Linux: trzeba doinstalować)

   Docker Compose

   1. Compose - wersje 1,2, 2.1, 3 ... 3.8 https://docs.docker.com/compose/compose-file/
   2. Kontenery mogą sięze sobą komunikować
      a. podczas uruchomienia tworzona jest nowa sieć typu bridge
      b. Wszystkie kontenery automatycznie dodawane są do jednej sieci
   3. Compose kompatybilny z Docker Swarm
   4. domyślna nazwa pliku YAML to `docker-compose.yml`, ale może być zmienionwa: `docker-compose -f docker-compose.dev.yml`

opis docker-compose

```
version: '3.9'

services:
  servicename: # WYMAGANE: dowolna nazwa ustalona przez Ciebie
    image: # OPCJONALNE: może być pominięty gdy korzystamy z "build"
    container_name: # OPCJONALNE: wymuszamy nazwę kontenera.
    command: # OPCJONALNE: możemy zastąpić CMD z Dockerfile
    ports: # OPCJONALNE: mapujemy porty - przekeiorwnaie ruchu z kontenera na host
    environment: # OPCJONALNE: zmienne środowiskowe. przykład: "-e MY_SQL_PASSWORD=1"
    volumes: # OPCJONALNE: odpowiednik "-v" w docker container run, podjemy referncje do wcześniej zdefiniowanego volumenu
  servicename-2:
    image: # OPCJONALNE: może być pominięty gdy korzystamy z "build"
    restart: # OPCJONALNE: określamy politykę restartów

volumes: # OPCJONALNE // muszą być zdefuniowane także osobno

networks: # OPCJONALNE  jeśli nie stworzymy sieci to docker samoistnie to zrobi
```

Przykład:

```
version: "3.7"

# odpowiednik w CLI
# docker volume create nginx-volume
# docker container run -p 8080:80 \
# -v nginx-volume:/usr/share/nginx/html \
# --name nginx1 nginx:1.17

services:
  mynginx:
    image: nginx:1.17
    container_name: nginx1
    ports:
      - 8080:80
    volumes:
      - nginx-volume:/usr/share/nginx/html   # podłączamy ścieżkę gdzie mają być zapisywane pliki, nazwa jak odgórna nginx-volume

volumes:
  nginx-volume:
    name: nginx-volume
```

docker-compose.yml

```
version: '3.7'

services:

  mywordpress:
    image: wordpress
    restart: always
    ports:
      - 9012:80
    environment:
      WORDPRESS_DB_HOST: db:3306   // nazwę bazy podajemy z kontenera niżej: czyli 'db:'
      WORDPRESS_DB_USER: db_user
      WORDPRESS_DB_PASSWORD: db_password
      WORDPRESS_DB_NAME: Wordpress
    volumes:
      - wordpress-volume:/var/www/html
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: Wordpress
      MYSQL_USER: db_user
      MYSQL_PASSWORD: db_password
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db-vol:/var/lib/mysql

volumes:
  wordpress-volume:
  db-vol:

// Odzwierciedlenie w CLI tego co powyzej.
# docker network create -d bridge wp   // tworzymy nową sieć

# docker run -d --name db -e MYSQL_DATABASE=Wordpress  \
# -e MYSQL_USER=db_user -e MYSQL_PASSWORD=db_password -e MYSQL_RANDOM_ROOT_PASSWORD=1
# --network=wp --restart=always mysql:5.7

# docker run -d -p 9012:80 -e WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_USER=db_user \
# -e WORDPRESS_DB_PASSWORD=db_password -e WORDPRESS_DB_NAME=Wordpress --network=wp \
# --restart=always wordpress:latest
```

Uruchomienie za pomocą docker-compose

```
docker-compose up <- Stara komenda
docker compose up
docker compose up -d // Uruchamiamy w tle
docker compose down // powoduje zatrzymanie kontenerów, nie usuwa volumenów
docker volume ls  // pokazuje zapisane volumeny
docker compose down -v  // usuwa kontenery wraz z ich volumenami
docker compose top  // pokazuje co się dzieje wewnatrz naszych kontenerów
docker compose --help  // poakzuje wszystkie dostępne komendy
docker compose stop db  // zatryzmanie konkrentego kontenera
docker compose start db  // ponowne uruchomienie zastopowanego kontenera
```

### 06.02: Automatyczne budowanie obrazów

1. Docker-compose może automatycznie budować obrazy https://docs.docker.com/compose/compose-file/#build
2. docker-compose up - zrobi to automatycznie, jeżeli obraz został znaleziony lokalnie
3. Jeżeli chcemy wymusić przebudowanie obrazu:

- `docker-compose build` albo `docker-compose up --build`

przykład kontenera dla aplikacji ReactJs

```
version: "3.7"

services:
  ui: #React UI
    image: projectz-ui
    build:
      context: ./ui
      args:
        NODE_ENV: production
        REACT_APP_SERVICE_HOST: http://localhost:8080
    ports:
      - "3000:80"

  services: #NodeJS backend
    image: projectz-svc
    build:
      context: ./services
      args:
        NODE_ENV: production
        PORT: "80"
    ports:
      - "8080:80"
```

Dockerfile dla kontenera ui:

```
FROM node:17.7.2-buster-slim as build

ARG NODE_ENV=production
ENV NODE_ENV $NODE_ENV

ARG REACT_APP_SERVICE_HOST=http://localhost:8080
ENV REACT_APP_SERVICE_HOST $REACT_APP_SERVICE_HOST

WORKDIR /code

COPY package.json package-lock.json ./
RUN npm ci

COPY . /code
RUN npm run build

FROM nginx:1.21.6

COPY --from=build /code/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

Dockerfile dla kontenera node backend:

```
FROM node:17.7.2-buster-slim

ARG NODE_ENV=production
ENV NODE_ENV $NODE_ENV

WORKDIR /app

ARG PORT=80
ENV PORT $PORT

COPY package.json package-lock.json ./

RUN npm ci

COPY . /app

CMD ["node", "src/server.js"]
```

Polecenie `docker-compose config` wyświtla konfgurację naszych usług

uruchamianie i budowanie:

```
docker-compose build
docker-compose config
docker-compose up -d
```

### 06.03: Zmienne środowiskowe

1. W pliku .env możemy współdzielić zmienne a ich zawartość zostanie podstawiona w docker-compose.yml
2. Przykład:
   Wpis `DB_PASSWORD=test123` pliku .env
   Wpis `${DB_PASSWORD}` w pliku docker-compose.yml
3. Pliki .env służą tylko do podstawiania wartości w docker-compose.yml i nie mają nic współnego z ENV w Dockerfile oraz "-e" w terminalu

Plik .env
1. Zmienne środowiskowe hosta mają wyższy priorytet niż wpisy w pliku .env
2. Zmienne środowiskowe w terminalu mają najwyższy priorytet (nadpisują zarówno .env jak i zmienne środowiskoe hosta)

Przykład:

Plik docker-cpomose.yml
```
version: "3.7"

services:
  mywordpress:
    image: wordpress
    restart: always
    ports:
      - ${WP_PORT}:80
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: ${DB_USER}
      WORDPRESS_DB_PASSWORD: ${DB_PASSWORD}
      WORDPRESS_DB_NAME: ${DB_NAME}
    volumes:
      - wordpress-volume:/var/www/html
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: ${DB_NAME}
      MYSQL_USER: ${DB_USER}
      MYSQL_PASSWORD: ${DB_PASSWORD}
      MYSQL_RANDOM_ROOT_PASSWORD: "1"
    volumes:
      - db-vol:/var/lib/mysql

volumes:
  wordpress-volume:
  db-vol:
```

Plik .env
```
DB_USER=myuser
DB_PASSWORD=mypassword
DB_NAME=mywordpress
WP_PORT=9099
```

Komenda zwracajaca całą konfigurację:
```
docker-compose config
```
Mogę zmienić, nadpisać wartość zmiennej poprzez cli:
```
Windows Powershell: $Env:DB_NAME="FromPowershellDbName"
Linux: export DB_NAME=FromPowershellDbName
```

Zmienne środowiskowe - env_file
1. Dodatkowy wpis `env_file: my_env_file` w pliku YAML
2. Wpliku `my_env_file` wpisy według schematu klucz=wartość
3. Przekazywanie zmiennych bezpośrednio do kontenera
4. Srawdza się w pracy w zespole
   - `my_env_file` może być dodany do ignorowania plików w kontroli wersji
   - każda osoba może mieć swoje ustawienia: portów, nazw itp.

Plik docker-compose z odwołaniem do indywidualnego pliku .env

```
version: "3.7"

services:
  mywordpress:
    image: wordpress
    restart: always
    ports:
      - 9012:80
    env_file: wordpress_env_file
    volumes:
      - wordpress-volume:/var/www/html
  db:
    image: mysql:5.7
    restart: always
    env_file: mysql_env_file
    volumes:
      - db-vol:/var/lib/mysql

volumes:
  wordpress-volume:
  db-vol:
```
Zmienne środowiskowe wewnątrz pliku mysql_env_file:
```
MYSQL_DATABASE=mywordpress
MYSQL_USER=myuser
MYSQL_PASSWORD=mypassword
MYSQL_RANDOM_ROOT_PASSWORD=1
```
Aby zerknąć na konfigurację pliku z niestandardową nazwą i uruchomić:
```
docker-compose -f .\docker-compose.envfile.yml config
docker-compose -f .\docker-compose.envfile.yml up -d
```

### 06.04: Wiele instancji na podstawie tego samego pliku YAML

* Problem: mamy tylko jeden serwer, a chcemy uruchomić więcej niż jedną instancję aplikacji z poziomu docker-compose
  - jedna instancja "developerska"
  - Druga instancja "dla testerów manualnych"
  - Trzecia instancja "dla klienta"
* Co nas blokuje?
  - nazwy automatycznie tworoznych kontenerów, volumenów, sieci
  - konfikt otwartych portów
* Rozwiązanie:
  - mechanizm podstawiania ${VARIABLE}
  - określenia nazwy projektu podczas uruchamiania `docker-compose -p <project_name>`
* Nie ma problemu z CI/CD (określamy tylko zmienną środowiskową)
* Mamy uruchomione kilka instancji na jednym serwerze / wirtualnej maszynie

```
version: '3.7'

services:
  mywordpress:
    image: wordpress
    restart: always
    ports:
      - ${WP_PORT}:80
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: db_user
      WORDPRESS_DB_PASSWORD: db_password
      WORDPRESS_DB_NAME: Wordpress
    volumes:
      - wordpress-volume:/var/www/html
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: Wordpress
      MYSQL_USER: db_user
      MYSQL_PASSWORD: db_password
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db-vol:/var/lib/mysql

volumes:
  wordpress-volume:
  db-vol:
```

Dodaj zmienną środowiskową o nazwie WP_PORT
```
Windows Powershell: $Env:WP_PORT="9091"
Linux: export WP_PORT=9091

docker-compose -p dev up -d
```
Drugi projekt:
```
export WP_PORT=9092
docker-compose -p staging up -d
```
Zatrzymanie kontenerów:
```
docker-compose -p staging down
```
### 06.05 Łączenie plików docker-compose.yml

PROBLEM:
Chcemy mieć różną konfigurację dla różnych środowisk.
Przykład 1: na potrzeby dev chcę otworzyć porty kontenera DB, a w wersji prod już nie
Przykład 2: w wersji Prod, chcę aby kontener był automatycznie uruchamiany po restarcie Docker Engine

Rozwiazanie:
- Kilka plików YAML, które są łączone podczas `docker-compose up`
- Plik główny `docker-compose.yml` - określa cechy wspólne np. obraz
- Pliki `docker-compose.dev.yml` - oraz `docker-compose.prod.yml` zawierają dodatkową konfigurację
- Wywołujemy zgodnie z kolejnością:

```
docker-compose -f docker-compose.yml -f docker-compose.dev.yml up
```

```
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up
```

DEV:
```
version: "3.7"

services:
  vault:
    ports:
      - 8200:8200
    environment:
      - VAULT_ADDR=http://127.0.0.1:8200
      - VAULT_DEV_ROOT_TOKEN_ID=00000000-0000-0000-0000-000000000000
    volumes:
      - vaultdata:/vault

volumes:
  vaultdata:
```

PROD:
```
version: "3.7"

services:
  vault:
    environment:
      - VAULT_ADDR=http://127.0.0.1:8200
    volumes:
      - "/var/vault/logs:/vault/logs"
      - "/var/vault/file:/vault/file"
      - "/var/vault/config:/vault/config"
    entrypoint: vault server -config=vault.json
```

docker-compose.yml
```
version: '3.7'

services:
  vault:
    image: vault:1.3.2
  
```

### 06.05A Kolejność startu kontenerów oraz czekanie na uruchomienie usługi
PROBLEM: Chcemy określić kolejność startu kontenerów

Przykład 1: chce, by najpierw została uruchomiona baza danych, a dopiero później moja apka, która łączy sięz tą bazą
Przykład 2: chcę, by najpierw został uruchomiony backend, a dopiero później frontend

Jak działa `depends_on` ?
- Określa kolejność startu kontenera
- Możemy określić wiele zależności
  depends_on
   - db
   - elasticsearch

! Weryfikowany jest tylko stan procesu, a nie stan usługi, czasem inicjalizacja pojektu trwa dłużej.

Przykład: Jeżeli proces bazy danych wystartuje, to nie oznacza to, że jest ona gotowa na przyjmowanie połączeń
 - Kontener z bazą danych będzie mieć stan `Up`, a sama baza nie będzie gotowa na przyjmowanie połączeń.

Jak wymusić zaczekanie na PEŁNĄ inicjalizację usługi
Opcja 1:
- Wraz z `depends_on`, ustawić `restart:always`
- jeżeli usługa (np. baza danych) nie będzie gotowa na przyjmowanie połączeń, kontener z aplikacją zostanie zatrzymany, a następnie będzie uruchamiancy ponownie, do mementu aż uda się nawiązać połączenie.

Opcha 2:
- Dodanie healthcheck do usługi na którą 'czekamy'
- Zastosowanie `depends_on` wraz z `condition: service_healthy`

Opcja 3:
- jeżeli to możłiwe, dodajemy obsługę błędów połączenia z zewnętrznymi usługami w aplikacji

docker-compose-service_healthly.yml:
```
docker-compose -f docker-compose-service_healthly.yml up -d
```

```
version: '3.7'

services:
  mywordpress:
    image: wordpress:5.7
    restart: always
    ports:
      - 9012:80
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: db_user
      WORDPRESS_DB_PASSWORD: db_password
      WORDPRESS_DB_NAME: Wordpress
    depends_on:
      db:
         condition: service_healthy
    volumes:
      - wordpress-volume:/var/www/html
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: Wordpress
      MYSQL_USER: db_user
      MYSQL_PASSWORD: db_password
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "127.0.0.1", "--silent"]
      interval: 10s
      retries: 5
      start_period: 30s
    volumes:
      - db-vol:/var/lib/mysql

volumes:
  wordpress-volume:
  db-vol:

```

### 06.05B: Wspólna część konifguracji dla wszystkich usług zdefiniowanych w docker-compose

Wspólna część konifguracji dla wielu (mikro) serwisów
- Problem: mam kilka (mikro) serwisów, które mają tę samą konfigurację

Przykład 1: Każdy (mikro) serwis potrzebuje informację o adresie Vaulta (przekazujemy ją przez ENV)
Przykład 2: Dla każdej z usług zdefiniowanych w docker.compose.yml chcę ustawić te same zależności (deppends_on)

Mamy zmienne środowiskowe i mozemy wstrzykonąćza pomocąwskaźnika do różnych mikro serwisów.
```
version: "3.8"
x-default-environment: &default-environment
  Vault__VaultAddress: http://vault:8200"
  Vault__AuthenticationToken: 00000000-0000-0000-0000-000000000000

x-connector-depends-on: &connector-depends-on
  - postgres
  - vault
  - vault-initializer
  - elasticsearch

services:
  api:
    build:
      context: .
      dockerfile: Dockerfile.api
    container_name: api
    ports:
      - "${API_PORT:-8080}:5000"
    restart: always
    environment: *default-environment
    depends_on:
      - postgres
      - vault
      - vault-initializer
      - elasticsearch

  stash:
    build:
      context: .
      dockerfile: Dockerfile.stash
    container_name: stash
    ports:
      - "${STASH_PORT:-60001}:5000"
    restart: always
    environment: *default-environment
    depends_on:
      - postgres
      - vault
      - vault-initializer
      - elasticsearch

  snow-incident:
    build:
      context: .
      dockerfile: Dockerfile.snow
    container_name: snow-incident
    environment:
      TableName: Incident
      <<: *default-environment
    restart: always
    depends_on: *connector-depends-on

  snow-incidentsla:
    build:
      context: .
      dockerfile: Dockerfile.snow
    container_name: snow-incidentsla
    environment:
      TableName: IncidentSLA
      <<: *default-environment
    restart: always
    depends_on: *connector-depends-on

  postgres:
    container_name: postgres
    image: container-registry:5089/postgres:10
    ports:
      - "${POSTGRES_PORT:-5433}:5432"
    environment:
      POSTGRES_PASSWORD: Vue@1kw8
      POSTGRES_USER: postgres
    volumes:
      - pgdata:/var/lib/postgresql/data
    restart: unless-stopped

  vault:
    image: container-registry:5089/vault:1.3.2
    container_name: vault
    ports:
      - "${VAULT_PORT:-8200}:8200"
    cap_add:
      - IPC_LOCK
    environment:
      - VAULT_ADDR=http://127.0.0.1:8200
      - VAULT_DEV_ROOT_TOKEN_ID=00000000-0000-0000-0000-000000000000
    volumes:
      - vaultdata:/vault
    restart: unless-stopped

  vault-initializer:
    build:
      context: .
      dockerfile: Dockerfile.vaultinit
    container_name: vault-initializer
    cap_add:
      - IPC_LOCK
    environment:
      - VAULT_ADDR=http://vault:8200
    depends_on:
      - "vault"

  elasticsearch:
    image: container-registry:5089/elasticsearch:7.5.2
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - http.port=9200
      - http.cors.enabled=true
      - http.cors.allow-origin=http://localhost:1358,http://127.0.0.1:1358,http://dejavu-dev:1358
      - http.cors.allow-headers=X-Requested-With,X-Auth-Token,Content-Type,Content-Length,Authorization
      - http.cors.allow-credentials=true
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ports:
      - "${ELASTIC_PORT_1:-9200}:9200"
      - "${ELASTIC_PORT_2:-9300}:9300"
    volumes:
      - elasticdata:/usr/share/elasticsearch/data
    restart: unless-stopped

  dejavu:
    image: container-registry:5089/dejavu:3.3.0
    container_name: dejavu
    ports:
      - "${DEJA_VU_PORT:-1358}:1358"
    links:
      - elasticsearch
    restart: unless-stopped

volumes:
  pgdata:
  vaultdata:
  elasticdata:

```

### 06.06: Zewnętrzna sieć
Problem: Może zajśćpotrzeba rozdzielenia procesu uruchamiania poszczegółnych usług/komponentów systemu
- rozdzielenia usług/kontenerów systemu na osobne pliki docker-compose
- pojedyncze kontenery mogące komunikować się z innymi usługami (np. do debugowania)

Tworzenie sieci i uruchamianie kontenera:
```
docker network create -d bridge --attachable myproject-external-network
docker-compose -f docker-compose.vault.yml up -d
```

```
docker container run -it --net myproject-external-network ubuntu bash
apt-get update && apt-get install -y iputils-ping
ping vault
```



```
version: "3.7"

services:
  vault:
    image: vault:1.3.2
    ports:
      - 8200:8200
    environment:
      - VAULT_ADDR=http://127.0.0.1:8200
      - VAULT_DEV_ROOT_TOKEN_ID=00000000-0000-0000-0000-000000000000
    volumes:
      - vaultdata:/vault

volumes:
  vaultdata:

networks:
  default:
    external: true
    name: myproject-external-network
```


### 06.07: Tworzenie docker-compose.yml na podstawie docker run
Transformacja docker run na YAML

PROBLEM: mam przygotowanych kilka poleceń Docker CLI, chce je przenieść na docker-compose
Narzędzie composerize
- Open-source https://github.com/magicmark/composerize
- Transformuje polecenia na YAML
- Instalacja za pomocą NPM

```
node --version
npm install composerize -g
```
```
composerize docker run -p 80:80 --restart always --name composerize-nginx nginx:1.17
```
```
composerize docker run -d -p 3306:3306 --name db \
    -e MYSQL_DATABASE=exampledb \
    -e MYSQL_USER=exampleuser \
    -e MYSQL_PASSWORD=examplepass
    -e MYSQL_RANDOM_ROOT_PASSWORD=1 \
    --network=wp --restart=always mysql:5.7

composerize docker run -d -p 8080:80 \
    -e WORDPRESS_DB_HOST=db:3306 \
    -e WORDPRESS_DB_USER=exampleuser \
    -e WORDPRESS_DB_PASSWORD=examplepass \
    -e WORDPRESS_DB_NAME=exampledb \
    --network=wp --restart=always \
    wordpress:latest
```

### 06.08: Przykłady docker-compose

https://github.com/docker/awesome-compose