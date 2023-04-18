# Instalación de Ansible y rol para instalar NTP



### Establecer el hostname a cada maquina virtual
```sh
hostnamectl   
sudo hostnamectl set-hostname test-kubmaster1.example.com
```

### Establecer el hostname en el archivo /etc/hosts
```sh
sudo vi /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 test-kubmaster1.example.com
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
10.4.115.X test-kubmaster1.example.com test-kubmaster1
```
### Instaalr Ansible en Nodo Management
subscription-manager repos --enable rhel-7-server-ansible-2.6-rpms
yum install -y ansible
ansible --version


### creacion de usuarios en todos los nodos incluyendo 10.4.115.5
```shell
useradd ansible
passwd ansible
#contrase�a: simplEd17y

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
10.4.115.50  test-kubmaster1.example.com
10.4.115.51  test-kubmaster2.example.com
10.4.115.52  test-kubmaster3.example.com
10.4.115.53  test-kubworker1.example.com
10.4.115.54  test-kubworker2.example.com
10.4.115.55  test-kubworker3.example.com
10.4.115.56  test-kubworker4.example.com
10.4.115.57  test-kubworker5.example.com
10.4.115.58  test-kubworker6.example.com
```

### Creando credenciales del usuario ansible en el servidor: 10.4.115.5
```sh
#usuario ansible logeado
ssh-keygen
#Ingresar por defecto ENTER
#Ingresar por defecto ENTER

#Hacerlo con los todos los VMs de master y workers
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
ansible_ssh_pass=simplEd17y
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

#Para verificar syntaxis
ansible-playbook --syntax-check install-netp.yml -v -K

# Ejecutar
ansible-playbook install-ntp.yml -v -K
```

### Versiones instaladas
```sh

```

### Instalando HaProxy en el servidor 10.4.115.5
```

```

Autores
-------
- Rolly Villegas Delgado  -  vdrolly@gmail.com

