# Schlangenprogrammierspiel - Installation mit Ansible

## Voraussetzungen:
- Die Anmeldung auf dem Zielrechner als root muss mit ssh-key, also ohne Passwort, funktionieren.
- IP-Adresse des Zielrechners ist in der Datei ``hosts`` eingetragen.

## Konfiguration:
Die Konfiguration ist optional und erfolgt in ``group_vars/all``.

Wenn nichts konfiguriert wird werden die unten angegebenen Standardeinstellungen verwendet.
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
