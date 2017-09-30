---
title: "Настройка устройств, зарегистрированных в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить устройства, зарегистрированные в Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 427bf9a0487c771e57ac53a9bb0b7d7e2f6bdca2
ms.contentlocale: ru-ru
ms.lasthandoff: 09/20/2017

---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Настройка устройств с Windows 10, зарегистрированных в Azure Active Directory

Благодаря управлению устройствами в Azure Active Directory (Azure AD) ваши пользователи получают доступ к ресурсам с устройств, которые соответствуют стандартам безопасности и нормативным требованиям. Дополнительные сведения см. в статье [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

Если вы хотите реализовать сценарий **принеси свое устройство (BYOD)**, это можно сделать, настроив устройства, зарегистрированные в Azure AD. В Azure AD устройства, зарегистрированные в Azure AD, можно настроить для Windows 10, iOS, Android и macOS. В этом разделе приведены соответствующие инструкции для устройств Windows 10. 


## <a name="before-you-begin"></a>Перед началом работы

Чтобы зарегистрировать устройство Windows 10, необходимо настроить службу регистрации устройств для регистрации устройств. Помимо наличия разрешения на регистрацию устройств в клиенте Azure AD, необходимо, чтобы число зарегистрированных устройств не превышало установленное максимальное значение. Дополнительные сведения см. в разделе [Управление устройствами с помощью портала Azure (предварительная версия)](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Необходимая информация

При регистрации устройства нужно помнить следующее.

- Windows регистрирует устройство в каталоге организации в Azure AD.

- Возможно, потребуется выполнить запрос защиты Многофакторной идентификации. Этот запрос может настроить администратор ИТ.

- Azure AD проверяет, требует ли устройство регистрации в службе управления мобильными устройствами, и регистрирует его, если это применимо.

- Если вы являетесь управляемым пользователем, Windows осуществляет переход на рабочий стол с помощью автоматического входа.

- Если вы являетесь федеративным пользователем, то появится экран входа в Windows, где нужно будет ввести учетные данные.


## <a name="registering-a-device"></a>Регистрация устройства

В этом разделе приводятся инструкции по регистрации устройства Windows 10 в Azure AD. После успешной регистрации устройства в Azure AD в диалоговом окне **Доступ к учетной записи места работы или учебного заведения** отобразится запись **Рабочая или учебная учетная запись**.

![регистрация;](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Чтобы зарегистрировать устройство Windows 10, выполните следующее.**

1. В меню **Пуск** щелкните **Параметры**.

    ![данных](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Щелкните **Учетные записи**.

    ![Учетные записи](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Щелкните **Доступ к учетной записи места работы или учебного заведения**.

    ![Доступ к учетной записи места работы или учебного заведения](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. В диалоговом окне **Доступ к учетной записи места работы или учебного заведения** щелкните **Подключиться**.

    ![Подключение](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. В диалоговом окне **Setup a work or school account** (Настройка рабочей или учебной учетной записи) введите имя учетной записи (например, someone@example.com), а затем нажмите кнопку **Далее**.

    ![Подключение](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. В диалоговом окне **Ввод пароля** введите пароль и нажмите кнопку **Далее**.

    ![Подключение](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. В диалоговом окне **You're all set** (Все готово) щелкните **Готово**.

    ![Подключение](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Проверка

Чтобы проверить, присоединено ли устройство к Azure AD, можно просмотреть диалоговое окно **Доступ к учетной записи места работы или учебного заведения** на устройстве.

![регистрация;](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Можно также просмотреть параметры устройства на портале Azure AD.

![регистрация;](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. в статье [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

- Дополнительные сведения об управлении устройствами на портале Azure AD см. в разделе [Управление устройствами с помощью портала Azure (предварительная версия)](device-management-azure-portal.md).





