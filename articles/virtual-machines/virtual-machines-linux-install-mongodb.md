---
title: "Установка MongoDB на виртуальной машине Linux в Azure | Документация Майкрософт"
description: "Узнайте, как установить и настроить MongoDB на виртуальной машине Linux в Azure, используя модель развертывания с помощью Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 3f55b546-86df-4442-9ef4-8a25fae7b96e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/20/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 370bcf5189c855185f11277518e0cbd5377993ab
ms.openlocfilehash: fd323a828ee879d6093e2473accbea883f861420


---
# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Установка и настройка MongoDB на виртуальной машине Linux в Azure
[MongoDB](http://www.mongodb.org) — это популярная высокопроизводительная база данных NoSQL с открытым кодом. В этой статье рассматривается, как установить и настроить MongoDB на виртуальной машине Linux в Azure, используя модель развертывания с помощью Resource Manager. Изучив представленные примеры, вы узнаете, как:

* [установить и настроить базовый экземпляр MongoDB вручную](#manually-install-and-configure-mongodb-on-a-vm);
* [создать базовый экземпляр MongoDB на основе шаблона Resource Manager](#create-basic-mongodb-instance-on-centos-using-a-template);
* [создать сложный сегментированный кластер MongoDB с набором реплик с использованием шаблона Resource Manager](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template).

## <a name="prerequisites"></a>Предварительные требования
Для выполнения инструкций в этой статье потребуются:

* учетная запись Azure ([получите бесплатную пробную версию](https://azure.microsoft.com/pricing/free-trial/));
* [интерфейс командной строки Azure](../xplat-cli-install.md) с выполненным входом (с использованием команды `azure login`);
* интерфейс командной строки Azure *нужно* переключить в режим Azure Resource Manager (выполнив команду `azure config mode arm`).

## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Установка и настройка MongoDB на виртуальной машине вручную
База данных MongoDB [содержит инструкции по установке](https://docs.mongodb.com/manual/administration/install-on-linux/) для дистрибутивов Linux, в том числе Red Hat, CentOS, SUSE, Ubuntu и Debian. В следующем примере создается виртуальная машина `CentOS`, использующая ключ SSH, который хранится в каталоге `~/.ssh/id_rsa.pub`. В ответ на запросы укажите имя учетной записи хранения, DNS-имя и учетные данные администратора:

```azurecli
azure vm quick-create --ssh-publickey-file ~/.ssh/id_rsa.pub --image-urn CentOS
```

Войдите в виртуальную машину, используя общедоступный IP-адрес, полученный на предыдущем шаге создания виртуальной машины:

```bash
ssh ops@40.78.23.145
```

Чтобы добавить источники установки для MongoDB, создайте файл репозитория `yum`, как показано ниже:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Откройте файл репозитория MongoDB для редактирования. Добавьте следующие строки.

```sh
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Установите MongoDB, используя `yum`, как показано ниже:

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

* [Базовый экземпляр MongoDB на виртуальной машине CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json.

В следующем примере создается группа ресурсов с именем `myResourceGroup` в регионе `WestUS`. Введите свои значения следующим образом:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [!NOTE]
> Интерфейс командной строки Azure отобразит строку всего через несколько секунд после начала развертывания, но для завершения установки и настройки может потребоваться несколько минут. Проверьте состояние развертывания, выполнив команду `azure group deployment show myResourceGroup` и указав соответствующим образом имя группы ресурсов. Подождите, пока для параметра `ProvisioningState` не отобразится значение Succeeded, а затем попробуйте подключиться к виртуальной машине по протоколу SSH.
> 
> 

После развертывания подключитесь к виртуальной машине по протоколу SSH. Получите IP-адрес виртуальной машины, используя команду `azure vm show`, как показано в следующем примере:

```azurecli
azure vm show --resource-group myResourceGroup --name myLinuxVM
```

В конце выходных данных будет отображаться `Public IP address`. Подключитесь к своей виртуальной машине по протоколу SSH, используя ее IP-адрес:

```bash
ssh ops@138.91.149.74
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
Используя следующий шаблон быстрого запуска Azure из GitHub, можно создать сложный сегментированный кластер MongoDB. Этот шаблон соответствует [рекомендациям для сегментированного кластера MongoDB](https://docs.mongodb.com/manual/core/sharded-cluster-components/) в отношении избыточности и высокой доступности. Он предусматривает создание двух сегментов с тремя узлами в каждом наборе реплик. Кроме того, он создает набор реплик сервера конфигурации и два сервера маршрутизации `mongos`. Это позволяет обеспечить согласованность приложений из разных сегментов.

* [Сегментированный кластер MongoDB на виртуальной машине CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos): https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json.

> [!WARNING]
> Для развертывания сложного сегментированного кластера MongoDB требуется более 20 ядер. Обычно 20 ядер — это количество по умолчанию для региона, выделяемое на одну подписку. Отправьте запрос в службу поддержки Azure, чтобы увеличить количество ядер.
> 
> 

В следующем примере создается группа ресурсов с именем `myResourceGroup` в регионе `WestUS`. Введите свои значения следующим образом:

```azurecli
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [!NOTE]
> Интерфейс командной строки Azure отобразит строку всего через несколько секунд после начала развертывания, но для завершения установки и настройки может потребоваться более одного часа. Проверьте состояние развертывания, выполнив команду `azure group deployment show myResourceGroup` и указав соответствующим образом имя группы ресурсов. Подождите, пока для параметра `ProvisioningState` не отобразится значение Succeeded, а затем подключайтесь к виртуальным машинам.
> 
> 

## <a name="next-steps"></a>Дальнейшие действия
В этих примерах подключение к экземпляру MongoDB выполняется локально с помощью виртуальной машины. Чтобы подключится к экземпляру MongoDB из другой виртуальной машины или сети, [создайте соответствующие правила группы безопасности сети](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Дополнительные сведения о создании с использованием шаблонов см. в статье [Общие сведения о диспетчере ресурсов Azure](../azure-resource-manager/resource-group-overview.md).

Для скачивания и выполнения скриптов на виртуальных машинах в шаблонах Azure Resource Manager используется расширение настраиваемых скриптов. Дополнительные сведения см. в статье [Использование расширения пользовательских сценариев Azure на виртуальных машинах Linux](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).




<!--HONumber=Dec16_HO3-->


