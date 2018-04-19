---
title: Установка MongoDB на виртуальную машину Linux с помощью Azure CLI 1.0 | Документация Майкрософт
description: Узнайте, как установить и настроить MongoDB на виртуальной машине Linux в Azure, используя модель развертывания с помощью Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.openlocfilehash: a334a0b7b3b638229c61eef086b1919b4c303338
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm-using-the-azure-cli-10"></a>Как установить и настроить MongoDB на виртуальную машину Linux с помощью Azure CLI 1.0
[MongoDB](http://www.mongodb.org) — это популярная высокопроизводительная база данных NoSQL с открытым кодом. В этой статье рассматривается, как установить и настроить MongoDB на виртуальной машине Linux в Azure, используя модель развертывания с помощью Resource Manager. Изучив представленные примеры, вы узнаете, как:

* [установить и настроить базовый экземпляр MongoDB вручную](#manually-install-and-configure-mongodb-on-a-vm);
* [создать базовый экземпляр MongoDB на основе шаблона Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template);
* [создать сложный сегментированный кластер MongoDB с набором реплик с использованием шаблона Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template).


## <a name="cli-versions-to-complete-the-task"></a>Версии интерфейса командной строки для выполнения задачи
Вы можете выполнить задачу, используя одну из следующих версий интерфейса командной строки.

- Azure CLI 1.0 — интерфейс командной строки для классической модели развертывания и модели развертывания Resource Manager (в этой статье).
- [Azure CLI 2.0](create-cli-complete-nodejs.md) — интерфейс командной строки следующего поколения для модели развертывания с помощью Resource Manager.


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Установка и настройка MongoDB на виртуальной машине вручную
База данных MongoDB [содержит инструкции по установке](https://docs.mongodb.com/manual/administration/install-on-linux/) для дистрибутивов Linux, в том числе Red Hat, CentOS, SUSE, Ubuntu и Debian. В следующем примере создается виртуальная машина *CentOS*, использующая ключ SSH, который хранится в каталоге *~/.ssh/id_rsa.pub*. В ответ на запросы укажите имя учетной записи хранения, DNS-имя и учетные данные администратора:

```azurecli
azure vm quick-create \
    --image-urn CentOS \
    --ssh-publickey-file ~/.ssh/id_rsa.pub 
```

Войдите в виртуальную машину, используя общедоступный IP-адрес, полученный на предыдущем шаге создания виртуальной машины:

```bash
ssh azureuser@40.78.23.145
```

Чтобы добавить источники установки для MongoDB, создайте файл репозитория **yum**, как показано ниже:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.4.repo
```

Откройте файл репозитория MongoDB для редактирования. Добавьте следующие строки.

```sh
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

Установите MongoDB, используя **yum**, как показано ниже:

```bash
sudo yum install -y mongodb-org
```

По умолчанию в образах CentOS принудительно используется SELinux. Это препятствует доступу к MongoDB. Установите средства управления политиками и настройте SELinux так, чтобы база данных MongoDB могла использовать TCP-порт 27017 (по умолчанию): 

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
На виртуальной машине CentOS можно создать базовый экземпляр MongoDB, используя следующий шаблон быстрого запуска Azure из GitHub. В этом шаблоне используется расширение настраиваемых скриптов для Linux, что позволяет добавить в созданную виртуальную машину CentOS репозиторий `yum`, а затем установить MongoDB.

* [Базовый экземпляр MongoDB на виртуальной машине CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

В следующем примере создается группа ресурсов с именем `myResourceGroup` в регионе `eastus`. Введите свои значения следующим образом:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Интерфейс командной строки Azure отобразит строку всего через несколько секунд после начала развертывания, но для завершения установки и настройки может потребоваться несколько минут. Проверьте состояние развертывания, выполнив команду `azure group deployment show myResourceGroup` и указав соответствующим образом имя группы ресурсов. Подождите, пока для параметра **ProvisioningState** не отобразится значение *Succeeded*, а затем попробуйте подключиться к виртуальной машине по протоколу SSH.

После развертывания подключитесь к виртуальной машине по протоколу SSH. Получите IP-адрес виртуальной машины, используя команду `azure vm show`, как показано в следующем примере:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

В конце выходных данных будет отображаться общедоступный IP-адрес. Подключитесь к своей виртуальной машине по протоколу SSH, используя ее IP-адрес:

```bash
ssh azureuser@138.91.149.74
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

* [Сегментированный кластер MongoDB на виртуальной машине CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Для развертывания этого сложного сегментированного кластера MongoDB требуется более 20 виртуальных ЦП. Обычно 20 виртуальных ЦП — это количество по умолчанию для региона, выделяемое на одну подписку. Отправьте запрос в службу поддержки Azure, чтобы увеличить количество виртуальных ЦП.

В следующем примере создается группа ресурсов с именем *myResourceGroup* в регионе *eastus*. Введите свои значения следующим образом:

```azurecli
azure group create \
    --name myResourceGroup \
    --location eastus \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Интерфейс командной строки Azure отобразит строку всего через несколько секунд после начала развертывания, но для завершения установки и настройки может потребоваться более одного часа. Проверьте состояние развертывания, выполнив команду `azure group deployment show myResourceGroup` и указав соответствующим образом имя группы ресурсов. Подождите, пока для параметра **ProvisioningState** не отобразится значение *Succeeded*, а затем попробуйте подключиться к виртуальным машинам.


## <a name="next-steps"></a>Дополнительная информация
В этих примерах подключение к экземпляру MongoDB выполняется локально с помощью виртуальной машины. Чтобы подключится к экземпляру MongoDB из другой виртуальной машины или сети, [создайте соответствующие правила группы безопасности сети](nsg-quickstart.md).

Дополнительные сведения о создании с использованием шаблонов см. в статье [Общие сведения о диспетчере ресурсов Azure](../../azure-resource-manager/resource-group-overview.md).

Для скачивания и выполнения скриптов на виртуальных машинах в шаблонах Azure Resource Manager используется расширение настраиваемых скриптов. Дополнительные сведения см. в статье [Использование расширения пользовательских сценариев Azure на виртуальных машинах Linux](extensions-customscript.md).

