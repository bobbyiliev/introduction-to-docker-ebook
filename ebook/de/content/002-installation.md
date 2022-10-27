# Docker installieren

Heutzutage können Sie Docker unter Windows, Mac und natürlich Linux ausführen. Ich werde nur durch die Docker-Installation für Linux gehen, da dies mein Betriebssystem der Wahl ist.

Ich werde einen **Ubuntu-Server auf DigitalOcean** bereitstellen, Sie können also gerne weitermachen und dasselbe tun:

[Create a Droplet DigitalOcean](https://docs.digitalocean.com/products/droplets/how-to/create)

Sobald Ihr Server hochgefahren ist und läuft, verbinden Sie sich per SSH mit dem Droplet und folgen Sie den Anweisungen!

Wenn Sie nicht sicher sind, wie man SSH benutzt, können Sie die Schritte hier nachlesen:

[https://www.digitalocean.com/docs/droplets/how-to/connect-with-ssh/](https://www.digitalocean.com/docs/droplets/how-to/connect-with-ssh/)

Die Installation ist wirklich einfach, Sie können einfach den folgenden Befehl ausführen, er sollte auf allen gängigen **Linux**-Distributionen funktionieren:

```
wget -qO- https://get.docker.com | sh
```

Dieser Befehl erledigt alles, was für die Installation von **Docker auf Ihrem Linux-Rechner** erforderlich ist.

Danach richten Sie Docker so ein, dass Sie es mit dem folgenden Befehl als Nicht-Root-Benutzer ausführen können:

```
sudo usermod -aG docker ${USER}
```

Um **Docker** zu testen, führen Sie das Folgende aus:

```
Docker-Version
```

Um weitere Informationen über Ihre Docker-Engine zu erhalten, können Sie den folgenden Befehl ausführen:

```
docker info
```

Mit dem Befehl ``docker info`` können wir sehen, wie viele Container in Betrieb sind und einige Server-Informationen erhalten.

Die Ausgabe des Befehls "docker version" sollte in etwa so aussehen:

![docker version output](https://imgur.com/tuGSemS.png)

Falls Sie Docker auf Ihrem Windows-PC oder Mac installieren möchten, können Sie die offizielle Docker-Dokumentation hier besuchen:

[https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/)

und:

[https://docs.docker.com/docker-for-mac/install/](https://docs.docker.com/docker-for-mac/install/)

Das war's eigentlich schon! Jetzt haben Sie Docker auf Ihrem Rechner laufen!

Jetzt sind wir bereit, mit Containern zu arbeiten! Wir ziehen ein **Docker-Image** aus dem **DockerHub**, starten einen Container, stoppen ihn, zerstören ihn und mehr!