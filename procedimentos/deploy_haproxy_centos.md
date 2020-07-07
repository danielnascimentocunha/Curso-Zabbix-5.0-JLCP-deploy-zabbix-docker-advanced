# Instalação HA-PROXY no Centos 8

## HA-PROXY

https://upcloud.com/community/tutorials/haproxy-load-balancer-centos/
https://medium.com/@warlord77/haproxy-2-0-centos-7-setup-6ca655bec840

### Instalar pré requisitos

```bash
dnf install -y net-tools vim nano epel-release wget curl tcpdump
dnf install -y gcc pcre-devel tar make
```


### Baixar HA PROXY 2.0

```bash
cd /tmp
wget http://www.haproxy.org/download/2.0/src/haproxy-2.0.14.tar.gz -O haproxy.tar.gz
```

### Descompactar e compilar

```bash
tar xzvf haproxy.tar.gz -C /tmp
cd haproxy-2.0.14/
make TARGET=linux-glibc
make install
```

### Configurações finais

```bash
sudo mkdir -p /etc/haproxy
sudo mkdir -p /var/lib/haproxy 
sudo touch /var/lib/haproxy/stats
sudo ln -s /usr/local/sbin/haproxy /usr/sbin/haproxy
sudo cp examples/haproxy.init /etc/init.d/haproxy
sudo chmod 755 /etc/init.d/haproxy
sudo systemctl daemon-reload
sudo chkconfig haproxy on
sudo useradd -r haproxy
haproxy -v
```

### Configurar HAPROXY
```bash
cd /etc/haproxy/
vim haproxy.cfg
```

### Validar arquivo de conf

```bash
haproxy -f haproxy.cfg
```

### Criar regras de firewall

```bash
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=10051/tcp
firewall-cmd --reload
```

### Remover execução manual

```bash
ps aux |grep haproxy
kill -9 PID_HAPROXY
```

### Configurar iniciar o serviço

```bash
systemctl start haproxy
systemctl status haproxy
```

### Acessar interface web

```bash
http://10.0.0.59/admin?stats

username: admin
password: password
```

