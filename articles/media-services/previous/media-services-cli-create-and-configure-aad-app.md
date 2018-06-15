---
title: Создание приложения Azure AD и его настройка для доступа к API служб мультимедиа Azure с помощью Azure CLI 2.0 | Документация Майкрософт
description: В этом разделе описывается, как создать и настроить приложение Azure AD для доступа к API служб мультимедиа Azure с помощью Azure CLI 2.0.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 7685be97213f0b298499d474c0a6a772ca608fb2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782733"
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Создание и настройка приложения Azure AD для доступа к API служб мультимедиа Azure с помощью Azure CLI 2.0

В этом разделе показано, как с помощью Azure CLI 2.0 создать приложение Azure Active Directory (Azure AD) и участник-службу для доступа к ресурсам служб мультимедиа Azure. 

## <a name="prerequisites"></a>предварительным требованиям

- Учетная запись Azure. Дополнительные сведения см. на странице с [бесплатной пробной версией Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Учетная запись служб мультимедиа. Дополнительные сведения см. в статье [Создание учетной записи служб мультимедиа Azure с помощью портала Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Использование Azure Cloud Shell

1. Войдите на [портале Azure](https://portal.azure.com/).
2. Запустите Cloud Shell с помощью верхней области навигации портала.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Дополнительные сведения см. в [обзоре Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Создание приложения Azure AD и настройка доступа к учетной записи служб мультимедиа с помощью Azure CLI 2.0
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Например: 

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

В этом примере **область** — полный путь к ресурсу учетной записи служб мультимедиа. Однако **область** может быть задана на любом уровне.

Например, ее можно задать на одном из следующих уровней:
 
* **подписка**;
* **группа ресурсов**;
* **ресурс** (например, учетная запись служб мультимедиа).

Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli).

Ознакомьтесь также с разделом [Управление доступом на основе ролей с помощью интерфейса командной строки Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Дополнительная информация

Приступите к [передаче файлов в учетную запись](media-services-portal-upload-files.md).
