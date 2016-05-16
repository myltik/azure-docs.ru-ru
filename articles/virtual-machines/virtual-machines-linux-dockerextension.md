<properties
   pageTitle="Дополнительные сведения о расширении виртуальной машины Docker в Azure | Microsoft Azure"
   description="Сведения об использовании расширения виртуальной машины Docker для быстрого и безопасного развертывания среды Docker в Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="04/27/2016"
   ms.author="iainfou"/>

# Использование расширения виртуальной машины Docker для развертывания среды

Docker — это популярная платформа управления контейнерами и работы с образами, которая позволяет быстро работать с контейнерами в Linux (а также в Windows). В Azure вы можете выполнить развертывание Docker несколькими разными способами в зависимости от потребностей.

- Если вам требуется быстро создать прототип приложения, а также если вы знакомы с принципами использования Docker Machine, можно [использовать драйвер Azure для Docker Machine](./virtual-machines-linux-docker-machine.md) для настройки узлов Docker в Azure.
- Чтобы получить дополнительный контроль над развертыванием среды Docker, реализуя воспроизводимый, основанный на шаблонах Azure подход, включающий элементы управления доступом на основе ролей, диагностику, а также регистрируемые в журналах и управляемые развертывания, лучшим решением будет использование расширения виртуальной машины Docker Azure с Azure Resource Manager.
- Кроме того, можно [развернуть полный кластер Docker Swarm в службах контейнеров Azure](../container-service/container-service-deployment.md), чтобы получить готовые к работе, масштабируемые развертывания, использующие дополнительные средства планирования и управления, предоставляемые Swarm.

Эта статья посвящена использованию шаблонов диспетчера ресурсов для развертывания расширения виртуальной машины Docker в настраиваемой, готовой к работе среде.

## Расширение виртуальной машины Docker Azure для развертывания шаблонов

Расширение виртуальной машины Docker Azure устанавливает Docker на виртуальной машине Linux и выполняет ее настройку. Используя шаблоны диспетчера ресурсов, затем можно выполнить согласованное повторное развертывание среды. Расширение виртуальной машины Docker Azure можно использовать для создания более надежной рабочей среды или среды разработки благодаря наличию некоторых дополнительных элементов управления, как для простого использования Docker Machine, так и для самостоятельного создания узла Docker.

С помощью Azure Resource Manager можно создать и развернуть шаблоны, определяющие структуру всей среды, включая узлы Docker, хранилище, управление доступом на основе ролей (RBAC), диагностику и т. д. Вы можете ознакомиться с [дополнительными сведениями об Azure Resource Manager](../resource-group-overview.md) и шаблонах, чтобы лучше понять некоторые преимущества. Преимущество использования шаблонов Resource Manager по сравнению с использованием только Docker Machine заключается в возможности определить дополнительные узлы Docker, хранилище, управление доступом и прочие компоненты, а также воспроизвести развертывание при необходимости в будущем.

## Развертывание шаблона с помощью расширения виртуальной машины Docker

Чтобы продемонстрировать развертывание виртуальной машины Ubuntu, на которой установлено расширение виртуальной машины Docker, мы используем готовый шаблон быстрого запуска. Шаблон можно найти здесь: [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu).

Разверните шаблон с помощью интерфейса командной строки Azure, указав имя нашей новой группы ресурсов (здесь `myDockerResourceGroup`) и URI шаблона:

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Ответьте на запросы, указав имя учетной записи хранения, DNS-имя, имя пользователя и т. д., а затем подождите несколько минут для завершения развертывания. Вы должны увидеть результат, аналогичный приведенному ниже:

```
info:    Executing command group create
+ Getting resource group myDockerResourceGroup
+ Updating resource group myDockerResourceGroup
info:    Updated resource group myDockerResourceGroup
info:    Supply values for the following parameters
newStorageAccountName: mydockerstorage
adminUsername: ops
adminPassword: P@ssword!
dnsNameForPublicIP: mydockergroup
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/guid/resourceGroups/myDockerResourceGroup
data:    Name:                myDockerResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK

```

## Развертывание первого контейнера nginx

После завершения развертывания подключитесь к новому узлу Docker с помощью SSH, используя DNS-имя, которое вы указали во время развертывания. Средства Docker уже установлены, так что мы можем перейти к настройке контейнера nginx:

```
docker run -d -p 80:80 nginx
```

Вы должны увидеть результат, аналогичный приведенному ниже:

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
a48df1751a97: Pull complete
8ddc2d7beb91: Pull complete
Digest: sha256:2ca2638e55319b7bc0c7d028209ea69b1368e95b01383e66dfe7e4f43780926d
Status: Downloaded newer image for nginx:latest
b6ed109fb743a762ff21a4606dd38d3e5d35aff43fa7f12e8d4ed1d920b0cd74
```

Проверьте выполнение контейнера на узле с помощью `docker ps`:

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   nostalgic_murdock
```

Откройте веб-браузер и введите DNS-имя, указанное во время развертывания, чтобы увидеть контейнер в действии:

![Запущенный контейнер nginx](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

## Пример шаблона JSON расширения виртуальной машины Docker

В этом примере используется шаблон быстрого запуска. Вы можете использовать собственные шаблоны Resource Manager для установки расширения виртуальной машин Docker на виртуальные машины, определенные в шаблоне, добавив в файл определения JSON следующий код:

```
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'), '/DockerExtension'))]",
  "apiVersion": "2015-05-01-preview",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "DockerExtension",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {},
    "protectedSettings": {}
  }
}
```

Более подробное пошаговое руководство по использованию шаблонов Resource Manager см. в статье [Обзор Azure Resource Manager](../resource-group-overview.md).

## Дальнейшие действия

Более подробные инструкции для разных вариантов развертывания см. в следующих статьях:
  1. [Использование машины Docker с драйвером Azure](./virtual-machines-linux-docker-machine.md)
  2. [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)](./virtual-machines-linux-classic-cli-use-docker.md)
  3. [Развертывание кластера службы контейнеров Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0504_2016-->