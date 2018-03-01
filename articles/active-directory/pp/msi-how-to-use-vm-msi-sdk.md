---
title: "Как использовать назначаемое пользователем управляемое удостоверение службы из пакетов SDK Azure на виртуальной машине"
description: "Примеры кода для использования пакетов SDK Azure с назначаемым пользователем управляемым удостоверением службы на виртуальной машине."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 808b0357494adac8c8ad7f51e668317e378d290d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/24/2018
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Использование пакетов SDK Azure с назначаемым пользователем управляемым удостоверением службы (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этой статье приведен список примеров пакетов SDK, в которых поддерживается пакет Azure SDK для назначаемого пользователем удостоверения MSI.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - Во всех примерах кода или скриптов в этой статье предполагается, что клиент выполняется на виртуальной машине с поддержкой MSI. Используйте функцию подключения виртуальной машины на портале Azure для удаленного подключения к своей виртуальной машине. Дополнительные сведения о включении MSI на виртуальной машине см. в статье [Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью Azure CLI](msi-qs-configure-cli-windows-vm.md) или в одном из вариантов статей (с использованием PowerShell, портала Azure, шаблона или пакета SDK для Azure). 

## <a name="sdk-code-samples"></a>Примеры кода SDK

| SDK             | Пример кода |
| --------------- | ----------- |
| Python          | [Use MSI to authenticate simply from inside a VM](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) (Использование MSI для простой проверки подлинности внутри виртуальной машины) |
| Ruby            | [Управление ресурсами из виртуальных машин с поддержкой MSI](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>Дополнительная информация

- В разделе [Пакеты SDK](https://azure.microsoft.com/downloads/) приведен полный список ресурсов для пакета Azure SDK, включая файлы для скачивания библиотеки, документацию и многое другое.

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.








