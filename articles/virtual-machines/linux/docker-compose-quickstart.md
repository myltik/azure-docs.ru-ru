---
title: "Использование Docker Compose на виртуальной машине Linux в Azure | Документация Майкрософт"
description: "Как использовать Docker и Compose на виртуальных машинах Linux с помощью Azure CLI"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 69c7f3b468e489f53d8ed89b533aeb30c244dd0f
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Приступая к работе с Docker и Compose для определения и запуска многоконтейнерного приложения в Azure
Решение [Compose](http://github.com/docker/compose) позволяет определить приложение, состоящее из нескольких контейнеров Docker, с помощью простого текстового файла. После этого приложение будет развернуто с помощью одной команды, которая выполняет все действия, необходимые для развертывания определенной среды. В этой статье показано, как быстро настроить блог WordPress с серверной базой данных SQL MariaDB на виртуальной машине Ubuntu. Решение Compose можно использовать для настройки и более сложных приложений.

## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Шаг 1. Настройка виртуальной машины Linux как узла Docker
Чтобы создать виртуальную машину Linux и настроить ее как узел Docker, можно использовать различные процедуры Azure и образы или шаблоны Resource Manager, доступные в Azure Marketplace. Например, в статье [Использование расширения виртуальной машины Docker для развертывания среды](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) описано быстрое создание виртуальной машины Ubuntu с расширением виртуальной машины Azure Docker с помощью [шаблона быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

При использовании этого расширения Docker виртуальная машина настраивается как узел Docker и компонент Compose устанавливается автоматически. Можно создать виртуальную машину и использовать расширение виртуальной машины Docker с помощью одной из следующих версий интерфейса командной строки:

- [Azure CLI 2.0](#azure-cli-20) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.
- [Azure CLI 1.0](#azure-cli-10) — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/#login).

Сначала создайте группу ресурсов для среды Docker командой [az group create](/cli/azure/group#create). В следующем примере создается группа ресурсов с именем `myResourceGroup` в расположении `West US`:

```azurecli
az group create --name myResourceGroup --location westus
```

Затем, выполнив команду [az group deployment create](/cli/azure/group/deployment#create), разверните виртуальную машину с расширением виртуальной машины Docker для Azure с помощью [этого шаблона Azure Resource Manager из репозитория GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). Введите собственные значения `newStorageAccountName`, `adminUsername`, `adminPassword` и `dnsNameForPublicIP`.

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"newStorageAccountName": {"value": "mystorageaccount"},
    "adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "dnsNameForPublicIP": {"value": "mypublicdns"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Подождите несколько минут до завершения развертывания. После завершения развертывания [перейдите к следующему шагу](#step-2-verify-that-compose-is-installed), чтобы подключиться к виртуальной машине по протоколу SSH. 

При необходимости, чтобы вернуть управление командной строке и позволить развертыванию работать в фоновом режиме, добавьте в предыдущую команду флаг `--no-wait`. Этот процесс позволит выполнять другую работу в интерфейсе командной строки, пока в течение нескольких минут будет продолжаться развертывание. После этого можно будет просмотреть сведения о состоянии узла Docker с помощью команды [az vm show](/cli/azure/vm#show). В следующем примере проверяется состояние виртуальной машины `myDockerVM` (это имя по умолчанию, указанное в шаблоне; не изменяйте его) в группе ресурсов `myResourceGroup`.

```azurecli
az vm show --resource-group myResourceGroup --name myDockerVM \
  --query [provisioningState] --output tsv
```

Если эта команда возвращает `Succeeded`, значит, развертывание завершено, и вы сможете установить подключение SSH к виртуальной машине на следующем шаге.

### <a name="azure-cli-10"></a>Azure CLI 1.0
Установите последнюю версию [Azure CLI 1.0](../../cli-install-nodejs.md) и войдите в систему с учетной записью Azure. Убедитесь, что для создания виртуальной машины используется режим Resource Manager (`azure config mode arm`).

Приведенный ниже пример создает группу ресурсов `myResourceGroup` в расположении `West US` и развертывает виртуальную машину с расширением виртуальной машины Docker для Azure. [Шаблон Azure Resource Manager из репозитория GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu) используется для развертывания среды.

```azurecli
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Azure CLI отобразит командную строку всего через несколько секунд, но создание и настройка узла Docker еще не завершены. Подождите несколько минут до завершения развертывания. Сведения о состоянии узла Docker можно просмотреть с помощью команды `azure vm show`. В следующем примере проверяется состояние виртуальной машины с именем `myDockerVM` (имя по умолчанию, указанное в шаблоне; не изменяйте его) в группе ресурсов `myResourceGroup`. Введите имя группы ресурсов, созданной на предыдущем шаге.

```azurecli
azure vm show --resource-group myResourceGroup --name myDockerVM
```

## <a name="step-2-verify-that-compose-is-installed"></a>Шаг 2. Проверка установки Compose
После завершения развертывания подключитесь к новому узлу Docker с помощью SSH, используя DNS-имя, которое вы указали во время развертывания. Для просмотра сведений о виртуальной машине, включая DNS-имя, можно использовать команду `azure vm show -g myResourceGroup -n myDockerVM` (Azure CLI 1.0) или `az vm show -g myResourceGroup -n myDockerVM -d --query [fqdns] -o tsv` (Azure CLI 2.0).

Чтобы проверить установку Compose на виртуальной машине, выполните следующую команду:

```bash
docker-compose --version
```

Должен появиться примерно такой результат: `docker-compose 1.6.2, build 4d72027`.

> [!TIP]
> Если использован другой способ создания узла Docker и необходимо самостоятельно установить Compose, ознакомьтесь с [документацией по Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Шаг 3. Создание файла конфигурации docker-compose.yml
Теперь необходимо создать файл `docker-compose.yml` (простой текстовый файл конфигурации), определяющий контейнеры Docker для запуска на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере (или сборка из Dockerfile), необходимые переменные и зависимости среды, порты и ссылки между контейнерами. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу Compose](http://docs.docker.com/compose/yml/).

Создайте файл `docker-compose.yml` следующим образом:

```bash
touch docker-compose.yml
```

Чтобы добавить в файл данные, используйте удобный для вас текстовый редактор. В следующем примере используется редактор `vi`.

```bash
vi docker-compose.yml
```

Вставьте следующий пример в текстовый файл. Эта конфигурация обеспечивает установку WordPress (системы для управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных SQL MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/) . Введите собственное значение `MYSQL_ROOT_PASSWORD` следующим образом.

```sh
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Шаг 4. Запуск контейнеров с Compose
В том же каталоге, где находится файл `docker-compose.yml`, выполните следующую команду (в зависимости от среды, может потребоваться выполнить `docker-compose` с помощью `sudo`):

```bash
docker-compose up -d
```

Эта команда запускает контейнеры Docker, указанные в `docker-compose.yml`. Выполнение этого этапа занимает одну-две минуты. Результат должен выглядеть примерно так:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> При запуске обязательно используйте параметр **-d** , чтобы контейнеры непрерывно выполнялись в фоновом режиме.


Чтобы убедиться, что контейнеры работоспособны, введите `docker-compose ps`. Вы увидите нечто вроде этого:

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Теперь можно подключиться непосредственно к WordPress на виртуальной машине через порт 80. Откройте веб-браузер и введите DNS-имя виртуальной машины (например, `http://myresourcegroup.westus.cloudapp.azure.com`). Откроется начальный экран WordPress, позволяющий завершить установку и начать работу с приложением.

![Начальный экран WordPress][wordpress_start]

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные возможности настройки Docker и Compose на виртуальной машине Docker см. в [руководстве пользователя по расширению виртуальной машины Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md). Например, один из вариантов — поместить YML-файл Compose (преобразованный в формат JSON) непосредственно в конфигурацию расширения виртуальной машины Docker.
* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справочнике по командной строке Compose](http://docs.docker.com/compose/reference/) и [руководстве пользователя](http://docs.docker.com/compose/).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](https://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например, в шаблоне [Развертывание блога WordPress с помощью Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) решения Docker и Compose используются для быстрого развертывания WordPress с серверной базой данных MySQL на виртуальной машине Ubuntu.
* Попытайтесь интегрировать Docker Compose с кластером Docker Swarm. Сценарии приведены в статье [Using Swarm with Compose](https://docs.docker.com/compose/swarm/) (Совместное использование Compose и Swarm).

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png

