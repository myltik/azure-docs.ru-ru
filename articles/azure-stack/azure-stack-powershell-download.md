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
ms.openlocfilehash: c0a4f337c055f4b62d986e2a3c3ce7b962aceae9
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2017
---
# <a name="download-azure-stack-tools-from-github"></a>Скачивание средств Azure Stack из GitHub

*Область применения: интегрированные системы Azure Stack и комплект разработки Azure Stack*

**AzureStack-Tools** — это репозиторий GitHub, в котором размещены модули PowerShell для администрирования и развертывания ресурсов в Azure Stack. Если вы планируете установить VPN-подключение, можно скачать эти модули PowerShell в пакет SDK для Azure Stack или внешний клиент под управлением Windows. Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку **AzureStack-Tools**. 

Чтобы клонировать репозиторий, скачайте [Git для Windows](https://git-scm.com/download/win), откройте командную строку и выполните следующий скрипт:

```PowerShell
# Change directory to the root directory. 
cd \

# Clone the repository.
git clone https://github.com/Azure/AzureStack-Tools.git --recursive

# Change to the tools directory.
cd AzureStack-Tools
```

Чтобы скачать папку средств, выполните следующий скрипт:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Возможности, предоставляемые модулями

Репозиторий **AzureStack-Tools** содержит модули PowerShell с поддержкой следующих функций для работы в среде Azure Stack:  

| Функции | Описание | Кто может использовать этот модуль? |
| --- | --- | --- |
| [Возможности облачных служб](user/azure-stack-validate-templates.md) | Этот модуль используется для получения возможностей облачных служб. Например, этот модуль позволяет использовать версии API и ресурсы Azure Resource Manager. Также с помощью модуля можно получить расширения виртуальных машин для Azure Stack и облаков Azure. | Операторы и пользователи облака. |
| [Администрирование вычислений Azure Stack](azure-stack-add-vm-image.md) | Этот модуль используется для добавления или удаления образа виртуальной машины из Azure Stack Marketplace. | Операторы облака. |
| [Администрирование инфраструктуры Azure Stack](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | Этот модуль используется для управления виртуальными машинами инфраструктуры Azure Stack, оповещениями, обновлениями и т. д. |  Операторы облака.|
| [Политика Resource Manager для Azure Stack](user/azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с той же версией и уровнем доступности службы, как и в Azure Stack. | Операторы и пользователи облака. |
| [Регистрация в Azure](azure-stack-register.md) | Этот модуль используется для регистрации экземпляра пакета средств разработки в Azure. После регистрации вы можете скачать элементы Marketplace из Azure и использовать их в Azure Stack. | Операторы облака. |
| [Развертывание Azure Stack](azure-stack-run-powershell-script.md) | Этот модуль используется для подготовки главного компьютера Azure Stack к развертыванию и повторному развертыванию с помощью образа виртуального жесткого диска Azure Stack (VHD). | Операторы облака.|
| [Подключение к Azure Stack](azure-stack-connect-powershell.md) | Этот модуль используется для подключения к экземпляру Azure Stack с помощью PowerShell, а также для настройки VPN-подключения к Azure Stack. | Операторы и пользователи облака. |
| [Администрирование служб Azure Stack](azure-stack-create-offer.md) | Этот модуль позволяет создать предложение клиента по умолчанию с неограниченной квотой для использования со службами вычислений, сетевыми службами, службой хранилища Azure и Key Vault.   | Операторы облака.|
| [Проверяющий элемент управления шаблона](user/azure-stack-validate-templates.md) | Этот модуль используется для проверки, если в Azure Stack можно развернуть существующий или новый шаблон. | Операторы и пользователи облака.|


## <a name="next-steps"></a>Дальнейшие действия
* [Configure the Azure Stack user's PowerShell environment](user/azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)   
* [Подключение к Azure Stack с помощью VPN](azure-stack-connect-azure-stack.md)  
