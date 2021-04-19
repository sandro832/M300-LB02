### Docker
Docker legt Programme samt ihrer Abhängigkeiten in Images ab. Diese bilden die Basis für virtualisierte Container, die auf nahezu jedem Betriebssystem lauffähig sind. So werden Anwendungen portabel und unkompliziert, sei es während der Entwicklung oder beim Skalieren von SaaS-Clustern.

Docker ist eine Software, welche die Container-Virtualisierung von Anwendungen ermöglicht. Anwendungen können inklusive ihrer Abhängigkeiten in ein Image gepackt werden. Mittels einer speziellen Engine kann die so verpackte Anwendung dann in einem Docker Container ausgeführt werden.

### Docker Container vs virtuelle Maschinen
Virtuelle Maschinen enthalten von der simulierten Hardware über das Betriebssystem bis zu den installierten Programmen eine große Menge an Informationen. Die Folge: Sie verbrauchen viel Speicherplatz und Ressourcen.

Virtuelle Maschinen haben aber nach wie vor ihre Daseinsberechtigung; zum Beispiel, wenn auf einem Host mehrere Maschinen mit jeweils unterschiedlichen Betriebssystemen oder Hardware-Spezifikationen simuliert werden müssen. Denn ein Docker Container enthält kein eigenes Betriebssystem und keine simulierte Hardware. Hier wird auf das System des Hosts zugegriffen, sodass alle Container OS und Hardware gemeinsam nutzen.


### Docker commands

 Befehl | Funktion | Beispiel
|----------|:-------------:|------:|
| docker pull | Holt ein docker image | docker pull [Name des Images] 
| docker run | startet docker mit image | docker run -it [Name des Images] 
| docker ps | zeigt laufende maschinen | docker ps 
| docker version | Zeigt docker Version vom Echo-Client und Server an | docker version 
| docker exec | Fürth befehl in einem laufenden Container aus | docker exec [ID des Containers] 
| docker search | Durchsucht Dockerhub nach images | docker search [ID des Containers] 
| docker stop | Haltet docker container an | docker stop [ID des Containers] 
| docker commit | erstellt ein neues image mit den neuen änderungen| docker commit [ID des Containers] 


### Arbeitschritte
Bei meinem Auftrag ging es darum ein Webserer zu hosten und diesmal mithilfe von Container anstatt Virtuelle Machinen. So werden nähmlich viel weniger Computer ressourcen verwendet und das hosten ist auch einfach gestahltet.

#### 1.1 Docker file beschaffung
Zuerst musste ich mir ein Docker File beschaffen was ich von einem Kollegen bekam. Das sieht ungefähr so aus. 


Bild

#### 1.2 Container image 
Mit dem command "docker build" habe ich dan den container gebaut mit den konfigurationen die ich im dockerfile gegeben habe. Wichtig hier ist, dass man das Dockerfile selbst nicht im Pfad angeben muss. Nur der Ordner, in welchem sich das File befindet, muss angegeben werden. Das File muss aber zwingend "Dockerfile" heissen.

#### 1.3 Container bauen
Danch konnte ich den Container bauen mit dem run command:
<mark>docker run --rm -d -p 8080:80 -v /web:/var/www/html --name web aa6106ddd123</mark>
Hier sieht man noch die Portweiterleitung von 80 auf 8080.

#### 1.4 index.html file
Das index file gab es noch zu erstellen das es diese nicht beim deployment selbst erstellt hat. Im file habe ich noch den Link zu meinem persöhnlichen repository angegeben. Beim Index handelt es sich nathürlich um den Inhalt des Webservers der gezeigt werden soll. Man kan auch kein index file haben dan wird die default page angezeigt. Mein index file sieht so aus:



Bild



Das File musste ich dan noch in die VM bringen was mit folgendem Kommando ging:

<Mark>docker cp [/SpeicherortDerDatei/index.html] [Container Name]:[Destination Path im Container]</Mark>


#### 1.5 Testing 

Als lezter Schritt musste ich noch die Verbindung testen. Das konnte ich ganz einfach über die Localhost addresse mit dem Port 187 den ich im Portforwarding angegeben habe. 


Bild

Sicherheit

Wie bei jeder neuen Software-Technologie gilt auch für Docker-Container: Sie sind keine Wunderwaffe und können alleine nicht jedes Problem lösen. Zwar kann Software in einem Container standardmäßig sicherer sein als Software, die auf einem Bare-Metal-System läuft. Allerdings ist diese Sicherheit trügerisch, denn sie sagt nichts über die Sicherheitsstandards außerhalb des Docker-Containers, beziehungsweise dessen Umgebung aus. Selbstverständlich können Container einer Anwendung eine zusätzliche Sicherheitsebene hinzufügen, aber nur als Teil eines allgemeinen Konzeptes zur Sicherung einer Anwendung im Gesamtkontext.

https://www.computerwoche.de/a/die-wichtigsten-vor-und-nachteile-von-docker-containern,3546671

Wie oben beschrieben sorgt die Docker technologie nicht für Sicherheit und man sollte darum zusätliche service und dienste aufsetzen umd dies zu gewährleisten. Eine davon nennt sich Cadvisor. Es ist ein Überwachungstool von google das sich sehr einfach aufsetzen lässt. Dafür benutzt man nur folgenden befehl um ein container zu erstehlen auf dem diese Webapplikation läuft: 

<Mark>docker run -d --name cadvisor -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro -p 188:8080 google/cadvisor:latest</Mark>

Noch zu beachten ist das man ein Port wählt der nicht besetzt ist da es sonst zu komplikationen kommen könnte. In meinem Beispiel habe ich 188 genommen da das nahe an 187 dran ist was ich für den Webserver benutzt habe. Den Zugriff konnte ich mit 127.0.0.1:188 machen also musste ich nur den Port anpassen.

Bild


