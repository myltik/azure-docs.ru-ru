---
title: "Использование управляемого удостоверения служб виртуальной машины Azure с пакетами Azure SDK"
description: "Примеры кода для использования Azure SDK с управляемым удостоверением служб виртуальной машины Azure."
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
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 6c9bd57080c95e4aad45515a0814471194261286
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Использование управляемого удостоверения служб (MSI) виртуальной машины Azure с пакетами Azure SDK 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
В этой статье приведен список примеров пакетов SDK, в которых поддерживается пакет Azure SDK для MSI.

## <a name="prerequisites"></a>Необходимые компоненты

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Во всех примерах кода или скриптов в этой статье предполагается, что клиент выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной машине см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](msi-qs-configure-portal-windows-vm.md) или в одном из вариантов статей (с использованием PowerShell, CLI, шаблона или пакета SDK для Azure). 

## <a name="sdk-code-samples"></a>Примеры кода SDK

| SDK             | Пример кода |
| --------------- | ----------- |
| .NET            | [Развертывание шаблона Azure Resource Manager с виртуальной машины Windows с помощью управляемого удостоверения службы](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Вызов служб Azure с виртуальной машины Linux с помощью управляемого удостоверения службы](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Manage resources using Managed Service Identity using node.js](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) (Управление ресурсами, используя управляемое удостоверение службы с помощью Node.js) |
| Python          | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Использование MSI для простой проверки подлинности внутри виртуальной машины) |
| Ruby            | [Управление ресурсами из виртуальных машин с поддержкой MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Связанная информация

- В разделе [Пакеты SDK](https://azure.microsoft.com/downloads/) приведен полный список ресурсов для пакета Azure SDK, включая файлы для скачивания библиотеки, документацию и многое другое.
- О том, как включить MSI для виртуальной машины Azure, можно узнать в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure](msi-qs-configure-portal-windows-vm.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








