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
   ms.date="07/20/2016"
   ms.author="iainfou"/>

# Использование расширения виртуальной машины Docker для развертывания среды

> [AZURE.NOTE] Если у вас есть несколько минут, помогите нам улучшить качество документации по виртуальным машинам Linux в Azure, поделившись своими впечатлениями в этом [быстром опросе](https://aka.ms/linuxdocsurvey). Каждый ваш ответ помогает нам совершенствовать средства, необходимые вам для работы.

Docker — это популярная платформа управления контейнерами и работы с образами, которая позволяет быстро работать с контейнерами в Linux (а также в Windows). В Azure вы можете выполнить развертывание Docker несколькими разными способами в зависимости от потребностей.

- Чтобы быстро создать прототип приложения, можно [использовать драйвер Azure для Docker Machine](./virtual-machines-linux-docker-machine.md) для развертывания узлов Docker в Azure.
- Расширение виртуальной машины Docker используется для развертывания виртуальных машин Azure на основе шаблона. Этот подход поддерживает интеграцию с развертыванием шаблонов Azure Resource Manager и предоставляет все связанные с этим преимущества, такие как доступ на основе ролей, диагностика и настройка после развертывания.
- Расширение виртуальной машины Docker также поддерживает Docker Compose. Docker Compose использует декларативный файл YAML, позволяющий использовать смоделированное разработчиком приложение в любой среде и обеспечить согласованное развертывание.
- Кроме того, можно [развернуть полный кластер Docker Swarm в службах контейнеров Azure](../container-service/container-service-deployment.md), чтобы получить готовые к работе, масштабируемые развертывания, использующие дополнительные средства планирования и управления, предоставляемые Swarm.

Эта статья посвящена использованию шаблонов Resource Manager для развертывания расширения виртуальной машины Docker в настраиваемой, готовой к эксплуатации среде.

## Расширение виртуальной машины Docker Azure для развертывания шаблонов

Расширение Docker для виртуальных машин Azure устанавливает и настраивает управляющую программу Docker, клиент Docker и Docker Compose на виртуальной машине Linux. Кроме того, расширение используется для определения и развертывания приложений контейнера с помощью Docker Compose. В этом расширении доступны дополнительные элементы управления для использования Docker Machine и для самостоятельного создания узла Docker. Благодаря этому оно подходит для более надежных сред разработки или рабочих сред.

С помощью Azure Resource Manager можно создать и развернуть шаблоны, определяющие структуру всей среды. Шаблоны позволяют определить узлы Docker, хранилище, элементы управления доступом на основе ролей (RBAC), службу диагностики и т. д. Вы можете ознакомиться с [дополнительными сведениями об Azure Resource Manager](../resource-group-overview.md) и шаблонах, чтобы лучше понять некоторые преимущества. С помощью шаблонов Resource Manager можно будет также воспроизвести развертывания в будущем, если потребуется.

## Развертывание шаблона с помощью расширения виртуальной машины Docker

Чтобы продемонстрировать развертывание виртуальной машины Ubuntu, на которой установлено расширение виртуальной машины Docker, мы используем готовый шаблон быстрого запуска. Шаблон можно найти в разделе [Simple deployment of an Ubuntu VM with Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) (Простое развертывание виртуальной машины Ubuntu с Docker). Также потребуется [последняя версия Azure CLI](../xplat-cli-install.md) в режиме Resource Manager (`azure config mode arm`).

Разверните шаблон с помощью Azure CLI, указав имя новой группы ресурсов (в данном случае это `myDockerResourceGroup`) и универсальный код ресурса (URI) шаблона.

```
azure group create --name myDockerResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

В ответ на запросы укажите имя учетной записи хранения, имя пользователя и пароль, а также DNS-имя. Вы должны увидеть результат, аналогичный приведенному ниже.

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

Azure CLI отобразит командную строку всего через несколько секунд, но в фоновом режиме будет выполняться развертывание шаблона в созданную группу ресурсов. Подождите несколько минут до завершения развертывания, прежде чем попытаться подключиться по протоколу SSH к виртуальной машине.

Сведения о развертывании и DNS-имя виртуальной машины можно получить с помощью команды `azure vm show`. В следующем примере замените `myDockerResourceGroup` именем, указанным на предыдущем шаге.

```bash
azure vm show -g myDockerResourceGroup -n myDockerVM
info:    Executing command vm show
+ Looking up the VM "myDockerVM"
+ Looking up the NIC "myVMNicD"
+ Looking up the public ip "myPublicIPD"
data:    Id                              :/subscriptions/guid/resourceGroups/mydockerresourcegroup/providers/Microsoft.Compute/virtualMachines/MyDockerVM
data:    ProvisioningState               :Succeeded
data:    Name                            :MyDockerVM
data:    Location                        :westus
data:    Type                            :Microsoft.Compute/virtualMachines
data:
data:    Hardware Profile:
data:      Size                          :Standard_F1
data:
data:    Storage Profile:
data:      Image reference:
data:        Publisher                   :Canonical
data:        Offer                       :UbuntuServer
data:        Sku                         :14.04.4-LTS
data:        Version                     :latest
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :osdisk1
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :http://mydockerstorage.blob.core.windows.net/vhds/osdiskfordockersimple.vhd
data:
data:    OS Profile:
data:      Computer Name                 :MyDockerVM
data:      User Name                     :ops
data:      Linux Configuration:
data:        Disable Password Auth       :false
data:
data:    Network Profile:
data:      Network Interfaces:
data:        Network Interface #1:
data:          Primary                   :true
data:          MAC Address               :00-0D-3A-33-D3-95
data:          Provisioning State        :Succeeded
data:          Name                      :myVMNicD
data:          Location                  :westus
data:            Public IP address       :13.91.107.235
data:            FQDN                    :mydockergroup.westus.cloudapp.azure.com
data:
data:    Diagnostics Instance View:
info:    vm show command OK
```

В начале выходных данных отображается `ProvisioningState` виртуальной машины. Если отображается `Succeeded`, значит, развертывание завершено, и вы можете подключиться к виртуальной машине по протоколу SSH.

В конце выходных данных `FQDN` отображает полное доменное имя на основе указанного DNS-имени и расположения, которое вы выбрали. Это полное доменное имя будет использовано для подключения к виртуальной машине по протоколу SSH на последующих шагах.


## Развертывание первого контейнера nginx
После завершения развертывания подключитесь к новому узлу Docker с помощью SSH, используя DNS-имя, которое вы указали во время развертывания. Давайте попробуем запустить контейнер nginx.

```
sudo docker run -d -p 80:80 nginx
```

Вы должны увидеть результат, аналогичный приведенному ниже.

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

Проверьте выполнение контейнера на узле с помощью `sudo docker ps`.

```
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                         NAMES
b6ed109fb743        nginx               "nginx -g 'daemon off"   About a minute ago   Up About a minute   0.0.0.0:80->80/tcp, 443/tcp   adoring_payne
```

Откройте веб-браузер и введите DNS-имя, указанное во время развертывания, чтобы увидеть контейнер в действии.

![Запущенный контейнер nginx](./media/virtual-machines-linux-dockerextension/nginxrunning.png)

Возможно, потребуется настроить TCP-порт и параметры безопасности управляющей программы Docker или развернуть контейнеры с помощью Docker Compose. Чтобы узнать больше, ознакомьтесь с [проектом расширения виртуальной машины Azure для Docker на сайте GitHub](https://github.com/Azure/azure-docker-extension/).

## Пример шаблона JSON расширения виртуальной машины Docker

В этом примере используется шаблон быстрого запуска. Чтобы развернуть расширение виртуальной машины Docker для Azure с помощью собственных шаблонов Resource Manager, добавьте следующий код JSON.

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

Подробное пошаговое руководство по использованию шаблонов Resource Manager см. в статье [Обзор Azure Resource Manager](../resource-group-overview.md).

## Дальнейшие действия

Более подробные инструкции для разных вариантов развертывания см. в следующих статьях:

1. [Использование машины Docker с драйвером Azure](./virtual-machines-linux-docker-machine.md)
2. [Использование расширения виртуальных машин Docker в интерфейсе командной строки Azure (CLI Azure)](./virtual-machines-linux-classic-cli-use-docker.md)
3. [Приступая к работе с решениями Docker и Compose для определения и запуска многоконтейнерного приложения на виртуальной машине Azure](virtual-machines-linux-docker-compose-quickstart.md)
3. [Развертывание кластера службы контейнеров Azure](../container-service/container-service-deployment.md)

<!---HONumber=AcomDC_0921_2016-->