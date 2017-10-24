---
title: "Скачивание средств Azure Stack из GitHub | Документация Майкрософт"
description: "Сведения о скачивании средств, необходимых для работы с Azure Stack."
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
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 69ca030d1b7601df424fa9446e1d194a3f6bd50a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Скачивание средств Azure Stack из GitHub

AzureStack-Tools — это репозиторий GitHub, в котором размещены модули PowerShell. Их можно использовать для администрирования и развертывания ресурсов в Azure Stack. Вы можете скачать и использовать эти модули PowerShell с пакетом SDK для Azure Stack или внешним клиентом на базе Windows (при планировании VPN-подключения). Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку AzureStack-Tools. 

Чтобы клонировать репозиторий, скачайте [Git](https://git-scm.com/download/win) для Windows, откройте окно командной строки и выполните следующий скрипт:

```PowerShell
# Change directory to the root directory 
cd \

# clone the repository
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory
cd AzureStack-Tools
```

Чтобы скачать папку средств, выполните следующий скрипт:

```PowerShell
# Change directory to the root directory 
cd \

# Download the tools archive
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Возможности, предоставляемые модулями

Репозиторий AzureStack-Tools содержит модули PowerShell с поддержкой следующих функций для работы в среде Azure Stack:  

| Функции | Описание | Кто может использовать этот модуль? |
| --- | --- | --- |
| [Возможности облачных служб](azure-stack-validate-templates.md) | Этот модуль используется для получения возможностей облачных служб. Например, с помощью этого модуля можно получить возможности использования версий API, ресурсов Azure Resource Manager, расширений виртуальных машин и т. д. для Azure Stack и облаков Azure. | Администраторы и пользователи облака. |
| [Политика Resource Manager для Azure Stack](azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с такой же версией и уровнем доступности службы, как и в Azure Stack. | Администраторы и пользователи облака |
| [Подключение к Azure Stack](azure-stack-connect-azure-stack.md) | Этот модуль используется для подключения к экземпляру Azure Stack с помощью PowerShell, а также для настройки VPN-подключения к Azure Stack. | Администраторы и пользователи облака |
| [Проверяющий элемент управления шаблона](azure-stack-validate-templates.md) | Этот модуль позволяет проверить, можно ли развернуть существующий или новый шаблон в Azure Stack. | Администраторы и пользователи облака |


## <a name="next-steps"></a>Дальнейшие действия
* [Настройка пользовательской среды PowerShell в Azure Stack](azure-stack-powershell-configure-user.md)   
* [Подключение к Azure Stack с помощью VPN](azure-stack-connect-azure-stack.md)  
