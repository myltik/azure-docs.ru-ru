---
title: "Скачивание средств Azure Stack из GitHub | Документация Майкрософт"
description: "Сведения о скачивании средств, необходимых для работы с Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: d4f8a8d73f8e2ea321cb6cc1deda2301033b249d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Скачивание средств Azure Stack из GitHub

AzureStack-Tools — это репозиторий GitHub, в котором размещены модули PowerShell. Их можно использовать для администрирования и развертывания ресурсов в Azure Stack. Вы можете скачать и использовать эти модули PowerShell с Пакетом средств разработки Azure Stack или внешним клиентом на базе Windows (при планировании VPN-подключения). Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку AzureStack-Tools, выполнив следующий скрипт:

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

| Функции | ОПИСАНИЕ | Кто может использовать этот модуль? |
| --- | --- | --- |
| [Возможности облачных служб](azure-stack-validate-templates.md) | Этот модуль используется для получения возможностей облачных служб. Например, с помощью этого модуля можно получить возможности использования версий API, ресурсов Azure Resource Manager, расширений виртуальных машин и т. д. для Azure Stack и облаков Azure. | Администраторы и пользователи облака. |
| [Политика Resource Manager для Azure Stack](azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с такой же версией и уровнем доступности службы, как и в Azure Stack. | Администраторы и пользователи облака |
| [Подключение к Azure Stack](azure-stack-connect-azure-stack.md) | Этот модуль используется для подключения к экземпляру Azure Stack с помощью PowerShell, а также для настройки VPN-подключения к Azure Stack. | Администраторы и пользователи облака |
| [Проверяющий элемент управления шаблона](azure-stack-validate-templates.md) | Этот модуль позволяет проверить, можно ли развернуть существующий или новый шаблон в Azure Stack. | Администраторы и пользователи облака |


## <a name="next-steps"></a>Дополнительная информация
* [Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)   
* [Подключение к Пакету средств разработки Azure Stack с помощью VPN](azure-stack-connect-azure-stack.md)  
