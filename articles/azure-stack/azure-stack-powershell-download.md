---
title: "Скачивание средств Azure Stack из GitHub | Документация Майкрософт"
description: "Сведения о скачивании средств, необходимых для работы с Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E4DF77FA-F468-42B5-B44F-F10ED8049171
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.openlocfilehash: 219fd8e4e164df8c3002044719a90a7be56a9edf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/08/2018
---
# <a name="download-azure-stack-tools-from-github"></a>Скачивание средств Azure Stack из GitHub

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

**AzureStack-Tools** — это репозиторий GitHub, в котором размещены модули PowerShell для администрирования и развертывания ресурсов в Azure Stack. Если вы планируете установить VPN-подключение, можно скачать эти модули PowerShell в Пакет средств разработки Azure Stack или внешний клиент под управлением Windows. Чтобы получить эти средства, клонируйте репозиторий GitHub или скачайте папку **AzureStack-Tools**, выполнив следующий скрипт:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
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

| Функции | ОПИСАНИЕ | Кто может использовать этот модуль? |
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


## <a name="next-steps"></a>Дополнительная информация
* [Configure the Azure Stack user's PowerShell environment](user/azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)   
* [Подключение к Пакету средств разработки Azure Stack с помощью VPN](azure-stack-connect-azure-stack.md)  
