---
title: "Настройка устройств, присоединенных к Azure Active Directory | Документация Майкрософт"
description: "Сведения о настройке устройств, присоединенных к Azure Active Directory."
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
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: ru-ru
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>Настройка устройств, присоединенных к Azure Active Directory

Благодаря управлению устройствами в Azure Active Directory (Azure AD) пользователи получают доступ к ресурсам с устройств, соответствующих стандартам безопасности и нормативным требованиям. Дополнительные сведения см. в статье [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

С помощью [устройств, присоединенных к Azure Active Directory](device-management-introduction.md#azure-ad-joined-devices), организационные устройства с Windows 10 можно настроить для управления в Azure AD. Эти устройства не должны быть [присоединены к локальной службе AD](device-management-introduction.md#hybrid-azure-ad-joined-devices).

В этом разделе содержатся инструкции по регистрации устройства с Windows 10 в Azure AD. 

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать, необходимо убедиться, что:

- Имеются разрешения на присоединение устройств к Azure AD.

    ![Подключено](./media/device-management-azuread-joined-devices-setup/21.png)

- Максимальное количество устройств на пользователя не превышено. 

    ![Подключено](./media/device-management-azuread-joined-devices-setup/22.png)


Дополнительные сведения см. в разделе [Управление устройствами с помощью портала Azure (предварительная версия)](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Необходимая информация


- Windows регистрирует устройство в каталоге организации в Azure AD.

- Возможно, потребуется пройти многофакторную проверку подлинности. Ваш ИТ-администратор может решить эту проблему.

- Azure AD проверяет, требуется ли устройству регистрация в рамках политики управления мобильными устройствами. По возможности выполняется регистрация устройства.

- Windows осуществляет перенаправление управляемых пользователей на рабочий стол за счет автоматического входа.

- Федеративные пользователи перенаправляются на страницу входа в Windows для ввода учетных данных.


## <a name="joining-a-device"></a>Присоединение устройства

В этом разделе приводятся инструкции по присоединению устройства Windows 10 к Azure AD. После успешного присоединения устройства к Azure AD в диалоговом окне **Доступ к учетной записи места работы или учебного заведения** отобразится запись **Подключен к \<ваш каталог Azure AD\>**.

![Подключено](./media/device-management-azuread-joined-devices-setup/13.png)


**Чтобы присоединить устройство Windows 10, выполните следующее.**

1. В меню **Пуск** щелкните **Параметры**.

    ![данных](./media/device-management-azuread-joined-devices-setup/01.png)

2. Щелкните **Учетные записи**.

    ![Учетные записи](./media/device-management-azuread-joined-devices-setup/02.png)


3. Щелкните **Доступ к учетной записи места работы или учебного заведения**.

    ![Доступ к учетной записи места работы или учебного заведения](./media/device-management-azuread-joined-devices-setup/03.png)

4. В диалоговом окне **Доступ к учетной записи места работы или учебного заведения** щелкните **Подключиться**.

    ![Подключение](./media/device-management-azuread-joined-devices-setup/04.png)


5. В диалоговом окне **Setup a work or school account** (Настройка рабочей или учебной учетной записи) щелкните **Присоединить это устройство к Azure Active Directory**.

    ![Подключение](./media/device-management-azuread-joined-devices-setup/08.png)


6. В диалоговом окне **Let's get you signed in** (Выполнение входа в систему) введите имя учетной записи (например, someone@example.com) и нажмите кнопку **Далее**.

    ![Выполнение входа в систему](./media/device-management-azuread-joined-devices-setup/10.png)


6. В диалоговом окне **Ввод пароля** введите пароль и щелкните **Вход**.

    ![Ввод пароля](./media/device-management-azuread-joined-devices-setup/05.png)


7. В диалоговом окне **Убедитесь в том, что это ваша организация** щелкните **Присоединиться**.

    ![Диалоговое окно "Убедитесь в том, что это ваша организация"](./media/device-management-azuread-joined-devices-setup/11.png)


8. В диалоговом окне **You're all set** (Все готово) щелкните **Готово**.

    ![Диалоговое окно "You're all set" (Все готово)](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Проверка

Чтобы проверить, присоединено ли устройство к Azure AD, можно просмотреть диалоговое окно **Доступ к учетной записи места работы или учебного заведения** на устройстве.

![Подключено](./media/device-management-azuread-joined-devices-setup/13.png)

Кроме того, можно выполнить следующую команду: `dsregcmd /status`.  
На успешно присоединенном устройстве **AzureAdJoined** имеет значение **Yes**.

![Подключено](./media/device-management-azuread-joined-devices-setup/14.png)

Можно также просмотреть параметры устройства на портале Azure AD.

![Подключено](./media/device-management-azuread-joined-devices-setup/15.png)

Дополнительные сведения см. в разделе [Поиск устройств](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения можно найти в разделе  

- [Общие сведения об управлении устройствами в Azure Active Directory](device-management-introduction.md).

- [Управление устройствами с помощью портала Azure](device-management-azure-portal.md)





