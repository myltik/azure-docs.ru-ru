---
title: Скачивание средств Azure Stack из GitHub | Документация Майкрософт
description: Сведения о скачивании средств, необходимых для работы с Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: a116b7a048ff95ca601a65633cdc63f98fefee9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075295"
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
| [Политика Resource Manager для Azure Stack](user/azure-stack-policy-module.md) | Этот модуль используется для настройки подписки Azure или группы ресурсов Azure с той же версией и уровнем доступности службы, как и в Azure Stack. | Операторы и пользователи облака. |
| [Регистрация в Azure](azure-stack-register.md) | Этот модуль используется для регистрации экземпляра пакета средств разработки в Azure. После регистрации вы можете скачать элементы Marketplace из Azure и использовать их в Azure Stack. | Операторы облака. |
| [Развертывание Azure Stack](azure-stack-run-powershell-script.md) | Этот модуль используется для подготовки главного компьютера Azure Stack к развертыванию и повторному развертыванию с помощью образа виртуального жесткого диска Azure Stack (VHD). | Операторы облака.|
| [Подключение к Azure Stack](azure-stack-connect-powershell.md) | Этот модуль используется для настройки VPN-подключения к Azure Stack. | Операторы и пользователи облака. |
| [Проверяющий элемент управления шаблона](user/azure-stack-validate-templates.md) | Этот модуль используется для проверки, если в Azure Stack можно развернуть существующий или новый шаблон. | Операторы и пользователи облака.|


## <a name="next-steps"></a>Дополнительная информация
* [Configure the Azure Stack user's PowerShell environment](user/azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)   
* [Подключение к Пакету средств разработки Azure Stack с помощью VPN](azure-stack-connect-azure-stack.md)  
