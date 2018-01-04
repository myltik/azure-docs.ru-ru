---
title: "Как использовать назначенный пользователем управляемого удостоверения службы с Azure SDK на виртуальной Машине"
description: "Примеры кода с использованием Azure SDK с MSI назначенный пользователем на виртуальной Машине."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f9a31a0500a6f5f1c49fc45d5811e28788e6f2b1
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Используйте пакеты SDK Azure с назначенный пользователем управляемые службы удостоверений (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]В этой статье список образцов SDK, которые демонстрируют использование поддержки их соответствующих Azure SDK для MSI, назначенный пользователем.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - Во всех примерах кода или скриптов в этой статье предполагается, что клиент выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной Машине см. в разделе [Настройка виртуальной Машины управляемые службы удостоверений (MSI) с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md), или один из вариантов статей (с помощью PowerShell, портала Azure, шаблон или пакет Azure SDK). 

## <a name="sdk-code-samples"></a>Примеры кода SDK

| SDK             | Пример кода |
| --------------- | ----------- |
| Python          | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Использование MSI для простой проверки подлинности внутри виртуальной машины) |
| Ruby            | [Управление ресурсами из виртуальных машин с поддержкой MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Дальнейшие действия

- В разделе [Пакеты SDK](https://azure.microsoft.com/downloads/) приведен полный список ресурсов для пакета Azure SDK, включая файлы для скачивания библиотеки, документацию и многое другое.

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








