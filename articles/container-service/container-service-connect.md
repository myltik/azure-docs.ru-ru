<properties
   pageTitle="Подключение к кластеру службы контейнеров Azure | Microsoft Azure"
   description="Сведения о подключении к кластеру службы контейнеров Azure с использованием туннеля SSH."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, контейнеры, микрослужбы, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>


# Подключение к кластеру службы контейнеров Azure

Кластеры Mesos и Swarm, развернутые с помощью службы контейнеров Azure, предоставляют конечные точки REST. Но эти конечные точки не открыты для внешнего использования. Чтобы управлять этими конечными точками, необходимо создать туннель Secure Shell (SSH). После создания туннеля SSH можно выполнить команды к конечным точкам кластера и просмотреть пользовательский интерфейс кластера через браузер на вашем компьютере. В этом документе приводятся инструкции по созданию туннеля SSH в ОС Linux, OS X и Windows.

>[AZURE.NOTE] Вы можете создать сеанс SSH, используя систему управления кластером, но это не рекомендуется. Работа напрямую в системе управления сопряжена с риском случайного изменения конфигурации.

## Создание туннеля SSH в Linux или OS X

Во-первых, чтобы создать туннель SSH в любой из этих операционных систем, вам нужно узнать общедоступное DNS-имя главного сервера с балансировкой нагрузки. Для этого разверните группу ресурсов, чтобы отображались все ресурсы. Найдите и выберите общедоступный IP-адрес образца. Откроется колонка со сведениями об общедоступном IP-адресе, в которой также будет DNS-имя. Сохраните это имя для последующего использования. <br />

![Общедоступное DNS-имя](media/pubdns.png)

Теперь откройте оболочку и выполните следующую команду, где:

**PORT** — это порт конечной точки, к которой требуется предоставить доступ. Для Swarm это порт 2375, а для Mesos — 80. **USERNAME** — имя пользователя, указанное при развертывании кластера. **DNSPREFIX** — префикс DNS, указанный при развертывании кластера. **REGION** — регион, где расположена ваша группа ресурсов.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Туннель Mesos

Чтобы создать туннель к связанным с Mesos конечным точкам, выполните следующую команду:

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Связанные с Mesos конечные точки теперь доступны по адресу:

- Mesos — `http://localhost/mesos`;
- Marathon — `http://localhost/marathon`;
- Chronos — `http://localhost/chronos`.

Аналогичным образом можно обеспечить доступ и к другим API для каждого приложения через туннель Marathon `http://localhost/marathon/v2`. Дополнительные сведения о различных доступных API см. в документации по Mesosphere в разделах о [API для Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) и [API для Chronos](https://mesos.github.io/chronos/docs/api.html), а также в документации по Apache в разделе [API для планировщика Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Туннель Swarm

Чтобы открыть туннель для конечной точки Swarm, выполните следующую команду:

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Теперь можно следующим образом задать переменную среды DOCKER\_HOST и дальше использовать интерфейс командной строки Docker в обычном режиме.

```
export DOCKER_HOST=:2375
```

## Создание туннеля SSH в Windows

Создавать туннели SSH в Windows можно несколькими способами. В этом документе описывается вариант с использованием PuTTY.

Скачайте PuTTY в операционную систему Windows и запустите приложение.

Введите имя узла, которое состоит из имени администратора кластера и общедоступного DNS-имени первого главного сервера в кластере. **Имя узла** будет выглядеть так: `adminuser@PublicDNS`. Введите значение 2200 в поле **Порт**.

![Конфигурация PuTTY 1](media/putty1.png)

Выберите `SSH` и `Authentication`. Выберите файл закрытого ключа для проверки подлинности.

![Конфигурация PuTTY 2](media/putty2.png)

Выберите `Tunnels` и настройте следующие порты для перенаправления:
- **порт источника** — выберите порт 80 для Mesos и 2375 для Swarm;
- **порт назначения** — localhost:80 для Mesos или localhost:2375 (для Swarm).

В следующем примере выполняется настройка для Mesos. Для Docker Swarm эта процедура аналогична.

>[AZURE.NOTE] При создании этого туннеля нужно, чтобы порт 80 больше нигде не использовался.

![Конфигурация PuTTY 3](media/putty3.png)

По завершении сохраните конфигурацию подключения и откройте сеанс подключения PuTTY. После подключения конфигурацию порта можно просмотреть в журнале событий PuTTY.

![Журнал событий PuTTY](media/putty4.png)

После настройки туннеля для Mesos связанная конечная точка будет доступна по адресу:

- Mesos — `http://localhost/mesos`;
- Marathon — `http://localhost/marathon`;
- Chronos — `http://localhost/chronos`.

После настройки туннеля для Docker Swarm доступ к кластеру Swarm можно получать через интерфейс командной строки Docker. Сначала необходимо настроить переменную среды Windows с именем `DOCKER_HOST` и значением ` :2375`.

## Устранение неполадок

### После создания туннеля и перехода по ссылке на Mesos или Marathon появляется ошибка 502 "Недопустимый шлюз"
Самый простой способ решить эту проблему — удалить кластер и развернуть его повторно. Кроме того, можно принудительно заставить Zookeeper выполнить автоматическое восстановление. Для этого сделайте следующее:

Войдите на каждый главный сервер и выполните следующие команды:

```
sudo service nginx stop
sudo service marathon stop
sudo service chronos stop
sudo service mesos-dns stop
sudo service mesos-master stop 
sudo service zookeeper stop
```

Затем, когда на всех главных серверах все службы будут остановлены, выполните следующие команды:
```
sudo mkdir /var/lib/zookeeperbackup
sudo mv /var/lib/zookeeper/* /var/lib/zookeeperbackup
sudo service zookeeper start
sudo service mesos-master start
sudo service mesos-dns start
sudo service chronos start
sudo service marathon start
sudo service nginx start
```
Когда все службы перезапустятся, вы сможете работать с кластером, как описано в документации.

## Дальнейшие действия

Развертывание контейнеров и управление ими с помощью Mesos или Swarm.

- [Работа со службой контейнеров Azure и Mesos](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0406_2016-->