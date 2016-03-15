<properties
   pageTitle="Подключение к кластеру службы контейнеров Azure | Microsoft Azure"
   description="Подключение к кластеру службы контейнеров Azure с помощью туннеля SSH."
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

Кластеры Mesos и Swarm, развернутые с помощью службы контейнера Azure, предоставляют конечные точки REST, но эти конечные точки недоступны из внешнего мира. Для управления этими конечными точками необходимо создать туннель SSH. После создания туннеля SSH можно выполнить команды к конечным точкам кластера и просмотреть пользовательский интерфейс кластера через браузер на вашем компьютере. В этом документе приводятся инструкции по созданию туннеля SSH в Linux, OSX и Windows.

> Можно создать сеанс SSH, используя систему управления кластером, хотя это и не рекомендуется. Работа непосредственно в системе управления сопряжена с риском случайного изменения конфигурации.

## Создание туннеля SSH в Linux и OSX

Во-первых, вам нужно узнать общедоступное DNS-имя образцов с балансировкой нагрузки. Для этого разверните группу ресурсов, чтобы отобразить все ресурсы. Найдите и выберите общедоступный IP-адрес образца. Откроется колонка со сведениями об общедоступном IP-адресе, который включает DNS-имя. Сохраните это имя для последующего использования. <br />

![Подключение Putty](media/pubdns.png)

Теперь откройте оболочку и выполните следующую команду, где:

**PORT** — это порт конечной точки, к которой требуется предоставить доступ. Для Swarm это 2375, для Mesos используйте порт 80. **USERNAME** — имя пользователя, указанное при развертывании кластера. **DNSPREFIX** — префикс DNS, указанный при развертывании кластера. **REGION** — область, в которой расположена ваша группа ресурсов.

```
ssh -L PORT:localhost:PORT -N [USERNAME]@[DNSPREFIX]man.[REGION].cloudapp.azure.com -p 2200
```
### Туннель Mesos

Чтобы создать туннель к связанным с Mesos конечным точкам, выполните следующую команду.

```
ssh -L 80:localhost:80 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Связанные с Mesos конечные точки теперь доступны по адресу:

- Mesos — `http://localhost/mesos`;
- Marathon — `http://localhost/marathon`;
- Chronos — `http://localhost/chronos`. 

Аналогичным образом и другие API для каждого приложения доступны через туннель Marathon — `http://localhost/marathon/v2`. Дополнительные сведения о различных доступных API см. в документации Mesosphere по [API для Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) и [API для Chronos](https://mesos.github.io/chronos/docs/api.html), а также в документации Apache по [API для планировщика Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

### Туннель Swarm

Чтобы открыть туннель для конечной точки Swarm, выполните следующую команду.

```
ssh -L 2375:localhost:2375 -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

Теперь можно следующим образом задать переменную среды DOCKER\_HOST и продолжать использовать интерфейс командной строки Docker в обычном режиме.

```
export DOCKER_HOST=:2375
```

## Создание туннеля SSH в Windows

Доступно несколько вариантов создания туннеля SSH в Windows; в этом документе описывается вариант с использованием Putty.

Загрузите Putty в операционную систему Windows и запустите приложение.

Введите имя узла, которое состоит из имени администратора кластеров и общедоступное DNS-имя первого образца в кластере. Имя узла будет выглядеть так: `adminuser@PublicDNS`. Введите 2200 в качестве значения порта.

![Подключение Putty](media/putty1.png)

Выберите `SSH` и `Authentication`, а затем добавьте файл закрытого ключа для проверки подлинности.

![Подключение Putty](media/putty2.png)

Выберите в `Tunnels` и `configure` следующие порты для перенаправления:
- **Порт источника:** выберите желаемый порт (80 для Mesos и 2375 для Swarm).
- **Назначение:** localhost:80 (для Mesos) или localhost:2375 (для Swarm)

В следующем примере выполняется настройка для Mesos; для Docker Swarm эта процедура аналогична.

> Порт 80 не должен использоваться при создании этого туннеля.

![Подключение Putty](media/putty3.png)

После завершения сохранения конфигурацию подключения и откройте сеанс подключения Putty. После подключения конфигурацию порта можно просмотреть в журнале событий Putty.

![Подключение Putty](media/putty4.png)

При настройке туннеля Mesos связанная конечная точка доступна по адресу:

- Mesos — `http://localhost/mesos`;
- Marathon — `http://localhost/marathon`;
- Chronos — `http://localhost/chronos`. 

При настройке туннеля для Docker Swarm доступ к кластеру Swarm может осуществляться через интерфейс командной строки Docker. Сначала необходимо настроить переменную среды Windows с именем `DOCKER_HOST` и значением ` :2375`.

## Дальнейшие действия
 
Развертывание контейнеров и управление ими с помощью Mesos или Swarm.
 
- [Container management with the REST API (Управление контейнерами с помощью REST API).](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0309_2016-->