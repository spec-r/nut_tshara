# nut_tshara
Configuração NUT do TS-Shara UPS PRO 2000

## Configuração NUT no Debian 11

- Objetivo: Monitoramento do Nobreak TS-Shara UPS PRO 2000
- Como o programa disponibilizado pela TS SHARA - POWER NT 2.2 não fica mais de algumas horas sem travar, busquei alternativas para monitoramento contínuo

## Requisitos

- Cabo USB para conectar o nobreak no servidor
- Debian 11
- NUT Server

## Instalação NUT

Instalação **NUT**

	apt-get install nut-client nut-server

Adicionar na última linha do **/etc/nut/nut.conf**

	MODE=netserver

Adicionar na última linha do **/etc/nut/ups.conf**
	
	[tsshara]
        	driver = nutdrv_qx
        	desc = "tsshara"
        	port = auto
        	protocol = "megatec"

 Adicionar na última linha do **/etc/nut/upsd.users**
 
	[upsmon]
        	password = "123456"
        	upsmon master
        	actions = SET
        	instcmds = ALL

Adicionar na última linha do **/etc/nut/upsmon.conf**

	MONITOR tsshara@localhost 1 upsmon "123456" master
	NOTIFYFLAG ONBATT SYSLOG+WALL+EXEC
	NOTIFYFLAG ONLINE SYSLOG+WALL+EXEC

Teste do driver. Ele deve mostrar algum erro se não funcionar.

	upsdrvctl start

Reinicie o servidor e depois teste se está comunicando

	upsc tsshara

Se estiver funcionando, deve aparecer algo assim:
	
	Init SSL without certificate database
	battery.charge: 92
	battery.voltage: 12.80
	battery.voltage.high: 13.00
	battery.voltage.low: 10.40
	battery.voltage.nominal: 12.0
	device.mfr: UPS  700 VA AUT
	device.model: TS Shara
	device.type: ups
	driver.name: nutdrv_qx
	driver.parameter.pollfreq: 30
	driver.parameter.pollinterval: 2
	driver.parameter.port: auto
	driver.parameter.protocol: megatec
	driver.parameter.synchronous: no
	driver.version: 2.7.4
	driver.version.data: Megatec 0.06
	driver.version.internal: 0.28
	input.current.nominal: 4.0
	input.frequency: 60.0
	input.frequency.nominal: 60
	input.voltage: 0.0
	input.voltage.fault: 0.0
	input.voltage.nominal: 115
	output.voltage: 111.0
	ups.beeper.status: enabled
	ups.delay.shutdown: 30
	ups.delay.start: 180
	ups.firmware: 20070731-5
	ups.productid: 0035
	ups.status: OB
	ups.type: offline / line interactive
	ups.vendorid: 0483
	
Alterar a no **/etc/nut/upsd.conf** a linha **LISTEN 127.0.0.1 3493**

	LISTEN 0.0.0.0 3493 

## Instalação NUT CGI Server

	apt install apache2 nut-cgi

Adicionar na última linha do **/etc/nut/hosts.conf**

	MONITOR tsshara@localhost "TS-Shara UPS PRO 2000"

Executar o comando:

	a2enmod cgi
 	systemctl restart apache2

Retirar os comentários no **/etc/nut/upsset.conf** da linha:

	I_HAVE_SECURED_MY_CGI_DIRECTORY

 Se tudo ocorreu com sucesso, acesse o link

	http://ip_do_debian/cgi-bin/nut/upsstats.cgi

Fontes de pesquisa:

http://mylowtechstuff.blogspot.com/2018/02/utilizando-nut-para-controle-de-nobreak.html <br />
https://technotim.live/posts/NUT-server-guide/
 
