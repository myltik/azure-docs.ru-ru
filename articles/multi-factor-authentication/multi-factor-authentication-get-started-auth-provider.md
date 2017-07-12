---
title: "Начало работы с поставщиком многофакторной идентификации Microsoft Azure | Документация Майкрософт"
description: "Из этой статьи вы узнаете, как создать поставщик Azure Multi Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 977640041f4b58a751848c96e2aa48eb2b284154
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017

---

<a id="getting-started-with-an-azure-multi-factor-auth-provider" class="xliff"></a>

# Начало работы с поставщиком Azure Multi Factor Authentication
Двухфакторная проверка подлинности доступна по умолчанию для глобальных администраторов, у которых есть служба Azure Active Directory, и для пользователей Office 365. Однако если вы хотите использовать преимущества [дополнительных функций](multi-factor-authentication-whats-next.md), необходимо приобрести полную версию службы многофакторной идентификации Microsoft Azure (MFA).

Поставщик Azure MFA используется для получения всех преимуществ полной версии Azure MFA. Он предназначен для пользователей, у которых **нет лицензий Azure MFA, Azure AD Premium или Enterprise Mobility + Security (EMS)**.  Эти решения по умолчанию включают в себя полную версию Azure MFA. Если у вас есть лицензии, вам не нужно создавать поставщик Azure Multi Factor Authentication.

Поставщик Azure Multi Factor Authentication требуется, если вы хотите скачать пакет SDK.

> [!IMPORTANT]
> Если вы хотите скачать пакет SDK, необходимо создать поставщик Azure Multi-Factor Authentication, даже если у вас уже есть лицензии Azure MFA, AAD Premium или EMS.  Если вы решили создать для этой цели поставщик Azure Multi-Factor Authentication и у вас уже есть лицензии, не забудьте создать поставщик на базе модели **На включенного пользователя**. Свяжите поставщик с каталогом, содержащим лицензии Azure MFA, Azure AD Premium или EMS. При такой конфигурации с вас будет взиматься плата, только если количество уникальных пользователей, использующих двухфакторную проверку подлинности, превысит количество лицензий, которыми вы владеете.

<a id="what-is-an-azure-multi-factor-auth-provider" class="xliff"></a>

## Что такое поставщик Azure Multi Factor Authentication?

Если у вас нет лицензий Многофакторной идентификации Azure, вы можете создать поставщик для обязательной двухфакторной проверки подлинности пользователей. Если вы разрабатываете пользовательское приложение и хотите включить Azure MFA, создайте поставщик проверки подлинности и [скачайте пакет SDK](multi-factor-authentication-sdk.md).

Поставщики проверки подлинности бывают двух типов. Их отличие заключается в способе оплаты за подписку Azure. При оплате за каждую проверку подлинности вычисляется количество событий проверки подлинности, выполненных для клиента в течение месяца. Этот вариант подойдет, если проверка подлинности редко выполняется для определенного числа пользователей. Например, если MFA требуется для пользовательского приложения. При оплате за каждого пользователя вычисляется количество пользователей в клиенте, выполнявших двухфакторную проверку подлинности в течение месяца. Этот вариант подойдет, если количества имеющихся лицензий для пользователей недостаточно для MFA.

<a id="create-a-multi-factor-auth-provider" class="xliff"></a>

## Создание поставщика многофакторной идентификации
Ниже показано, как создать поставщик Azure Multi Factor Authentication.

1. Войдите на [классический портал Azure](https://manage.windowsazure.com) с учетной записью администратора.
2. Выберите **Active Directory**слева.
3. На странице Active Directory вверху откройте вкладку **Поставщики Azure Multi Factor Authentication**.
   
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. В нижней части страницы нажмите кнопку **Создать**.
   
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. В разделе "Службы приложений" выберите элемент **Поставщик Multi Factor Authentication**
   
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Выберите **Быстрое создание**.
   
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Заполните перечисленные ниже поля и нажмите кнопку **Создать**.
   1. **Имя** — имя поставщика Multi-Factor Authentication.
   2. **Модель использования** — выберите один из вариантов:
      * "На проверку подлинности" — покупательская модель, в которой оплачивается каждая проверка подлинности. Обычно используется в сценариях, использующих Azure Multi-Factor Authentication в потребительском приложении.
      * "На включенного пользователя" — покупательская модель, в которой оплачивается каждый включенный пользователь. Обычно используется для доступа сотрудников к таким приложениям, как Office 365. Выберите этот параметр, если некоторые пользователи уже имеют лицензию на Azure MFA.
   3. **Каталог** — клиент Azure Active Directory, с которым связан поставщик Multi-Factor Authentication. Обратите внимание на такие моменты:
      * Каталог Azure AD не требуется для создания поставщика Multi-Factor Authentication. Если вы планируете использовать только сервер Azure Multi-Factor Authentication или пакет SDK, это поле можно оставить пустым.
      * Чтобы пользоваться преимуществами расширенных функций, необходимо связать поставщик Azure MFA с каталогом Azure AD.
      * Средства синхронизации Azure AD Connect, AAD Sync или DirSync требуются только при синхронизации локальной среды Active Directory с каталогом Azure AD.  Если вы используете только один несинхронизированный каталог Azure AD, синхронизация не требуется.
        
        ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. После нажатия кнопки "Создать" будет создан поставщик Multi-Factor Authentication и появится сообщение **Поставщик Multi-Factor Authentication успешно создан**. Нажмите кнопку **ОК**.
   
   ![Создание поставщика MFA](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

