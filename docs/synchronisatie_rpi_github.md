# Synchronisatie van het project op de RPi met de Github repository

Ja, helemaal eerlijk: Git + submodules + SFTP + Raspberry Pi is gewoon veel tegelijk. Dat ligt niet aan jou. Submodules zijn krachtig, maar niet bepaald ontworpen om menselijk aan te voelen.

Ik zou het voor jezelf reduceren tot **één simpele regel** en **twee vaste recepten**.

**De Regel**

```
Windows/PyCharm is de waarheid.
Raspberry Pi is alleen testmachine.
```

Dus: als Git op de Pi klaagt over lokale wijzigingen, betekent dat meestal:

```
SFTP heeft testbestanden aangepast op de Pi.
Die mogen weg, want de echte versie staat op Windows/GitHub.
```

**Recept 1: Pi Terug Schoonmaken**

Gebruik dit wanneer `git submodule update` klaagt over lokale changes:

```
cd /shared/python-projects/python-automation/packages/pyberryplc-stepper
git reset --hard HEAD

cd /shared/python-projects/python-automation
git submodule update --init --recursive
```

Dat wist lokale testwijzigingen in `pyberryplc-stepper` op de Pi.

**Recept 2: Alles Updaten Op De Pi**

Gebruik dit als je de laatste versie van GitHub wilt ophalen:

```
cd /shared/python-projects/python-automation
git pull
git submodule update --init --recursive
```

Als dat klaagt over local changes, eerst Recept 1 doen.

Meer hoef je in de praktijk niet te onthouden.

Je kunt er zelfs een scriptje van maken op de Pi, bijvoorbeeld:

```
nano update_python_automation.sh
```

Met inhoud:

```
#!/bin/bash
set -e

cd /shared/python-projects/python-automation/packages/pyberryplc-stepper
git reset --hard HEAD

cd /shared/python-projects/python-automation
git pull
git submodule update --init --recursive
```

Dan:

```
chmod +x update_python_automation.sh
```

En voortaan:

```
./update_python_automation.sh
```

Dat is veel menselijker.