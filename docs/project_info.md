# PROJECT INFO python-automation

## Structuur

Je hebt nu een meta-repo:

```
python-automation
├─ packages/
│  ├─ automation-motion      # gedeelde motion profiles/charts
│  ├─ pyberryplc             # PLC core
│  ├─ pyberryplc-stepper     # stepper add-on
│  └─ python-robot           # robotica package
├─ docs/
├─ demos/
└─ README.md
```

`python-automation` is zelf vooral een workspace/meta-repo. De packages zijn aparte Git-repositories, gekoppeld als submodules.

## Dependencies

De logische afhankelijkheden zijn:

```
pyberryplc-stepper
├─ pyberryplc
└─ automation-motion

python-robot
└─ automation-motion

pyberryplc
└─ geen dependency op stepper
```

`stepper` is dus losgetrokken uit `pyberryplc`, en gebruikt nu dezelfde motion-code als `python-robot`.

## Installatie vanaf GitHub

Voor een volledige checkout:

```
git clone --recurse-submodules git@github.com:TomLXXVI/python-automation.git
cd python-automation
```

Als iemand HTTPS gebruikt en alleen publieke repos nodig heeft:

```
git clone --recurse-submodules https://github.com/TomLXXVI/python-automation.git
cd python-automation
```

Als submodules ontbreken of leeg zijn:

```
git submodule sync --recursive
git submodule update --init --recursive
```

------

Een Git submodule is eigenlijk: “in deze map hoort een andere Git-repository te staan, op deze specifieke commit”.

In jouw meta-repo `python-automation` staan dus niet letterlijk alle files van `automation-motion`, `pyberryplc`, enz. De meta-repo bewaart vooral verwijzingen:

```
packages/automation-motion      -> repo automation-motion, commit abc123
packages/pyberryplc             -> repo pyberryplc, commit def456
packages/pyberryplc-stepper     -> repo pyberryplc-stepper, commit ghi789
packages/python-robot           -> repo python-robot, commit jkl012
```

Als je clone doet met:

```
git clone --recurse-submodules git@github.com:TomLXXVI/python-automation.git
```

dan doet Git meteen twee dingen:

1. clone de meta-repo `python-automation`
2. clone ook alle submodule-repos in `packages/...`

Dan is alles normaal meteen goed.

Maar als iemand per ongeluk alleen dit doet:

```
git clone git@github.com:TomLXXVI/python-automation.git
```

zonder `--recurse-submodules`, dan krijg je dit:

```
python-automation/
├─ packages/
│  ├─ automation-motion/      # leeg of bijna leeg
│  ├─ pyberryplc/             # leeg of bijna leeg
│  ├─ pyberryplc-stepper/     # leeg of bijna leeg
│  └─ python-robot/           # leeg of bijna leeg
```

Dan ontbreken de echte package-files. Dáárvoor dient:

```
git submodule update --init --recursive
```

Betekenis:

```
--init
```

Initialiseer submodules die nog niet lokaal bekend zijn.

```
--recursive
```

Doe dit ook voor eventuele submodules binnen submodules.

En:

```
git submodule sync --recursive
```

dient om de lokale submodule-URLs te synchroniseren met `.gitmodules`.

Dat is vooral nuttig als je later de URL verandert, bijvoorbeeld van HTTPS naar SSH:

```
https://github.com/TomLXXVI/automation-motion.git
```

naar:

```
git@github.com:TomLXXVI/automation-motion.git
```

Dan zegt `sync`: “gebruik lokaal opnieuw de URLs zoals ze nu in `.gitmodules` staan”.

Dus kort:

```
git clone --recurse-submodules ...
```

= alles meteen in één keer juist binnenhalen.

```
git submodule update --init --recursive
```

= reparatie/aanvulling als iemand zonder submodules gekloond heeft of als er nieuwe submodules bijkomen.

```
git submodule sync --recursive
```

= gebruik de juiste/nieuwe submodule-URLs uit `.gitmodules`.

In dagelijks gebruik op de Pi zal je meestal alleen dit nodig hebben:

```
git pull
git submodule update --init --recursive
```

Dan komt de meta-repo én de juiste submodule-versies mee.

------

Daarna een virtual environment maken:

```
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip wheel setuptools
```

Packages editable installeren:

```
pip install -e packages/automation-motion
pip install -e packages/pyberryplc
pip install -e packages/pyberryplc-stepper
```

Voor robotica ook:

```
pip install -e packages/python-robot
```

Controle:

```
python -c "import automation_motion; print(automation_motion.__file__)"
python -c "import pyberryplc; print(pyberryplc.__file__)"
python -c "import pyberryplc_stepper; print(pyberryplc_stepper.__file__)"
python -c "import python_robot; print(python_robot.__file__)"
```

### Eerste installatie op Raspberry Pi

Op de Pi is SSH aanbevolen voor GitHub:

```
ssh-keygen -t ed25519 -C "raspberry-pi"
cat ~/.ssh/id_ed25519.pub
```

Public key toevoegen in GitHub:

```
Settings > SSH and GPG keys > New SSH key
```

Daarna testen:

```
ssh -T git@github.com
```

Clone:

```
git clone --recurse-submodules git@github.com:TomLXXVI/python-automation.git
```

Voor GPIO/stepper-experimenten op de Pi kan ook nodig zijn:

```
sudo apt update
sudo apt install pigpio python3-dev
sudo systemctl enable pigpiod
sudo systemctl start pigpiod
```

## Werken met submodules

Als je in een package iets wijzigt:

```
cd packages/automation-motion
git add .
git commit -m "..."
git push
```

Daarna in de meta-repo de submodule-pointer bijwerken:

```
cd ../..
git status
git add packages/automation-motion
git commit -m "Update automation-motion submodule"
git push
```

Op een andere machine updaten:

```
git pull
git submodule update --init --recursive
```

Of expliciet de nieuwste remote commits ophalen:

```
git submodule update --remote --merge
```

## Belangrijke importnamen
De distributienamen en importnamen verschillen soms:

```
automation-motion   -> import automation_motion
pyberryplc          -> import pyberryplc
pyberryplc-stepper  -> import pyberryplc_stepper
python-robot        -> import python_robot
```

Voorbeeld:

```
from automation_motion.profiles_1D.point_to_point import TrapezoidalProfile
from pyberryplc.core import MemoryVariable
from pyberryplc_stepper import TMC2208StepperMotor, RotationDirection
```
