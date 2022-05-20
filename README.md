# Upozornění

Jedná se o repozitář sloužící především k otestování řídících algoritmů na robotické platformě založené na RC autíčku.  Repozitář obsahuje zároveň [f1tenth_system](https://github.com/f1tenth/f1tenth_system) od [F1TENTH](https://f1tenth.org/) který zde slouží k základnímu nastavení robotu a zároveň obsahuje systém lokalizace [particle_filter](https://github.com/mit-racecar/particle_filter).

# Nastavení pro otestování v simulátoru

Pro zprovoznění je zapotřebí mít minimálně takovou robotickou platformu, která bude postavena podle [návodu](https://f1tenth.org/build.html) na sestavení autonomního závodního robota od [F1TENTH](https://f1tenth.org/). 
Robot musí mít nainstalován operační systém Linux a ROS Melodic. Repositář už obsahuje workspace, tudíž stačí pouze nastavit samotný mikropočítač a nainstalovat ROS a popřípadě nastavit regulátor VESC. Po stažení bude nejspíše potřeba nainstalovat navíc nějaké ROS balíčky. Ty se nainstalují pro ROS Melodic pomocí příkazů v terminálu:
Poté se balíčky jednoduše nainstalují konkrétně pro ROS Melodic pomocí příkazů v terminálu:
```
$ sudo apt-get update
$ sudo apt-get install ros-melodic-driver-base
```
Následně potřebují být všechny Python skripty spustitelné. To se provede pomocí příkazů:
```
$ cd f110_ws
$​ find . -name “*.py” -exec chmod +x {} \;
```
Poté je zapotřebí nastavit udev pravidla. Především pak pro regulátor VESC a LiDAR senzor. Tyto pravidla se jednoduše nastaví pomocí druhé části [návodu](https://github.com/f1tenth/f1tenth_doc/blob/stable/getting_started/firmware/drive_workspace.rst). Po nastavení těchto pravidel je následně potřeba pro mapování nainstalovat balíček HECTOR SLAM. Nainstalování tohoto balíčku se provede pomocí příkazu:
```
$ sudo apt-get install ros-melodic-hector-slam
```
Dále je potřeba pro generování trajektorie stáhnout Python knihovny  [scipy](https://scipy.org/) a [scikit-image](https://scikit-image.org/). Všechny tyto knihovny se dají nainstalovat pomocí dvou příkazů: 
```
$ sudo apt-get install python-skimage
$ sudo apt-get install python-scipy
```
Poslední části, která je důležitá pro otestování řídícího algoritmu je nainstalování dříve zmíněných knihoven [OSQP](https://osqp.org/), [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page) a [osqp-eigen](https://github.com/robotology/osqp-eigen). Celý proces instalace knihovny [OSQP](https://osqp.org/) je popsán přímo v [návodu](https://osqp.org/docs/get_started/sources.html). Stejně tak nainstalování knihovny [osqp-eigen](https://github.com/robotology/osqp-eigen),  kde návod na instalaci je popsán rovnou v repositáři. Nakonec knihovna [Eigen](https://eigen.tuxfamily.org/index.php?title=Main_Page) se nainstaluje pomocí příkazu: 
```
$ sudo apt-get install libeigen3-dev
```
Nakonec je pro nainstalování knihovny [osqp-eigen](https://github.com/robotology/osqp-eigen) potřeba postupovat pomocí [návodu](https://github.com/robotology/osqp-eigen). Pro zprovoznění systému lokalizace je ještě zapotřebí následně nainstalovat knihovnu [RangeLibc](https://github.com/kctess5/range_libc). Po úspěšném nastavení a nainstalovní příslušných knihoven stačí workspace už jen přeložit pomocí sady následujících příkazů:
```
$ cd ~/f110_ws
$ catkin_make
$ source devel/setup.bash
```
Pro mapování je potřeba spustit následující sekvenci příkazů, každý v jiném terminálu:
```
$ roslaunch racecar teleop.launch
$ roslaunch racecar mapping.launch
```
Vytvořenou mapu pak lze následně uložit pod názvem \texttt{track} následující sadou příkazů:
```
$ cd ~/f110_ws/src/particle_filter/maps
$ rosrun map_server map_saver -f track
```
Po zmapování lze již spustit řídící algoritmus. To se provede opět sadou příkazů, kde každý příkaz bude v jiném terminálu: 
```
$ roslaunch racecar teleop.launch
$ roslaunch partcile_filter localize.launch
$ rosrun navigation line.py
$ rosrun navigation controller.launch
```
Ovládání robotu bylo testováno na XBOX One ovladači. Pro ovládání robotu ručně je potřeba mít stisknuté tlačítko **LB** a pro autonomní řízení pak tlačítko **RB**. 
