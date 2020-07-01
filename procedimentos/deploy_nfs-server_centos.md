# Instalação NFS-Server no Centos

- [Instalação NFS-Server no Centos](#instalação-nfs-server-no-centos)
  - [Primeiro passo corrigir o horário do servidor](#primeiro-passo-corrigir-o-horário-do-servidor)
    - [Verificar o timezone atual](#verificar-o-timezone-atual)
    - [Listar timezone disponiveis](#listar-timezone-disponiveis)
    - [Definir timezone](#definir-timezone)
    - [Verifique status do NTP](#verifique-status-do-ntp)
  - [Ajustes de S.O após a instalação](#ajustes-de-so-após-a-instalação)
    - [Verificar se é necessário atualização do S.O](#verificar-se-é-necessário-atualização-do-so)
    - [Instalar utilitários](#instalar-utilitários)
  - [NFS-Server](#nfs-server)
    - [Criar diretório para dados que serão compartilhados entrem os nodes docker](#criar-diretório-para-dados-que-serão-compartilhados-entrem-os-nodes-docker)
    - [Instalar NFS](#instalar-nfs)
    - [Configurando exports](#configurando-exports)
    - [Inicializando o serviço](#inicializando-o-serviço)
    - [Liberando Firewall](#liberando-firewall)
  - [NFS-Client no CentOS](#nfs-client-no-centos)
    - [Instalar utilitários no client](#instalar-utilitários-no-client)
    - [Instalar NFS no client](#instalar-nfs-no-client)
    - [Criar diretório para o ponto de montagem](#criar-diretório-para-o-ponto-de-montagem)
    - [Validando o ponto de montagem](#validando-o-ponto-de-montagem)
    - [Verifique se funcionou](#verifique-se-funcionou)
    - [Removendo ponto de montagem temporario](#removendo-ponto-de-montagem-temporario)
    - [Configurando ponto de montagem permanente](#configurando-ponto-de-montagem-permanente)
    - [Force o reload do fstab](#force-o-reload-do-fstab)

## Primeiro passo corrigir o horário do servidor

### Verificar o timezone atual

```bash
timedatectl status
```

Output:

```bash
               Local time: Wed 2020-06-03 05:03:25 -03
           Universal time: Wed 2020-06-03 08:03:25 UTC
                 RTC time: Wed 2020-06-03 08:03:24
                Time zone: America/New_York (-03, -0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

### Listar timezone disponiveis

```bash
timedatectl list-timezones | grep Sao_Paulo
```

Output:

```bash
America/Sao_Paulo
```

### Definir timezone

```bash
timedatectl set-timezone America/Sao_Paulo
```

### Verifique status do NTP

```bash
timedatectl status
```

```bash
               Local time: Wed 2020-06-03 05:03:25 -03
           Universal time: Wed 2020-06-03 08:03:25 UTC
                 RTC time: Wed 2020-06-03 08:03:24
                Time zone: America/Sao_Paulo (-03, -0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

## Ajustes de S.O após a instalação

### Verificar se é necessário atualização do S.O

```bash
dnf clean all
dnf check-update
```

### Instalar utilitários

```bash
dnf install -y net-tools vim nano epel-release wget curl tcpdump
```

## NFS-Server

### Criar diretório para dados que serão compartilhados entrem os nodes docker

```bash
mkdir /data/data-docker
```

### Instalar NFS

```bash
dnf install nfs-utils -y
```

### Configurando exports

```shell
vim /etc/exports
/data/data-docker/ *(rw,sync,no_root_squash,no_subtree_check)
exportfs -ar
```

### Inicializando o serviço

```bash
systemctl enable --now nfs-server
systemctl status nfs-server
```

### Liberando Firewall

```bash
firewall-cmd --permanent --add-service=nfs
firewall-cmd --permanent --add-service=mountd
firewall-cmd --permanent --add-service=rpc-bind
firewall-cmd --reload
```

## NFS-Client no CentOS

`LEMBRE-SE DE VERIFICAR A DATA E HORA DOS SERVIDORES`

### Instalar utilitários no client

```bash
dnf install -y net-tools vim nano epel-release wget curl tcpdump
```

### Instalar NFS no client

```bash
dnf install nfs-utils -y
```

### Criar diretório para o ponto de montagem

```shell
mkdir -p /mnt/data-docker
```

### Validando o ponto de montagem

```bash
mount -v -t nfs 10.0.0.60:/data/data-docker /mnt/data-docker
```

### Verifique se funcionou

```bash
df -h
Filesystem                       Size  Used Avail Use% Mounted on
devtmpfs                         7.8G     0  7.8G   0% /dev
tmpfs                            7.8G     0  7.8G   0% /dev/shm
tmpfs                            7.8G   20M  7.8G   1% /run
tmpfs                            7.8G     0  7.8G   0% /sys/fs/cgroup
/dev/mapper/centos-root           44G  2.2G   42G   5% /
/dev/sda1                       1014M  173M  842M  17% /boot
/dev/mapper/VG_DOCKER-LV_DOCKER   49G   53M   46G   1% /var/lib/docker
tmpfs                            1.6G     0  1.6G   0% /run/user/0
**10.0.0.60:/data/data-docker    99G   60M   94G   1% /mnt/data-docker**
```

### Removendo ponto de montagem temporario

```bash
umount /mnt/data-docker
```

### Configurando ponto de montagem permanente

```bash
vim /etc/fstab
10.0.0.60:/data/data-docker   /mnt/data-docker    nfs    defaults    0 0 
```

### Force o reload do fstab

```bash
sudo mount -av
/                        : ignored
/boot                    : already mounted
swap                     : ignored
/var/lib/docker          : already mounted
/mnt/data-docker         : already mounted
```

