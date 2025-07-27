# Домашнее задание к занятию "Disaster recovery и Keepalived" - `Важничев Георгий`


### Задание 1

1. `Дана схема для Cisco Packet Tracer, рассматриваемая в лекции.`
2. `На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)`
3. `Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).`
4. `Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.`
5. `На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.`

#### Решение:
##### pkt: 
[hsrp_advanced-10-hw.pkt](https://github.com/vajnichev/10-01-hw/blob/main/hsrp_advanced-10-hw.pkt) 
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.1.png)

### Задание 2

1. `Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции, используя пример конфигурационного файла.`
2. `Настройте любой веб-сервер (например, nginx или simple python server) на двух виртуальных машинах`
3. `Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.`
4. `Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script`
5. `На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html`
#### Решение:
#### Bash-скрипт:
[check_server.sh](https://github.com/vajnichev/10-01-hw/blob/main/check_server.sh)
```bash
#!/bin/bash
if [[ $(netstat -ant | grep LISTEN | grep :80) ]] && [[ -f /var/www/html/index.nginx-debian.html ]]; then
  exit 0
else
  sudo systemctl stop keepalived
fi
```
#### Конфиг файл MASTER :
[keepalived1.conf](https://github.com/vajnichev/10-01-hw/blob/main/keepalived1.conf)
```bash
vrrp_script check_server {
        script "/home/jora/vajnichev/check_server.sh"
        interval 3
}

vrrp_instance VI_1 {
        state MASTER
        interface enp0s3
        virtual_router_id 20
        priority 255
        advert_int 1

        virtual_ipaddress {
                192.168.126.20/24
        }

        track_script {
                check_server
        }

}
```

#### Конфиг файл BACKUP
[keepalived2.conf](https://github.com/vajnichev/10-01-hw/blob/main/keepalived2.conf)

```bash
vrrp_instance VI_1 {
	state BACKUP
	interface enp0s3
	virtual_router_id 20
	priority 160
	advert_int 1

	virtual_ipaddress {
		192.168.126.20/24
	}

}

```
`Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля (то есть порт веб-сервера был недоступен или отсутствовал index.html). Используйте для этого секцию vrrp_script`

![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.2.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.3.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.4.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.5.png)

`На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html`
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.6.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.7.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.8.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.9.png)
![png](https://github.com/vajnichev/10-01-hw/blob/main/img/10.0.10.png)
