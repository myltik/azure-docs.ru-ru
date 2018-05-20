---
title: Как настроить виртуальную машину Azure с поддержкой MSI с помощью пакета Azure SDK
description: Пошаговые инструкции по настройке и использованию управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью пакета Azure SDK.
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 781f332b2892d9af536bf9a6f81642842285927b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью пакета Azure SDK

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory (AD). Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Azure с помощью пакета Azure SDK.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>Пакеты Azure SDK с поддержкой MSI 

Благодаря [пакетам SDK Azure](https://azure.microsoft.com/downloads) в Azure реализована поддержка разных платформ программирования. Некоторые из них обновлены для включения поддержки MSI. Кроме того, для них предоставляются соответствующие примеры использования. Этот список обновляется по мере добавления поддержки дополнительных платформ:

| SDK | Образец |
| --- | ------ | 
| .NET   | [Управление ресурсами из виртуальных машин с поддержкой MSI](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Управление хранилищем из виртуальных машин с поддержкой MSI](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Создание виртуальной машины с поддержкой MSI](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Создание виртуальной машины с поддержкой MSI](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Создание виртуальной машины Azure с поддержкой MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Дополнительная информация

- Сведения о том, как использовать портал Azure, PowerShell, CLI и шаблоны Resource Manager см. в соответствующих статьях раздела "Настройка MSI для виртуальной машины Azure".

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
