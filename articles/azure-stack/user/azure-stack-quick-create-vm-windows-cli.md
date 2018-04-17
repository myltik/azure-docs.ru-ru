---
title: Создание виртуальной машины Windows в Azure Stack с помощью Azure CLI | Документация Майкрософт
description: Узнайте, как создать виртуальную машину Windows в Azure Stack с помощью Azure CLI
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 2a4eb909c39051ce9fa2efd7e7997644d9b8b1b1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Создание виртуальной машины Windows в Azure Stack с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure Stack и управления ими из командной строки. В этом руководстве объясняется, как с помощью Azure CLI создать виртуальную машину под управлением Windows Server 2016 в Azure Stack. После создания виртуальной машины вы сможете подключиться к удаленному рабочему столу, установить IIS и увидеть веб-сайт по умолчанию. 

## <a name="prerequisites"></a>предварительным требованиям 

* Убедитесь, что оператор Azure Stack добавил образ Windows Server 2016 в Azure Stack Marketplace.  

* Для создания ресурсов и управления ими в Azure CLI требуется определенная версия Azure Stack. Если вы еще не настроили Azure CLI для Azure Stack, выполните действия по [установке и настройке](azure-stack-version-profiles-azurecli2.md) Azure CLI.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логический контейнер, в котором выполняется развертывание и администрирование ресурсов Azure Stack. Из пакета средств разработки или интегрированной системы Azure Stack выполните команду [az group create](/cli/azure/group#az_group_create), чтобы создать группу ресурсов. В этом документе мы присвоили значения всем переменным. Вы можете использовать их как есть или присвоить другое значение. В следующем примере создается группа ресурсов с именем myResourceGroup в локальном расположении.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm#az_vm_create). В следующем примере создаются виртуальная машина с именем myVM В этом примере используются имя администратора Demouser и пароль Demouser@123. Измените эти значения в соответствии со своей средой. Эти значения требуются при подключении к виртуальной машине.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Когда виртуальная машина будет создана, запишите параметр *PublicIPAddress*, который отображается в выходных данных. Вы будете использовать его для доступа к виртуальной машине.
 
## <a name="open-port-80-for-web-traffic"></a>Открытие порта 80 для веб-трафика

По умолчанию виртуальные машины Windows, развернутые в Azure Stack, поддерживают только RDP-подключения. Если эта виртуальная машина будет использоваться в качестве веб-сервера, необходимо открыть порт 80 через Интернет. Выполните команду [az vm open-port](/cli/azure/vm#open-port), чтобы открыть нужный порт.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Подключение к виртуальной машине

Используйте следующую команду для создания сеанса удаленного рабочего стола с виртуальной машиной. Замените IP-адрес общедоступным IP-адресом виртуальной машины. При появлении запроса введите учетные данные, использованные при создании виртуальной машины.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Установка IIS с помощью PowerShell

После входа на виртуальную машину Azure вы можете установить IIS и включить локальное правило брандмауэра, разрешающее веб-трафик, с помощью одной строки кода PowerShell. Откройте командную строку PowerShell и выполните следующую команду:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Просмотр страницы приветствия IIS

Установив IIS и открыв через Интернет порт 80 на виртуальной машине, вы можете просмотреть страницу приветствия IIS по умолчанию в любом браузере. Чтобы перейти на страницу по умолчанию, используйте общедоступный IP-адрес, записанный ранее. 

![Сайт IIS по умолчанию](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете удалить ставшие ненужными группу ресурсов, виртуальную машину и все связанные с ней ресурсы, выполнив команду [az group delete](/cli/azure/group#az_group_delete).

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы развернули простую виртуальную машину Windows. Дополнительные сведения о виртуальных машинах Azure Stack см. в [рекомендациях по работе с виртуальными машинами в Azure Stack](azure-stack-vm-considerations.md).
