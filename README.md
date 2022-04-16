# ![ChRIS logo](https://github.com/FNNDSC/ChRIS_ultron_backEnd/blob/master/docs/assets/logo_chris.png) miniChRIS-k8s

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![GitHub last commit](https://img.shields.io/github/last-commit/Prakashh21/miniChRIS-k8s)

**The goal of this repository is to create a development instance of ChRIS which can work by simply wiriting couple of kubectl commands like -- kubectl apply -f chris.yaml (something like this) , and that would work in any kind of kubernetes distribution like miniKube , k3s.**      

The conventional way to run CUBE (chris backend) is through  ChRIS_ultron_backEnd/make.sh. , make.sh is responsible for setting up a development instance of chris , but make.sh executes dangerous commands like chmod 755 and chmod -R 777 on the hostfile system.

miniChRIS addresses this problem wherein , It provides a no-nonsense collection of scripts which use Docker Compose  to run a minimal and complete chRIS system , unlike make.sh , miniChRIS is fully containerized and writes all the data into the docker volume , but the problem is miniChRIS runs in docker swarm only.

