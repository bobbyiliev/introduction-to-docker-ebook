# Einführung zu Docker

Es ist sehr wahrscheinlich, dass **Docker** und Container auf die eine oder andere Weise Teil Ihrer IT-Karriere sein werden.

Nach der Lektüre dieses eBooks werden Sie ein gutes Verständnis der folgenden Punkte haben:

* Was ist Docker?
* Was sind Container?
* Was sind Docker-Images?
* Was ist Docker Hub?
* Wie installiert man Docker?
* Wie arbeitet man mit Docker-Containern?
* Wie arbeitet man mit Docker-Images?
* Was ist ein Dockerfile?
* Wie man eine Docker-Anwendung einsetzt
* Docker-Netzwerke
* Was ist Docker Swarm?
* Wie man einen Docker Swarm Cluster einsetzt und verwaltet

Ich werde **DigitalOcean** für alle Demos verwenden, daher empfehle ich Ihnen dringend, ein **DigitalOcean**-Konto zu erstellen, um den Kurs zu verfolgen. Sie werden mehr lernen, wenn Sie es tun!

Um die Sache noch besser zu machen, können Sie meinen Empfehlungslink verwenden, um ein kostenloses Guthaben in Höhe von 100 $ zu erhalten, mit dem Sie Ihre virtuellen Maschinen bereitstellen und die Anleitung selbst auf einigen **DigitalOcean-Servern** testen können:

**[DigitalOcean $100 Free Credit](https://m.do.co/c/2a9bba940f39)**

Sobald Sie Ihr Konto haben, erfahren Sie hier, wie Sie Ihr erstes Droplet/Server einrichten:

[https://www.digitalocean.com/docs/droplets/how-to/create/](https://www.digitalocean.com/docs/droplets/how-to/create/)

Ich werde **Ubuntu 21.04** verwenden, also würde ich empfehlen, dass Sie dasselbe verwenden, damit Sie mitmachen können.

Sie können Docker jedoch auf fast jedem Betriebssystem ausführen, einschließlich Linux, Windows, Mac, BSD und so weiter.


## Was ist ein Container?

Laut der offiziellen Definition auf der [docker.com](docker.com)-Website ist ein Container eine Standard-Softwareeinheit, die den Code und alle seine Abhängigkeiten zusammenfasst, damit die Anwendung schnell und zuverlässig in einer anderen Computerumgebung ausgeführt werden kann. Ein Docker-Container-Image ist ein leichtgewichtiges, eigenständiges, ausführbares Softwarepaket, das alles enthält, was zur Ausführung einer Anwendung erforderlich ist: Code, Laufzeit, Systemtools, Systembibliotheken und Einstellungen.

Container-Images werden zur Laufzeit zu Containern, und im Falle von Docker-Containern werden Images zu Containern, wenn sie auf der Docker Engine ausgeführt werden. Containerisierte Software ist sowohl für Linux- als auch für Windows-basierte Anwendungen verfügbar und wird unabhängig von der Infrastruktur immer gleich ausgeführt. Container isolieren die Software von ihrer Umgebung und stellen sicher, dass sie trotz der Unterschiede, beispielsweise zwischen Entwicklung und Staging, einheitlich funktioniert.

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/de/assets/images/infrastructure.png)

## Was ist ein Docker Image?

Ein **Docker-Image** ist lediglich eine Vorlage, die zum Erstellen eines laufenden Docker-Containers verwendet wird, ähnlich wie ISO-Dateien und virtuelle Maschinen. Die Container sind im Wesentlichen die laufende Instanz eines Images. Images werden verwendet, um containerisierte Anwendungen gemeinsam zu nutzen. Sammlungen von Images werden in Registern wie [DockerHub](https://hub.docker.com/) oder privaten Registern gespeichert.

![](https://github.com/bobbyiliev/introduction-to-docker-ebook/raw/main/ebook/de/assets/images/process.png)


## Was ist der Docker Hub?

DockerHub ist die standardmäßige **Docker-Image-Registry**, in der wir unsere **Docker-Images** speichern können. Sie können es sich als GitHub für Git-Projekte vorstellen.

Hier ist ein Link zum Docker Hub:

[https://hub.docker.com](https://hub.docker.com)

Sie können sich für ein kostenloses Konto anmelden. Auf diese Weise können Sie Ihre Docker-Images von Ihrem lokalen Rechner zu DockerHub übertragen.

