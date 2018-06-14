---
title: Использование удостоверения управляемой службы виртуальной машины Azure для входа
description: Пошаговая инструкция с примерами демонстрирует применение субъекта-службы MSI виртуальной машины Azure для входа и доступа к ресурсам из клиентского скрипта.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: c48e4bdf9a8c4b8515028fe45cdf724f5ff9f666
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929204"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>Использование удостоверения управляемой службы (MSI) виртуальной машины Azure для входа 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Эта статья содержит примеры сценариев PowerShell и интерфейса командной строки, позволяющих войти в систему с использованием субъекта-службы MSI, а также рекомендации по некоторым важным вопросам, таким как обработка ошибок.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Если вы планируете использовать примеры Azure PowerShell или CLI из этой статьи, убедитесь, что установлена последняя версия [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) или [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Во всех примерах скриптов в этой статье предполагается, что клиент командной строки выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной машине см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](qs-configure-portal-windows-vm.md) или в одном из вариантов статей (с использованием PowerShell, CLI, шаблона или пакета SDK для Azure). 
> - Чтобы избежать ошибок во время доступа к ресурсам, для MSI виртуальной машины необходимо предоставить по меньшей мере роль "Читатель" в соответствующей области (на уровне виртуальной машины или выше), то есть разрешить операции Azure Resource Manager на виртуальной машине. Дополнительные сведения см. в статье [Назначение доступа на основе управляемого удостоверения службы (MSI) для ресурса с помощью портала Azure](howto-assign-access-portal.md).

## <a name="overview"></a>Обзор

Удостоверение MSI предоставляет [объект субъекта-службы](../develop/active-directory-dev-glossary.md#service-principal-object), который [создается при включении MSI](overview.md#how-does-it-work) на виртуальной Машине. Субъекту-службе можно предоставить доступ к определенным ресурсам Azure и использовать его в качестве удостоверения для входа и использования ресурсов из скриптов и клиентов командной строки. Чтобы клиентский скрипт мог обращаться к защищенным ресурсам с собственным удостоверением, обычно нужно выполнить следующие условия:  

   - зарегистрировать скрипт в качестве конфиденциального клиента приложения или приложения веб-клиента в Azure AD и получить соответствующее согласие;
   - выполнить вход из скрипта с соответствующим субъектом-службой, используя учетные данные приложения (обычно они встраиваются в скрипт).

Благодаря MSI это больше не требуется. Скрипт может просто выполнить вход от имени субъекта-службы MSI. 

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью субъекта-службы MSI виртуальной машины;  
2. вызвать Azure Resource Manager и получить идентификатор субъекта-службы виртуальной машины. CLI автоматически управляет получением и использованием маркера. Не забудьте указать правильное имя виртуальной машины вместо `<VM-NAME>`.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью субъекта-службы MSI виртуальной машины;  
2. вызвать командлет Azure Resource Manager для получения сведений о виртуальной машине. PowerShell автоматически управляет использованием маркера.  

   ```azurepowershell
   Add-AzureRmAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure

Список ресурсов, которые поддерживают Azure AD и были протестированы с MSI и соответствующими идентификаторами ресурсов, см. в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](services-support-msi.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Руководство по обработке ошибок 

Такие ответы могут обозначать, что MSI виртуальной машины настроен неправильно:

- PowerShell: *Invoke-WebRequest : Unable to connect to the remote server* (Invoke-WebRequest: Unable to connect to the remote server)
- CLI: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Не удалось получить маркер от http://localhost:50342/oauth2/token, получена ошибка HTTPConnectionPool(host='localhost', port=50342)). 

Если вы встретите любую из этих ошибок, найдите виртуальную машину Azure на [портале Azure](https://portal.azure.com) и выполните на ней следующие действия:

- Откройте страницу **Конфигурация** и убедитесь, что для параметра "Удостоверение управляемой службы" задано значение "Да".
- Откройте страницу **Расширения** и убедитесь, что расширение MSI успешно развернуто.

Если какое-либо из условий не выполнено, может потребоваться повторно развернуть MSI для ресурса или устранить неполадки развертывания. Если вам нужна помощь при конфигурации виртуальной машины, см. статью [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](qs-configure-portal-windows-vm.md).

## <a name="related-content"></a>Связанная информация

- Как включить MSI для виртуальной машины Azure, можно узнать из статей [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью PowerShell](qs-configure-powershell-windows-vm.md) и [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








