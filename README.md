# Instalación de Ansible y rol para instalar NTP

### Arquitectura Ansible
<img title="Ansible" alt="Alt text" src="/images/arq-ansible.jpg">

### Establecer el hostname a cada maquina servidor
```sh
hostnamectl   
sudo hostnamectl set-hostname test-kubmaster1.example.com
```

### Establecer el hostname en el archivo /etc/hosts
```sh
sudo vi /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 test-kubmaster1.example.com
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.0.0.X test-kubmaster1.example.com test-kubmaster1
```
### Instalar Ansible en Nodo Management
```sh
subscription-manager repos --enable rhel-7-server-ansible-2.6-rpms
yum install -y ansible
ansible --version
```

### creacion de usuarios en todos los nodos incluyendo Nodo Management
```shell
useradd ansible
passwd ansible
#contraseña: XXXXX

usermod -aG wheel ansible
su ansible -
groups
sudo whoami
exit

# visudo para activar modo sudo
visudo
%wheel  ALL=(ALL)  ALL
```

### Actualizando el hosts del Nodo Management
```sh
vi /etc/hosts
```
```text
10.0.0.50  test-kubmaster1.example.com
10.0.0.51  test-kubmaster2.example.com
10.0.0.52  test-kubmaster3.example.com
10.0.0.53  test-kubworker1.example.com
10.0.0.54  test-kubworker2.example.com
10.0.0.55  test-kubworker3.example.com
10.0.0.56  test-kubworker4.example.com
10.0.0.57  test-kubworker5.example.com
10.0.0.58  test-kubworker6.example.com
```

### Creando credenciales del usuario ansible en el servidor: 10.0.0.5
```sh
#usuario ansible logeado
ssh-keygen
#Ingresar por defecto ENTER
#Ingresar por defecto ENTER

#Hacerlo con los todos los servidores de master y workers
ssh-copy-id ansible@test-kubmaster1.example.com
#Ingresar la clave del usuario ansible
```

### Actualizando archivo hosts
```sh
sudo vi /etc/ansible/hosts
```
```text
[kubemaster]
test-kubmaster1.example.com
test-kubmaster2.example.com
test-kubmaster3.example.com
 
[kubeworkers]
test-kubworker1.example.com
test-kubworker2.example.com
test-kubworker3.example.com
test-kubworker4.example.com
test-kubworker5.example.com
test-kubworker6.example.com

[all:vars]
ansible_connection=ssh
ansible_ssh_user=ansible
ansible_ssh_pass=XXXXX
```
```sh
sudo vi /etc/ansible/ansible.cfg
```
```text
#Descomentar la linea
[defaults]
host_key_checking = false
```

### Test ansible ping, todos deben dar como resultado "ping:pong"
```sh
ansible all -m ping

test-kubworker1.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubworker3.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubworker4.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubworker2.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubworker5.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubworker6.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubmaster1.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubmaster2.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
test-kubmaster3.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

### Verificar sintaxis del playbook y ejecutar
```ssh
#Para verificar syntaxis
ansible-playbook --syntax-check install-ntp.yml -v -K
```
```ssh
#Para ejecutar playbook
ansible-playbook install-ntp.yml -v -K
```
```ssh
PLAY RECAP ************************************************************************************************************
nsrtm-test-master01.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-master02.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-master03.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-worker01.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-worker02.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-worker03.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-worker04.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-worker05.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
nsrtm-test-worker06.example.com : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
By
-------
- Rolly Villegas Delgado  -  vdrolly@gmail.com

