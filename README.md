# Schlangenprogrammierspiel - Installation mit Ansible

## Voraussetzungen:
- Die Anmeldung auf dem Zielrechner als root muss mit ssh-key, also ohne Passwort, funktionieren.
- IP-Adresse des Zielrechners ist in der Datei ``hosts`` eingetragen.

## Konfiguration:
Die Konfiguration ist **komplett optional** und erfolgt in ``group_vars/all``.

Wenn nichts konfiguriert wird werden die unten angegebenen Einstellungen verwendet.
"database.password" und "django.secret_key" werden dann auf zufällige Werte gesetzt und in ``/etc/spn/db.conf`` und ``/etc/spn/django.conf`` abgelegt.

```
schlangenprogrammierspiel:
  git_repository: https://github.com/schlangenprogrammiernacht/spn-meta.git
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
Nach der Installation ist das Schlangenprogrammierspiel auf dem Zielrechner unter Port 80 bzw. dem in der Variable schlangenprogrammierspiel.web_port angegebenen Port verfügbar.

- Stopp: ```systemctl stop spn.target```
- Start: ```systemctl start spn.target```

## Anmerkungen
### Apache2-Reverse-Proxy
Ich hatte ein bisschen zu kämpfen bis ich einen Apache2-Reverse-Proxy vor dem Schlangenprogrammierspiel-Webserver zum Laufen gebracht habe.
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