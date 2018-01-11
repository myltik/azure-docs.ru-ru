---
title: "Настройка MSI, назначенный пользователем для ВМ Azure с помощью пакета Azure SDK"
description: "Пошагово, пошаговые инструкции по настройке назначенный пользователем управляемые службы удостоверений (MSI) для виртуальной Машины Azure, используя пакет SDK Azure."
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
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>Настройка назначенный пользователем управляемые службы удостоверений (MSI) для виртуальной Машины, с помощью пакета Azure SDK

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет служб Azure с использованием управляемого удостоверения Azure Active Directory. Этот идентификатор можно использовать для проверки подлинности для служб, которые поддерживают проверку подлинности Azure AD, без использования учетных данных в коде. 

В этой статье вы узнаете, как для включения и удаления MSI, назначенный пользователем для виртуальной Машины Azure, используя пакет SDK Azure.

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>Пакеты Azure SDK с поддержкой MSI 

Благодаря [пакетам SDK Azure](https://azure.microsoft.com/downloads) в Azure реализована поддержка разных платформ программирования. Некоторые из них обновлены для включения поддержки MSI. Кроме того, для них предоставляются соответствующие примеры использования. Этот список обновляется по мере добавления поддержки дополнительных платформ:

| SDK | Образец |
| --- | ------ | 
| Python | [Создание виртуальной машины с поддержкой MSI](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Создание виртуальной машины Azure с поддержкой MSI](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Дальнейшие действия

- Связанные статьи в разделе «Настройка MSI-ФАЙЛ для виртуальной Машины Azure», Подробнее о том, как настроить MSI, назначенный пользователем на Виртуальной машине Azure в разделе.

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
