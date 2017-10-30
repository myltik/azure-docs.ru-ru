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
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 20cac6eace89d86f8fa6d7640357e19c8d7359b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Скачивание средств Azure Stack из GitHub

*Область применения: интегрированные системы Azure Stack и пакет SDK для Azure Stack*

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
| [Возможности облачных служб](user/azure-stack-validate-templates.md) | Этот модуль используется для получения возможностей облачных служб. Например, можно получить возможности использования версий API, ресурсов Azure Resource Manager, расширений виртуальных машин и т. д. для Azure Stack и облаков Azure с поддержкой этого модуля. | Операторы и пользователи облака. |
| [Администрирование вычислений Azure Stack](azure-stack-add-vm-image.md) | Этот модуль используется для добавления или удаления образа виртуальной машины из Azure Stack Marketplace. | Операторы облака. |
| [Администрирование инфраструктуры Azure Stack](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | Этот модуль используется для управления виртуальными машинами инфраструктуры Azure Stack, оповещениями, обновлениями и т. д. |  Операторы облака.|
| [Политика Resource Manager для Azure Stack](user/azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с той же версией и уровнем доступности службы, как и в Azure Stack. | Операторы и пользователи облака. |
| [Регистрация в Azure](azure-stack-register.md) | Этот модуль используется для регистрации экземпляра пакета средств разработки в Azure. После регистрации вы можете скачать элементы Marketplace из Azure и использовать их в Azure Stack. | Операторы облака. |
| [Развертывание Azure Stack](azure-stack-run-powershell-script.md) | Этот модуль используется для подготовки хост-компьютера Azure Stack к развертыванию и повторному развертыванию с помощью образа виртуального жесткого диска Azure Stack (VHD). | Операторы облака. |
| [Подключение к Azure Stack](azure-stack-connect-powershell.md) | Этот модуль используется для подключения к экземпляру Azure Stack с помощью PowerShell, а также для настройки VPN-подключения к Azure Stack. | Операторы и пользователи облака. |
| [Администрирование служб Azure Stack](azure-stack-create-offer.md) | Администраторы Azure Stack могут использовать этот модуль для создания предложения клиента по умолчанию с неограниченной квотой для использования со службами вычислений, хранения, сети и хранилища ключей.   | Операторы облака.|
| [Проверяющий элемент управления шаблона](user/azure-stack-validate-templates.md) | Этот модуль используется для проверки, если в Azure Stack можно развернуть существующий или новый шаблон. | Операторы и пользователи облака. |


## <a name="next-steps"></a>Дальнейшие действия
* [Настройка пользовательской среды PowerShell в Azure Stack](user/azure-stack-powershell-configure-user.md)   
* [Подключение к пакету SDK для Azure Stack через VPN](azure-stack-connect-azure-stack.md)  
