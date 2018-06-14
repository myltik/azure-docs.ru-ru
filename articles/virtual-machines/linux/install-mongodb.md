---
title: Установка MongoDB на виртуальную машину Linux с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как установить и настроить MongoDB на виртуальной машине Linux с помощью Azure CLI 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: b21b33a265d499136dbe3e72538923d8295e9876
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852240"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Как установить и настроить MongoDB на виртуальной машине Linux
[MongoDB](http://www.mongodb.org) — это популярная высокопроизводительная база данных NoSQL с открытым кодом. В этой статье показано, как установить и настроить MongoDB на виртуальной машине Linux с помощью Azure CLI 2.0. Эти действия можно также выполнить с помощью [Azure CLI 1.0](install-mongodb-nodejs.md). Изучив представленные примеры, вы узнаете, как:

* [установить и настроить базовый экземпляр MongoDB вручную](#manually-install-and-configure-mongodb-on-a-vm);
* [создать базовый экземпляр MongoDB на основе шаблона Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template);
* [создать сложный сегментированный кластер MongoDB с набором реплик с использованием шаблона Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template).


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Установка и настройка MongoDB на виртуальной машине вручную
База данных MongoDB [содержит инструкции по установке](https://docs.mongodb.com/manual/administration/install-on-linux/) для дистрибутивов Linux, в том числе Red Hat, CentOS, SUSE, Ubuntu и Debian. В следующем примере создается виртуальная машина *CentOS*. Для создания этой среды необходимо установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login).

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создается виртуальная машина с именем *myVM* и именем пользователя *azureuser*, использующая аутентификацию с открытым ключом SSH.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Подключитесь к виртуальной машине по протоколу SSH с помощью имени пользователя и адреса `publicIpAddress`, указанного в результатах, полученных на предыдущем шаге.

```bash
ssh azureuser@<publicIpAddress>
```

Чтобы добавить источники установки для MongoDB, создайте файл репозитория **yum**, как показано ниже:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Откройте файл репозитория MongoDB для редактирования, например с помощью `vi` или `nano`. Добавьте следующие строки.

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Установите MongoDB, используя **yum**, как показано ниже:

```bash
sudo yum install -y mongodb-org
```

По умолчанию в образах CentOS принудительно используется SELinux. Это препятствует доступу к MongoDB. Установите средства управления политиками и настройте SELinux так, чтобы база данных MongoDB могла использовать TCP-порт 27017 по умолчанию:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Запустите службу MongoDB следующим образом:

```bash
sudo service mongod start
```

Проверьте установленную базы данных MongoDB, подключившись к ней с помощью локального клиента `mongo`:

```bash
mongo
```

Теперь проверьте экземпляр MongoDB, добавив некоторые данные и выполнив их поиск:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

При необходимости настройте автоматический запуск MongoDB при перезагрузке системы:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Создание базового экземпляра MongoDB на виртуальной машине CentOS с использованием шаблона
На виртуальной машине CentOS можно создать базовый экземпляр MongoDB, используя следующий шаблон быстрого запуска Azure из GitHub. В этом шаблоне используется расширение настраиваемых скриптов для Linux, что позволяет добавить в созданную виртуальную машину CentOS репозиторий **yum**, а затем установить MongoDB.

* [Базовый экземпляр MongoDB на виртуальной машине CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json.

Для создания этой среды необходимо установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Затем разверните шаблон MongoDB с помощью команды [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). При появлении запроса введите свои уникальные значения для *newStorageAccountName*, *dnsNameForPublicIP*, а также имя пользователя и пароль администратора:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Войдите на виртуальную машину с помощью ее общедоступного DNS-адреса. Его можно просмотреть с помощью команды [az vm show](/cli/azure/vm#az_vm_show).

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

Подключитесь к виртуальной машине по протоколу SSH с помощью имени пользователя и общедоступного DNS-адреса.

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Проверьте установку базы данных MongoDB, подключившись к ней с помощью локального клиента `mongo`, как показано ниже:

```bash
mongo
```

Теперь проверьте экземпляр, добавив некоторые данные и выполнив их поиск, как показано ниже:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Создание сложного сегментированного кластера MongoDB на виртуальной машине CentOS с использованием шаблона
Используя следующий шаблон быстрого запуска Azure из GitHub, можно создать сложный сегментированный кластер MongoDB. Этот шаблон соответствует [рекомендациям для сегментированного кластера MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) в отношении избыточности и высокой доступности. Он предусматривает создание двух сегментов с тремя узлами в каждом наборе реплик. Кроме того, он создает набор реплик сервера конфигурации и два сервера маршрутизации **mongos**. Это позволяет обеспечить согласованность приложений из разных сегментов.

* [Сегментированный кластер MongoDB на виртуальной машине CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json.

> [!WARNING]
> Для развертывания сложного сегментированного кластера MongoDB требуется более 20 ядер. Обычно 20 ядер — это количество по умолчанию для региона, выделяемое на одну подписку. Отправьте запрос в службу поддержки Azure, чтобы увеличить количество ядер.

Для создания этой среды необходимо установить последнюю версию [Azure CLI 2.0](/cli/azure/install-az-cli2) и войти в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Сначала создайте группу ресурсов с помощью команды [az group create](/cli/azure/group#az_group_create). В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Затем разверните шаблон MongoDB с помощью команды [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Определите необходимые имена и размеры ресурсов, например *mongoAdminUsername*, *sizeOfDataDiskInGB* и *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

На то, чтобы развернуть и настроить все экземпляры виртуальной машины, может потребоваться более одного часа. Флаг `--no-wait` в конце предыдущей команды используется для возвращения управления командной строке после того, как развертывание шаблона будет принято платформой Azure. Затем можно просмотреть состояние развернутой службы с помощью команды [az group deployment show](/cli/azure/group/deployment#az_group_deployment_show). Приведенный ниже пример позволяет просмотреть состояние развернутой службы *myMongoDBCluster* в группе ресурсов *myResourceGroup*:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Дополнительная информация
В этих примерах подключение к экземпляру MongoDB выполняется локально с помощью виртуальной машины. Чтобы подключится к экземпляру MongoDB из другой виртуальной машины или сети, [создайте соответствующие правила группы безопасности сети](nsg-quickstart.md).

В этих примерах в целях разработки развертывается основная среда MongoDB. Примените необходимые параметры конфигурации безопасности для среды. Дополнительные сведения о безопасности MongoDB см. на [этой странице](https://docs.mongodb.com/manual/security/).

Дополнительные сведения о создании с использованием шаблонов см. в статье [Общие сведения о диспетчере ресурсов Azure](../../azure-resource-manager/resource-group-overview.md).

Для скачивания и выполнения скриптов на виртуальных машинах в шаблонах Azure Resource Manager используется расширение настраиваемых скриптов. Дополнительные сведения см. в статье [Использование расширения пользовательских сценариев Azure на виртуальных машинах Linux](extensions-customscript.md).

