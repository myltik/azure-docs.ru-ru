---
title: Использование управляемых удостоверений службы в Azure Cloud Shell | Документация Майкрософт
description: Аутентификация кода с помощью управляемых удостоверений службы в Azure Cloud Shell
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: juluk
ms.openlocfilehash: 99577faf7328dc773a9da5f7c1227aa63600aa0a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31516288"
---
# <a name="use-msi-in-azure-cloud-shell"></a>Использование управляемых удостоверений службы в Azure Cloud Shell

Azure Cloud Shell поддерживает авторизацию с помощью управляемых удостоверений службы (MSI). Используя этот метод, вы можете получить маркеры доступа, чтобы безопасно подключаться к службам Azure.

## <a name="about-managed-service-identity-msi"></a>Основные сведения об MSI
Распространенная проблема при создании облачных приложений — безопасное управление учетными данными, которые необходимы в коде для аутентификации в облачных службах. В Cloud Shell может возникнуть необходимость аутентифицировать получение учетных данных из Key Vault, которые могут требоваться в скрипте.

Управляемое удостоверение службы (MSI) упрощает решение этой задачи, предоставляя службам Azure автоматически управляемое удостоверение в Azure Active Directory (Azure AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, включая Key Vault, не храня какие-либо учетные данные в коде.

## <a name="acquire-access-token-in-cloud-shell"></a>Получение маркера доступа в Cloud Shell

Чтобы задать маркер доступа MSI в качестве переменной среды, `access_token`, выполните такие команды:
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Обработка срока действия маркера

Локальная подсистема MSI кэширует маркеры. Таким образом их можно вызывать столько, сколько нужно, а сетевой вызов Azure AD выполняется, только если:
- произошел промах кэша из-за отсутствия маркера в кэше;
- истек срок действия маркера.

Если маркер кэшируется в коде, необходимо подготовиться к обработке сценариев, в которых ресурс указывает, что срок действия маркера истек.

Сведения об обработке ошибок маркера см. [здесь](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token#error-handling).

## <a name="next-steps"></a>Дополнительная информация
[Управляемое удостоверение службы (MSI) для ресурсов Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)  
[Использование управляемого удостоверения службы (MSI) виртуальной машины Azure для получения маркера](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)
