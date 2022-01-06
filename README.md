# Schlangenprogrammierspiel - Installation mit Ansible

Installiert das Schlangenprogrammierspiel von https://github.com/schlangenprogrammiernacht unter Debian 11.

Nach der Installation ist das Schlangenprogrammierspiel auf dem Zielrechner unter Port 80 bzw. dem in der Variable ``schlangenprogrammierspiel.web_port`` (siehe unten) angegebenen Port erreichbar.

## Voraussetzungen:
- Die Anmeldung auf dem Zielrechner als root muss mit ssh-key, also ohne Passwort, funktionieren.
- IP-Adresse des Zielrechners ist in der Datei ``hosts`` eingetragen.

## Konfiguration:
Die Konfiguration ist **komplett optional** und erfolgt in ``group_vars/all``.

Wenn nichts konfiguriert wird werden die unten angegebenen Einstellungen verwendet.
"database.password" und "django.secret_key" werden dann auf zufällige Werte gesetzt und in ``/etc/spn/db.conf`` und ``/etc/spn/django.conf`` abgelegt.

```
schlangenprogrammierspiel:
  git:
    repository: https://github.com/schlangenprogrammiernacht/spn-meta.git
    version: master
  database:
    host: localhost
    db: spn
    user: spn
    password: !topSecret!
  django:
    secret_key: 123mySuperSecreDjangoPassword45
  web_port: 80
```

## Installation
```ansible-playbook -i hosts schlangenprogrammierspiel.yml```

## Start/Stopp
- Stopp: ```systemctl stop spn.target```
- Start: ```systemctl start spn.target```

## Anmerkungen
### Apache2-Reverse-Proxy
Ich hatte ein bisschen zu kämpfen bis ich einen zusätzlichen Apache2-Reverse-Proxy vor dem Schlangenprogrammierspiel-Server zum Laufen gebracht habe.
Hier die Konfiguration mit der es schließlich funktioniert hat (das Schlangenprogrammierspiel läuft auf 192.168.3.54, der "web_port" wurde auf 80 belassen):
```
<VirtualHost *:80>
    ServerName spn.example.com

    RewriteEngine on
    ProxyRequests off
    ProxyPreserveHost on

    # Websocket
    RewriteCond %{REQUEST_URI} "^/websocket" [NC]
    RewriteRule /(.*) "ws://192.168.3.54:80/$1" [P,L]

    # Website
    ProxyPass / http://192.168.3.54:80/
    ProxyPassReverse / http://192.168.3.54:80/
</VirtualHost>
```