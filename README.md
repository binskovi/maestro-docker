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
























