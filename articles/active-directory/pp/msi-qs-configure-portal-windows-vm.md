---
title: Настройка MSI на виртуальной машине Azure с помощью портала Azure
description: Пошаговые инструкции по настройке управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 37710015904c8112e5d2de504ed5b42895ffb809
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/03/2018
ms.locfileid: "28979179"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Настройка управляемого удостоверения службы (MSI) на виртуальной машине Azure с помощью портала Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

Из этой статьи вы узнаете, как включить и удалить MSI для виртуальных машин Azure с помощью портала Azure.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Включение MSI во время создания виртуальной машины Azure

На момент написания этой статьи включение MSI во время создания виртуальной машины на портале Azure не поддерживалось. Чтобы создать виртуальную машину, воспользуйтесь одним из следующих кратких руководств:

- [Создание виртуальной машины Windows с помощью портала Azure](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Создание виртуальной машины Linux с помощью портала Azure](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Дополнительные сведения о включении MSI на виртуальной машине см. в следующем разделе.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Включение MSI на имеющейся виртуальной машине Azure

При наличии виртуальной машины, изначально созданной без MSI, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, связанной с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин".

2. Перейдите к необходимой виртуальной машине.

2. Щелкните страницу "Конфигурация", включите MSI на виртуальной машине, выбрав "Да" в разделе Managed service identity (Управляемое удостоверение службы), а затем щелкните **Сохранить**. Эта операция может занять более 60 секунд.

   ![Снимок экрана страницы "Конфигурация"](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Удаление MSI с виртуальной машины Azure

При наличии виртуальной машины, которой больше не требуется MSI, сделайте следующее:

1. Войдите на [портал Azure](https://portal.azure.com) с помощью учетной записи, связанной с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в виртуальной машине, например "Участник виртуальных машин".

2. Перейдите к необходимой виртуальной машине.

3. Щелкните страницу "Конфигурация", удалите MSI с виртуальной машины, выбрав "Нет" в разделе Managed service identity (Управляемое удостоверение службы), а затем щелкните **Сохранить**. Эта операция может занять более 60 секунд.

   ![Снимок экрана страницы "Конфигурация"](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).

## <a name="next-steps"></a>Дополнительная информация

- С помощью портала Azure, предоставьте управляемому удостоверению службы виртуальной машины Azure [доступ к другому ресурсу Azure](msi-howto-assign-access-portal.md).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
