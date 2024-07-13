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