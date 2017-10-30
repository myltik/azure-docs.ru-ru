---
title: "Создание виртуальной машины Linux с помощью Azure CLI в Azure Stack | Документация Майкрософт"
description: "Создание виртуальной машины Linux с помощью CLI в Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: de2ff697c083493b43ab0d1b5bcde532c28684e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Создание виртуальной машины Linux с помощью Azure CLI в Azure Stack

*Область применения: интегрированные системы Azure Stack*

Azure CLI используется для создания ресурсов Azure Stack и управления ими из командной строки. В этом кратком руководстве подробно описано использование Azure CLI для создания виртуальной машины Linux в Azure Stack.  После создания виртуальной машины устанавливается веб-сервер и открывается порт 80, чтобы разрешить веб-трафик.

## <a name="prerequisites"></a>Предварительные требования 

* Убедитесь в том, что оператор Azure Stack добавил образ Ubuntu Server 16.04 LTS в Azure Stack Marketplace. 

* Для создания ресурсов и управления ими в Azure CLI требуется определенная версия Azure Stack. Если Azure CLI не настроен для Azure Stack, войдите в [пакет средств разработки](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) (или внешний клиент на базе Windows в случае [подключения через VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)) и выполните действия по [установке и настройке Azure CLI](azure-stack-connect-cli.md).

* В каталоге .ssh вашего профиля пользователя Windows должен быть создан открытый ключ SSH с именем id_rsa.pub. Дополнительные сведения о создании ключей SSH см. в статье [Использование ключей SSH с Windows в Azure](../../virtual-machines/linux/ssh-from-windows.md). 

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором происходит развертывание ресурсов Azure Stack и управление ими. Из пакета средств разработки или интегрированной системы Azure Stack выполните команду [az group create](/cli/azure/group#create), чтобы создать группу ресурсов. В этом документе мы присвоили значения всем переменным. Вы можете использовать их как есть или присвоить другое значение. В следующем примере создается группа ресурсов с именем myResourceGroup в локальном расположении.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#create). В следующем примере создаются виртуальная машина с именем myVM В этом примере используются имя администратора Demouser и пароль Demouser@123. Измените эти значения в соответствии со своей средой. Эти значения требуются при подключении к виртуальной машине.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

После завершения команда выведет параметры виртуальной машины.  Запишите значение *PublicIPAddress* — оно потребуется для подключения к виртуальной машине и управления ею.

## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

По умолчанию виртуальные машины Linux, развернутые в Azure, поддерживают только SSH-подключения. Если эта виртуальная машина будет использоваться в качестве веб-сервера, необходимо открыть порт 80 через Интернет. Выполните команду [az vm open-port](/cli/azure/vm#open-port), чтобы открыть нужный порт.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>SSH-подключение к виртуальной машине

Чтобы подключиться к виртуальной машине, в системе, в которой установлен протокол SSH, выполните следующую команду. В случае работы в Windows для создания подключения можно использовать [Putty](http://www.putty.org/). Замените адрес правильным общедоступным IP-адресом виртуальной машины. В примере выше использовался IP-адрес 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Установка nginx

Чтобы обновить источники пакетов и установить последнюю версию пакета nginx, используйте следующий bash-скрипт: 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Просмотр страницы приветствия nginx

Установив nginx и открыв через Интернет порт 80 на виртуальной машине, вы можете просмотреть страницу приветствия nginx по умолчанию в любом браузере. Чтобы перейти на страницу по умолчанию, используйте значение *publicIpAddress*, записанное ранее. 

![Сайт nginx по умолчанию](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#delete).

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы развернули простую виртуальную машину Linux. Дополнительные сведения о виртуальных машинах Azure Stack см. в [рекомендациях по работе с виртуальными машинами в Azure Stack](azure-stack-vm-considerations.md).

