1.8 Webcluster
1. Erweitern Sie das statische Inventory-File um die Hosts webserver[01:03] und loadbalancer. Die
Gruppe webcluster-backend soll die Hosts webserver01, webserver02 und webserver03 enthalten
und die Gruppe webcluster-loadbalancer soll den Host loadbalancer enthalten.
2. Auf allen Hosts soll ein NginX Webserver installiert werden. Bei allen vier Hosts handelt es sich um
Fedora 36 Hosts. Die Hosts webserver[01:03] sollen dabei als redundantes Backend dienen und der
Host loadbalancer soll einen Loadbalancer sein, der vor die drei Webserver geschaltet ist. Erstellen
Sie ein Playbook setup.yml das die Konfiguration der Hosts vornimmt. Im ersten Play sollen dabei
die Webserver in diesen Schritten konfiguriert werden:
- Installieren Sie das Paket nginx-1.20.2-2.fc36
- Auf den webserver-Hosts muss die NginX-Konfiguration nicht angepasst werden. Ändern Sie hier
aber die Webseite (/usr/share/testpage/index.html), sodass bei einem Aufruf (z.B. curl
http://webserver01/index.html) nur der String "You are looking at Host webserver01 with IP
10.88.0.201" zurückgegeben wird. Dabei soll natürlich auf jedem Webserver der passende
Hostname und die passende IP-Adresse angezeigt werden. Nutzen Sie hier ein Template und
passende Ansible-Variablen.
Im zweiten Play des Playbooks setup.yml soll der Loadbalancer konfiguriert werden:
- NginX soll installiert werden
- Auf dem Loadbalancer soll die Nginx Konfiguration angepasst werden, sodass nach dem Round-
Robin verfahren die Aufrufe an die drei Webserver weitergeleitet werden (siehe
https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/). Nutzen Sie auch hier
ein Template.
- Fügen Sie einen Handler hinzu, der bei einer Änderung an der NginX-Konfiguration den Service
restartet.
- Testen Sie den Loadbalancer durch wiederholtes Ausführen von "curl
http://loadbalancer/index.html" und prüfen Sie, dass alle Webserver genutzt werden.
3. Erstellen Sie ein neues Playbook update-nginx.yml. Hier soll das Paket nginx auf den Webservern
auf die aktuelle Version aktualisiert werden. Beachten Sie dabei folgendes:
- Es soll immer nur ein Webserver parallel bearbeitet werden.
- Damit der Webservice auch während des Updates der Webserver verfügbar ist, soll der
Webserver, bevor auf ihm das Update ausgeführt wird, aus der Loadbalancer-Konfiguration
herausgenommen werden. Danach soll das Update durchgeführt werden und anschließend soll der
Webserver wieder der Loadbalancer Konfiguration hinzugefügt werden. Denken Sie an einen Reload
des Loadbalancers, wenn Sie die Konfiguration geändert haben.
- Durch das Hinzufügen einer Pause mithilfe des Pause-Moduls können Sie zwischendurch mit dem
curl-Befehl überprüfen, ob ihr Playbook wie gewünscht funktioniert und der Webservice dauerhaft
erreichbar ist.