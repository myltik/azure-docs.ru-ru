---
title: Использование Docker Compose на виртуальной машине Linux в Azure | Документация Майкрософт
description: Как использовать Docker и Compose на виртуальных машинах Linux с помощью Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: iainfou
ms.openlocfilehash: 387f3095e8eebce3fa6c2f47ffc87995e65bfe2b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29848412"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Приступая к работе с Docker и Compose для определения и запуска многоконтейнерного приложения в Azure
Решение [Compose](http://github.com/docker/compose) позволяет определить приложение, состоящее из нескольких контейнеров Docker, с помощью простого текстового файла. После этого приложение будет развернуто с помощью одной команды, которая выполняет все действия, необходимые для развертывания определенной среды. В этой статье показано, как быстро настроить блог WordPress с серверной базой данных SQL MariaDB на виртуальной машине Ubuntu. Решение Compose можно использовать для настройки и более сложных приложений.


## <a name="set-up-a-linux-vm-as-a-docker-host"></a>Настройка виртуальной машины Linux как узла Docker
Чтобы создать виртуальную машину Linux и настроить ее как узел Docker, можно использовать различные процедуры Azure и образы или шаблоны Resource Manager, доступные в Azure Marketplace. Например, в статье [Использование расширения виртуальной машины Docker для развертывания среды](dockerextension.md) описано быстрое создание виртуальной машины Ubuntu с расширением виртуальной машины Azure Docker с помощью [шаблона быстрого запуска](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

При использовании этого расширения Docker виртуальная машина настраивается как узел Docker и компонент Compose устанавливается автоматически.


### <a name="create-docker-host-with-azure-cli-20"></a>Создание узлов Docker с помощью Azure CLI 2.0
Установите последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войдите в систему с учетной записью Azure, выполнив команду [az login](/cli/azure/reference-index#az_login).

Сначала создайте группу ресурсов для среды Docker командой [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Затем, выполнив команду [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create), разверните виртуальную машину с расширением виртуальной машины Docker для Azure с помощью [этого шаблона Azure Resource Manager из репозитория GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). При запросе укажите собственные уникальные значения для параметров *newStorageAccountName*, *adminUsername*, *adminPassword* и *dnsNameForPublicIP*:

```azurecli
az group deployment create --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

Подождите несколько минут до завершения развертывания.


## <a name="verify-that-compose-is-installed"></a>Проверка установки Compose
Для просмотра сведений о виртуальной машине, включая DNS-имя, можно использовать команду [az vm show](/cli/azure/vm#az_vm_show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --show-details \
    --query [fqdns] \
    --output tsv
```

Подключитесь к новому узлу Docker по протоколу SSH. Укажите имя пользователя и DNS-имя, созданное на предыдущих шагах:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Чтобы проверить установку Compose на виртуальной машине, выполните следующую команду:

```bash
docker-compose --version
```

Вы увидите что-то похожее: *docker-compose 1.6.2, build 4d72027*.

> [!TIP]
> Если использован другой способ создания узла Docker и необходимо самостоятельно установить Compose, ознакомьтесь с [документацией по Compose](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="create-a-docker-composeyml-configuration-file"></a>Создание файла конфигурации docker-compose.yml
Теперь необходимо создать файл `docker-compose.yml` (простой текстовый файл конфигурации), определяющий контейнеры Docker для запуска на виртуальной машине. В файле указывается образ для выполнения в каждом контейнере (или сборка из Dockerfile), необходимые переменные и зависимости среды, порты и ссылки между контейнерами. Дополнительные сведения о синтаксисе YML-файла приведены в [справке по файлу Compose](https://docs.docker.com/compose/compose-file/).

Создайте файл *docker-compose.yml*. Чтобы добавить в файл данные, используйте удобный для вас текстовый редактор. В следующем примере создается файл с запросом для `sensible-editor` на выбор редактора, который вы хотите использовать:

```bash
sensible-editor docker-compose.yml
```

Вставьте следующий пример в файл Docker Compose. Эта конфигурация обеспечивает установку WordPress (системы для управления блогами и содержимым с открытым исходным кодом) и связанной серверной базы данных SQL MariaDB с использованием образов из [реестра DockerHub](https://registry.hub.docker.com/_/wordpress/) . Введите собственное значение *MYSQL_ROOT_PASSWORD* следующим образом:

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

## <a name="start-the-containers-with-compose"></a>Запуск контейнеров с использованием Compose
В том же каталоге, где находится файл *docker-compose.yml*, выполните следующую команду (в зависимости от среды может потребоваться выполнить `docker-compose` с помощью `sudo`):

```bash
docker-compose up -d
```

Эта команда запускает контейнеры Docker, указанные в файле *docker-compose.yml*. Выполнение этого этапа занимает одну-две минуты. Результат должен выглядеть примерно так:

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

> [!NOTE]
> При запуске обязательно используйте параметр **-d** , чтобы контейнеры непрерывно выполнялись в фоновом режиме.


Чтобы убедиться, что контейнеры работоспособны, введите `docker-compose ps`. Вы увидите нечто вроде этого:

```bash
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Теперь можно подключиться непосредственно к WordPress на виртуальной машине через порт 80. Откройте веб-браузер и введите DNS-имя виртуальной машины (например, `http://mypublicdns.eastus.cloudapp.azure.com`). Откроется начальный экран WordPress, позволяющий завершить установку и начать работу с приложением.

![Начальный экран WordPress][wordpress_start]

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные возможности настройки Docker и Compose на виртуальной машине Docker см. в [руководстве пользователя по расширению виртуальной машины Docker](https://github.com/Azure/azure-docker-extension/blob/master/README.md). Например, один из вариантов — поместить YML-файл Compose (преобразованный в формат JSON) непосредственно в конфигурацию расширения виртуальной машины Docker.
* Дополнительные примеры сборки и развертывания многоконтейнерных приложений см. в [справочнике по командной строке Compose](http://docs.docker.com/compose/reference/) и [руководстве пользователя](http://docs.docker.com/compose/).
* Воспользуйтесь шаблоном диспетчера ресурсов Azure (собственным или полученным из [сообщества](https://azure.microsoft.com/documentation/templates/)), чтобы развернуть виртуальную машину Azure с Docker и настроить приложение при помощи решения Compose. Например, в шаблоне [Развертывание блога WordPress с помощью Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) решения Docker и Compose используются для быстрого развертывания WordPress с серверной базой данных MySQL на виртуальной машине Ubuntu.
* Попытайтесь интегрировать Docker Compose с кластером Docker Swarm. Сценарии приведены в статье [Using Swarm with Compose](https://docs.docker.com/compose/swarm/) (Совместное использование Compose и Swarm).

<!--Image references-->

[wordpress_start]: media/docker-compose-quickstart/WordPress.png
