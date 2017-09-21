---
title: "Настройка устройств с Windows 10, зарегистрированных в Azure Active Directory | Документация Майкрософт"
description: "Узнайте, как настроить устройства с Windows 10, зарегистрированные в Azure Active Directory."
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
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Настройка устройств с Windows 10, зарегистрированных в Azure Active Directory

Благодаря управлению устройствами в Azure Active Directory (Azure AD) пользователи получают доступ к ресурсам с устройств, соответствующих стандартам безопасности и нормативным требованиям. Дополнительные сведения см. в статье [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

С помощью [зарегистрированных устройств Azure AD](device-management-introduction.md#azure-ad-registered-devices) можно включить сценарий **принеси свое устройство (BYOD)**, позволяющий пользователям получить доступ к ресурсам организации с личных устройств.  

В Azure AD можно настроить зарегистрированные устройства для:

- Windows 10
- iOS
- Android
- macOS.  

В этом разделе содержатся инструкции по регистрации устройства с Windows 10 в Azure AD. 


## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, необходимо убедиться, что:

- Имеются разрешения на регистрацию устройств. 

    ![регистрация;](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- Максимальное количество устройств на пользователя не превышено. 

    ![регистрация;](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

Дополнительные сведения см. в разделе [Настройка параметров устройства](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Необходимая информация

При регистрации устройства обратите внимание на следующее:

- Windows регистрирует устройство в каталоге организации в Azure AD.

- Возможно, потребуется пройти многофакторную проверку подлинности. Ваш ИТ-администратор может решить эту проблему.

- Azure AD проверяет, требуется ли устройству регистрация в рамках политики управления мобильными устройствами. По возможности выполняется регистрация устройства.

- Windows осуществляет перенаправление управляемых пользователей на рабочий стол за счет автоматического входа.

- Федеративные пользователи перенаправляются на страницу входа в Windows для ввода учетных данных.


## <a name="register-a-device"></a>Регистрация устройства

Чтобы зарегистрировать устройство с Windows 10 в Azure AD, сделайте следующее. После успешной регистрации устройства в Azure AD на странице **Access work or school** (Доступ к рабочей или учебной учетной записи) отобразится запись **Рабочая или учебная учетная запись**.

![регистрация;](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Чтобы зарегистрировать устройство Windows 10, сделайте следующее:

1. В меню **Пуск** щелкните **Параметры**.

    ![Выбор элемента "Параметры"](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Щелкните **Учетные записи**.

    ![Выбор элемента "Учетные записи"](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Щелкните **Доступ к учетной записи места работы или учебного заведения**.

    ![Выбор элемента Access work or school (Доступ к рабочей или учебной учетной записи)](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. В диалоговом окне **Access work or school** (Доступ к рабочей или учебной учетной записи) щелкните **Подключение**.

    ![Страница Access work or school (Доступ к рабочей или учебной учетной записи)](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. На странице **Настройка рабочей или учебной учетной записи** введите имя учетной записи (например, someone@example.com), а затем нажмите кнопку **Далее**.

    ![Страница "Настройка рабочей или учебной учетной записи"](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. На странице **Введите пароль** введите пароль и нажмите кнопку **Далее**.

    ![Ввод пароля](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. На странице **You're all set** (Все готово) щелкните **Готово**.

    ![Страница You're all set (Все готово)](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Проверка

Чтобы проверить, присоединено ли устройство к Azure AD, можно просмотреть страницу **Доступ к рабочей или учебной учетной записи** на устройстве.

![Состояние рабочей или учебной учетной записи](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Кроме того, можно просмотреть параметры устройства на портале Azure AD.

![Устройства, зарегистрированные в Azure AD](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения можно найти в разделе  

- [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

- [Управление устройствами с помощью портала Azure](device-management-azure-portal.md)





