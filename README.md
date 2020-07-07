# Curso Zabbix 5.0 - Zabbix em Docker SWARM com alta disponibilidade

Este repositório contem os arquivos do Curso de Zabbix 5.0 da JLCP. 

Aqui estamos montando um deploy do Zabbix 5 com 6 Máquinas:
	* Banco de dados Mysql no CentOS8
	* HA Proxy no CentOS8
	* NFS no CentOS8
	* Três nós master do SWARM com CentOs8

No SWARM Estão rodando os Serviços
	* Zabbix Server (1 instancia)
	* Zabbix Front-End (1 instancia)
	* Grafana (1 instancia)
	* Traefik (3 Instancias)
