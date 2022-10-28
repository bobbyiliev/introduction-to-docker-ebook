# Mit Docker containern arbeiten

Sobald Sie Ihr **Ubuntu-Droplet** bereit haben, können Sie per ssh auf den Server zugreifen und mitmachen!

Starten wir also unseren ersten Docker-Container! Dazu müssen Sie nur den folgenden Befehl ausführen:

```
docker run hello-world
```

Sie werden die folgende Ausgabe erhalten:

![](https://cdn.devdojo.com/posts/images/April2020/docker-run.png)

Wir haben gerade einen Container basierend auf dem **hello-world Docker Image** ausgeführt. Da wir das Image nicht lokal hatten, hat docker das Image vom **[DockerHub](https://hub.docker.com)** gezogen und dann dieses Image verwendet, um den Container auszuführen. 
Alles, was passierte, war: Der **Container lief**, gab etwas Text auf dem Bildschirm aus und beendete sich dann.

Um einige Informationen über die laufenden und gestoppten Container zu sehen, führen Sie aus:

```
docker ps -a
```

Sie werden die folgenden Informationen für Ihren **hello-world Container** sehen, den Sie gerade ausgeführt haben:

```
root@docker:~# docker ps -a
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
62d360207d08 hello-world "/hello" vor 5 Minuten beendet (0) vor 5 Minuten focused_cartwright
```

Um die lokal verfügbaren Docker-Images auf Ihrem Host aufzulisten, führen Sie den folgenden Befehl aus:

```
Docker-Images
```


## Ein Image aus dem Docker Hub pullen

Lassen wir einen nützlicheren Container laufen, z. B. einen **Apache**-Container. 

Zuerst können wir das Image aus dem Docker Hub mit dem **docker pull Befehl** ziehen:

```
docker pull webdevops/php-apache
```

Sie werden die folgende Ausgabe sehen:

![](https://cdn.devdojo.com/posts/images/April2020/docker-pull-php-apache.png)

Dann können wir die Image-ID mit dem Befehl docker images erhalten:

```
docker images
```

Die Ausgabe würde wie folgt aussehen:

![](https://cdn.devdojo.com/posts/images/April2020/docker-images.png)

> Beachten Sie, dass Sie das Image nicht unbedingt abrufen müssen, dies dient nur zu Demonstrationszwecken. Wenn der Befehl "docker run" ausgeführt wird und das Image nicht lokal verfügbar ist, wird es automatisch von Docker Hub gezogen.

Danach können wir den Befehl **docker run** verwenden, um einen neuen Container zu starten:

```
docker run -d -p 80:80 IMAGE_ID
```

Kurzer Überblick über die Argumente, die ich verwendet habe:

* `-d`: gibt an, dass ich den Container im Hintergrund laufen lassen will. Auf diese Weise würde der Container weiterlaufen, wenn man sein Terminal schließt.

* `-p 80:80`: das bedeutet, dass der Verkehr vom Host auf Port 80 an den Container weitergeleitet wird. Auf diese Weise können Sie direkt über Ihren Browser auf die Apache-Instanz zugreifen, die in Ihrem Docker-Container läuft.

Mit dem Befehl docker info können wir nun sehen, dass wir 1 laufenden Container haben. 

Und mit dem Befehl `docker ps` können wir einige nützliche Informationen über den Container sehen, wie die Container-ID, wann der Container gestartet wurde, etc:

```
root@docker:~# docker ps
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
7dd1d512b50e fd4f7e58ef4b "/entrypoint supervi..."   Vor etwa einer Minute Up Vor etwa einer Minute 443/tcp, 0.0.0.0:80->80/tcp, 9000/tcp pedantic_murdock
```

## Einen Container stoppen oder neustarten

Dann können Sie den laufenden Container mit dem Befehl docker stop, gefolgt von der Container-ID, anhalten:

```
docker stop CONTAINER_ID
```

Falls nötig, können Sie den Container wieder starten:

```
docker start CONTAINER_ID
```

Um den Container neu zu starten, können Sie folgendes verwenden:

```
docker restart CONTAINER_ID
```


## Auf einen laufenden Container zugreifen

Wenn Sie sich mit dem Container verbinden und einige Befehle innerhalb des Containers ausführen möchten, verwenden Sie den Befehl `docker exec`:

```
docker exec -it CONTAINER_ID /bin/bash 
```

Auf diese Weise gelangen Sie zu einer **Bash-Shell** im Container und können einige Befehle innerhalb des Containers selbst ausführen. 

Um die interaktive Shell zu verlassen, drücken Sie die Tastenkombination "STRG+PQ". Auf diese Weise halten Sie den Container nicht an, sondern trennen ihn nur von der interaktiven Shell.

![](https://cdn.devdojo.com/posts/images/April2020/docker-exec-stop1.png)


## Einen Container löschen

Um den Container zu löschen, vergewissern Sie sich zunächst, dass der Container nicht läuft, und führen Sie ihn dann aus:

```
docker rm CONTAINER_ID
```

Wenn Sie den Container und das Image insgesamt löschen möchten, führen Sie einfach folgendes aus:

```
docker rmi IMAGE_ID
```


Damit wissen Sie nun, wie Sie Docker-Images aus dem **Docker Hub** pullen, ausführen, stoppen, starten und sogar an Docker-Container anhängen können!

Wir sind bereit zu lernen, wie man mit **Docker-Images** arbeitet!
