---
title: "Использование удостоверения управляемой службы виртуальной машины Azure для входа"
description: "Пошаговая инструкция с примерами демонстрирует применение субъекта-службы MSI виртуальной машины Azure для входа и доступа к ресурсам из клиентского скрипта."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5c1be01947dba8b7f4ef8aa54aa6aedfb191d32
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Вход в систему с использованием назначаемого пользователем управляемого удостоверения службы (MSI) для виртуальной машины

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Эта статья содержит примеры скриптов CLI, которые позволяют войти в систему с использованием назначаемого пользователем субъекта-службы MSI. Также здесь представлены рекомендации по таким важным вопросам, как обработка ошибок.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Чтобы использовать примеры Azure CLI в этой статье, убедитесь, что у вас установлена последняя версия [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Во всех примерах скриптов в этой статье предполагается, что клиент командной строки выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной машине см. в руководствах по [настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI, PowerShell, портала, шаблона или пакета SDK для Azure](msi-qs-configure-cli-windows-vm.md). 
> - Чтобы избежать ошибок во время входа в систему и доступа к ресурсам, для MSI необходимо предоставить по меньшей мере роль "Читатель" в соответствующей области (на уровне виртуальной машины или выше), то есть разрешить операции Azure Resource Manager на виртуальной машине. Дополнительные сведения см. в статье [Назначение доступа на основе управляемого удостоверения службы (MSI) для ресурса с помощью Azure CLI](msi-howto-assign-access-cli.md).

## <a name="overview"></a>Обзор

Удостоверение MSI предоставляет [субъект-службу](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), который [создается при включении MSI](msi-overview.md#how-does-it-work) на виртуальной Машине. Субъекту-службе можно предоставить доступ к определенным ресурсам Azure. Также субъект-служба может использоваться в качестве удостоверения скриптами и клиентами командной строки для входа в систему и доступа к ресурсам. Чтобы клиентский скрипт мог обращаться к защищенным ресурсам с собственным удостоверением, обычно нужно выполнить следующие условия:  

   - зарегистрировать скрипт в качестве конфиденциального клиента приложения или приложения веб-клиента в Azure AD и получить соответствующее согласие;
   - выполнить вход из скрипта с соответствующим субъектом-службой, используя учетные данные приложения (обычно они встраиваются в скрипт).

Благодаря MSI это больше не требуется. Скрипт может просто выполнить вход от имени субъекта-службы MSI. 

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Следующий сценарий демонстрирует, как:

1. войти в Azure AD с помощью назначаемого пользователем субъекта-службы MSI;  
2. вызвать Azure Resource Manager и получить данные о расположении региона Azure для виртуальной машины. CLI автоматически управляет получением и использованием маркера. Обязательно укажите имя своей виртуальной машины вместо `<VM NAME>` и идентификатор ресурса назначаемого пользователем MSI вместо `<MSI ID>`. Идентификатор ресурса MSI возвращается в свойстве `id` при создании назначаемого пользователем MSI. Примеры использования команды `az identity create` см. в статье [Настройка назначаемого пользователем управляемого удостоверения службы (MSI) для виртуальной машины с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

    ```azurecli
    az login --msi –u <MSI ID>
   
    vmLocation=$(az resource list -n <VM NAME> --query [*].location --out tsv)
    echo The VM region location is $vmLocation
    ```

    Примеры ответов:
   
    ```bash
    user@vmLinux:~$ az login --msi -u /subscriptions/80c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName
    [
      {
        "environmentName": "AzureCloud",
        "id": "90c696ff-5efa-4909-a64d-z1b616f423bl",
        "isDefault": true,
        "name": "MSIResource-/subscriptions/90c696ff-5efa-4909-a64d-z1b616f423bl/resourcegroups/rgName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiName@50342",
        "state": "Enabled",
        "tenantId": "933a8f0e-ec41-4e69-8ad8-971zc4b533ll",
        "user": {
          "name": "userAssignedIdentity",
          "type": "servicePrincipal"
        }
      }
    ]  
    user@vmLinux:~$ vmLocation=$(az resource list -n vmLinux --query [*].location --out tsv)
    user@vmLinux:~$ echo The VM region location is $vmLocation
    The VM region location is westcentralus
    ```

## <a name="resource-ids-for-azure-services"></a>Идентификаторы ресурсов для служб Azure

Список ресурсов, которые поддерживают Azure AD и были протестированы с MSI и соответствующими идентификаторами ресурсов, см. в разделе [Службы Azure, поддерживающие аутентификацию Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).

## <a name="error-handling-guidance"></a>Руководство по обработке ошибок 

Такие ответы могут указывать на то, что удостоверение MSI настроено неправильно:

- CLI: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Не удалось получить маркер от http://localhost:50342/oauth2/token, получена ошибка HTTPConnectionPool(host='localhost', port=50342)). 

Если вы встретите любую из этих ошибок, найдите виртуальную машину Azure на [портале Azure](https://portal.azure.com) и выполните на ней следующие действия:

- Откройте страницу **Конфигурация** и убедитесь, что для параметра "Удостоверение управляемой службы" задано значение "Да".
- Откройте страницу **Расширения** и убедитесь, что расширение MSI успешно развернуто.

Если какое-либо из условий не выполнено, может потребоваться повторно назначить MSI для ресурса или устранить неполадки с развертыванием. Если вам нужна помощь при настройке виртуальной машины, см. статью [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

## <a name="next-steps"></a>Дополнительная информация

- О том, как включить MSI на виртуальной машине Azure, можно узнать в разделе [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








