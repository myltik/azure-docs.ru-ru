---
title: "Краткое руководство по Bash в Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Краткое руководство по Bash в Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: bbad2735acf06ebefda020410731fb307c4ec614
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Краткое руководство по Bash в Azure Cloud Shell

В этом документе объясняется, как использовать Bash в Azure Cloud Shell на [портале Azure](https://ms.portal.azure.com/).

> [!NOTE]
> Также вы можете ознакомиться с кратким руководством по использованию [PowerShell в Azure Cloud Shell](quickstart-powershell.md).

## <a name="start-cloud-shell"></a>Запуск Cloud Shell
1. Запустите **Cloud Shell** в верхней панели навигации портала Azure. <br>
![](media/quickstart/shell-icon.png)
2. Выберите подписку для создания учетной записи хранения и общей папки Azure.
3. Нажмите кнопку "Создать хранилище".

> [!TIP]
> Вы автоматически проходите проверку подлинности для Azure CLI 2.0 в каждом сеансе.

### <a name="select-the-bash-environment"></a>Выбор среды Bash
1. Щелкните раскрывающийся список среды в левой части окна оболочки. <br>
![](media/quickstart/env-selector.png)
2. Выберите Bash.

### <a name="set-your-subscription"></a>Настройка подписки
1. Выведите список подписок, к которым у вас есть доступ: <br>
`az account list`
2. Задайте предпочитаемую подписку: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> Подписка будет сохранена для последующих сеансов в файле `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов в WestUS с именем MyRG: <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Создание виртуальной машины Linux
Создайте виртуальную машину Ubuntu в новой группе ресурсов. Azure CLI 2.0 создаст ключи SSH и настроит с их помощью виртуальную машину. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Открытый и закрытый ключи, используемые для проверки подлинности виртуальной машины, помещаются в `/User/.ssh/id_rsa` и `/User/.ssh/id_rsa.pub` с помощью Azure CLI 2.0 по умолчанию. Папка в формате SSH сохраняется в образе размером 5 ГБ, размещенном в подключенной общей папке Azure.

Имя пользователя на этой виртуальной машине будет использоваться в Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH-подключение к виртуальной машине Linux
1. Найдите имя виртуальной машины на панели поиска портала Azure.
2. Щелкните "Подключить" и выполните `ssh username@ipaddress`.

![](media/quickstart/sshcmd-copy.png)

После установки SSH-подключения отобразится строка приветствия Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Очистка. 
Удалите группу ресурсов и все ее ресурсы. <br>
Запустите `az group delete -n MyRG`

## <a name="next-steps"></a>Дальнейшие действия
[Дополнительные сведения о сохранении файлов для Bash в Cloud Shell.](persisting-shell-storage.md) <br>
[Справочник команд Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Знакомство с хранилищем файлов Azure](../storage/files/storage-files-introduction.md) <br>
