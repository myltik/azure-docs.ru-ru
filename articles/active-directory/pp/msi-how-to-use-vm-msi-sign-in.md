---
title: "Использование удостоверения управляемой службы виртуальной машины Azure для входа"
description: "Пошаговая инструкция с примерами демонстрирует применение субъекта-службы MSI виртуальной машины Azure для входа и доступа к ресурсам из клиентского скрипта."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/05/2018
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: c5f71d27a9e07cc6d6a260b809e91aaa2a50270c
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2018
---
# <a name="sign-in-using-a-vm-user-assigned-managed-service-identity-msi"></a>Выполните вход с помощью виртуальной Машины назначенный пользователем управляемые службы удостоверений (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]В этой статье содержатся примеры сценариев CLI для войдите в систему пользователь назначен субъект-служба MSI и рекомендации на важные подразделы, такие как обработка ошибок.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Чтобы использовать Azure CLI примеры в этой статье, не забудьте установить последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

> [!IMPORTANT]
> - Во всех примерах скриптов в этой статье предполагается, что клиент командной строки выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной Машине см. в разделе [Настройка виртуальной Машины управляемые службы удостоверений (MSI) с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md), или один из вариантов статей (с помощью PowerShell, портала, шаблон или пакет Azure SDK). 
> - Чтобы избежать ошибок во время входа и ресурсов доступа, MSI-ФАЙЛ должен быть присвоен по крайней мере доступ «Чтения» в соответствующей области (виртуальной Машины или более поздней версии) для обеспечения операций диспетчера ресурсов Azure на виртуальной Машине. В разделе [назначение доступа управляемые службы удостоверений (MSI) для ресурса с помощью Azure CLI](msi-howto-assign-access-cli.md) подробные сведения.

## <a name="overview"></a>Обзор

Удостоверение MSI предоставляет [субъект-службу](~/articles/active-directory/develop/active-directory-dev-glossary.md#service-principal-object), который [создается при включении MSI](msi-overview.md#how-does-it-work) на виртуальной Машине. Субъекта-службы можно получает доступ к ресурсам Azure и как удостоверение скрипт/командной-строки клиенты используют для входа и ресурсов доступа. Чтобы клиентский скрипт мог обращаться к защищенным ресурсам с собственным удостоверением, обычно нужно выполнить следующие условия:  

   - зарегистрировать скрипт в качестве конфиденциального клиента приложения или приложения веб-клиента в Azure AD и получить соответствующее согласие;
   - выполнить вход из скрипта с соответствующим субъектом-службой, используя учетные данные приложения (обычно они встраиваются в скрипт).

Благодаря MSI это больше не требуется. Скрипт может просто выполнить вход от имени субъекта-службы MSI. 

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Следующий сценарий демонстрирует, как:

1. Войдите в Azure AD в назначенное пользователем MSI участника-службы.  
2. Вызов диспетчера ресурсов Azure и получения сведений о расположении регион Azure для виртуальной Машины. CLI автоматически управляет получением и использованием маркера. Обязательно замените на имя виртуальной Машины `<VM NAME>`и пользователь назначен идентификатор ресурса MSI для `<MSI ID>`. Идентификатор ресурса MSI возвращается в `id` свойства во время создания MSI, назначенный пользователем (см. [настроить назначенное пользователем управляемые службы удостоверений (MSI) для виртуальной Машины, с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md) примеры `az identity create` команды ).

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

Следующие ответы может указывать, что MSI-ФАЙЛ не был правильно настроен.

- CLI: *MSI: Failed to retrieve a token from 'http://localhost:50342/oauth2/token' with an error of 'HTTPConnectionPool(host='localhost', port=50342)* (Не удалось получить маркер от http://localhost:50342/oauth2/token, получена ошибка HTTPConnectionPool(host='localhost', port=50342)). 

Если вы встретите любую из этих ошибок, найдите виртуальную машину Azure на [портале Azure](https://portal.azure.com) и выполните на ней следующие действия:

- Откройте страницу **Конфигурация** и убедитесь, что для параметра "Удостоверение управляемой службы" задано значение "Да".
- Откройте страницу **Расширения** и убедитесь, что расширение MSI успешно развернуто.

Если либо неверна, может потребоваться снова переназначить MSI-ФАЙЛ к ресурсу или устранения сбоя развертывания. В разделе [Настройка виртуальной Машины управляемые службы удостоверений (MSI) с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md) Если вам требуется помощь для конфигурации виртуальной Машины.

## <a name="next-steps"></a>Дальнейшие действия

- При включении MSI в виртуальной Машине Azure см. [Настройка виртуальной Машины управляемые службы удостоверений (MSI) с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








