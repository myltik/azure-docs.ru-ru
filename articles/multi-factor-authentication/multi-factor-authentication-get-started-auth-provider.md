---
title: "Начало работы с поставщиком многофакторной идентификации Microsoft Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как создать поставщик Azure Multi Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4572913c2bc732c31f38021f9d3ccc34417de875
ms.openlocfilehash: be4361af8f3f3024ab7f12114760f6f58e77deaf


---
# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Начало работы с поставщиком Azure Multi Factor Authentication
Двухфакторная проверка подлинности доступна по умолчанию для глобальных администраторов, у которых есть служба Azure Active Directory, и для пользователей Office 365. Однако если вы хотите использовать преимущества [дополнительных функций](multi-factor-authentication-whats-next.md), необходимо приобрести полную версию службы многофакторной идентификации Microsoft Azure (MFA).

> [!NOTE]
> Поставщик Azure MFA используется для получения всех преимуществ полной версии Azure MFA. Он предназначен для пользователей, у которых **нет лицензий Azure MFA, Azure AD Premium или Enterprise Mobility Suite**.  Эти решения по умолчанию включают в себя полную версию Azure MFA.  Если у вас есть лицензии, вам не нужно создавать поставщик Azure Multi Factor Authentication.

Поставщик Azure Multi Factor Authentication требуется, если вы хотите скачать пакет SDK.

> [!IMPORTANT]
> Если вы хотите скачать пакет SDK, необходимо создать поставщик Azure Multi-Factor Authentication, даже если у вас уже есть лицензии Azure MFA, AAD Premium или EMS.  Если вы решили создать для этой цели поставщик Azure Multi-Factor Authentication и у вас уже есть лицензии, не забудьте создать поставщик на базе модели **На включенного пользователя**. Свяжите поставщик с каталогом, содержащим лицензии Azure MFA, Azure AD Premium или EMS.  В таком случае с вас будет взиматься плата, только если количество уникальных пользователей пакета SDK превысит количество лицензий, которыми вы владеете.

## <a name="create-a-multi-factor-auth-provider"></a>Создание поставщика многофакторной идентификации
Ниже показано, как создать поставщик Azure Multi Factor Authentication.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com) с учетной записью администратора.
2. Выберите **Active Directory**слева.
3. На странице Active Directory вверху откройте вкладку **Поставщики Azure Multi Factor Authentication**.
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. В нижней части страницы нажмите кнопку **Создать**.
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. В разделе Службы приложений выберите элемент **Поставщик Multi Factor Authentication**
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Выберите **Быстрое создание**.
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. Заполните перечисленные ниже поля и нажмите кнопку **Создать**.
   1. **Имя** — имя поставщика Multi-Factor Authentication.
   2. **Модель использования** – выберите, хотите ли вы включить отдельных пользователей или оплачивать каждую проверку. Выберите один из способов:
      * "На проверку подлинности" — покупательская модель, в которой оплачивается каждая проверка подлинности. Обычно используется в сценариях, использующих Azure Multi-Factor Authentication в потребительском приложении.
      * "На включенного пользователя" — покупательская модель, в которой оплачивается каждый включенный пользователь. Обычно используется для доступа сотрудников к таким приложениям, как Office 365. Выберите этот параметр, если некоторые пользователи уже имеют лицензию на Azure MFA.
   3. **Каталог** — клиент Azure Active Directory, с которым связан поставщик Multi-Factor Authentication. Обратите внимание на такие моменты:
      * Каталог Azure AD не требуется для создания поставщика Multi-Factor Authentication. Если вы планируете использовать только сервер Azure Multi-Factor Authentication или пакет SDK, это поле можно оставить пустым.
      * Чтобы пользоваться преимуществами расширенных функций, необходимо связать поставщик Azure MFA с каталогом Azure AD.
      * Средства синхронизации Azure AD Connect, AAD Sync или DirSync требуются только при синхронизации локальной среды Active Directory с каталогом Azure AD.  Если вы используете только один несинхронизированный каталог Azure AD, синхронизация не требуется.
        ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. После нажатия кнопки "Создать" будет создан поставщик Multi-Factor Authentication и появится сообщение **Поставщик Multi-Factor Authentication успешно создан**. Нажмите кнопку **ОК**.
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)




<!--HONumber=Feb17_HO3-->


