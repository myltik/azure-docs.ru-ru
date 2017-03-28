---
title: "Приложение Microsoft Authenticator для мобильных телефонов | Документация Майкрософт"
description: "Узнайте, как выполнить обновление до последней версии Azure Authenticatior."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/25/2016
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6872b216ab2bfd5823b65b1bb70d503c7628ee13
ms.openlocfilehash: 26d9f04f9d3019ab5fcad48ef1e9821a4dd6a724
ms.lasthandoff: 03/01/2017

---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Начало работы с приложением Microsoft Authenticator
Приложение Microsoft Authenticator обеспечивает дополнительный уровень безопасности для учетной записи Azure (например, bsimon@contoso.onmicrosoft.com), для локальной рабочей учетной записи (например, bsimon@contoso.com) или для учетной записи Майкрософт (например, bsimon@outlook.com).

В приложении используется один из следующих методов:

* **Уведомление.** Приложение помогает предотвратить несанкционированный доступ к учетным записям и остановить мошеннические транзакции, отправив уведомление на смартфон или планшетный ПК. Просто просмотрите уведомление, и если оно подлинное, нажмите кнопку **Проверить**. В противном случае нажмите кнопку **Отклонить**. Сведения о том, как отклонить уведомление, см. в статье об использовании функции запрета и уведомления о мошенничестве для Многофакторной идентификации.
* **Пароль и код проверки**. Приложение можно использовать в качестве программного токена для создания кода проверки OATH. После ввода имени пользователя и пароля на экране входа нужно ввести код, предоставленный приложением. Код проверки выступает вторым методом проверки подлинности.

Теперь вместо приложения Azure Authenticator используется приложение Microsoft Authenticator.  Вы можете продолжать использовать приложение Azure Authenticator. Сведения в этой статье пригодятся вам, когда вы решите перейти к новому приложению Microsoft Authenticator.  

## <a name="install-the-app"></a>Установка приложения
Приложение Microsoft Authenticator доступно для [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) и [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Добавление учетной записи в приложение
Чтобы добавить учетную запись в приложение Microsoft Authenticator, следуйте одной из приведенных ниже процедур.

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Добавление личной учетной записи Майкрософт в приложение

Чтобы добавить личную учетную запись Майкрософт (используемую для входа в Outlook.com, Xbox, Skype и т. д.), нужно просто войти в нее в приложении Microsoft Authenticator.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Добавление рабочей или учебой учетной записи в приложение с использованием сканера QR-кода
1. Перейдите на страницу параметров проверки безопасности.  Инструкции по переходу на эту страницу см. в статье [Управление параметрами двухфакторной проверки подлинности](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Установите флажок **Приложение Azure Authenticator** и нажмите кнопку **Настроить**.

    ![Кнопка "Настроить" на странице параметров проверки безопасности](./media/authenticator-app-how-to/azureauthe.png)

    Откроется страница с QR-кодом.

    ![Окно с QR-кодом](./media/authenticator-app-how-to/barcode2.png)
3. Откройте приложение Microsoft Authenticator. В окне с **учетными записями** щелкните значок **+**, а затем укажите, что вы хотите добавить рабочую или учебную учетную запись.
4. Отсканируйте QR-код с помощью камеры, а затем нажмите кнопку **Готово** , чтобы закрыть окно с кодом.

    Если камера не работает должным образом, можно [ввести QR-код и URL-адрес вручную](#add-an-account-to-the-app-manually).

5. Когда в приложении отобразится имя пользователя с шестизначным кодом под ним, это означает, что все готово. 

    ![Окно с учетными записями](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Добавление учетной записи в приложение вручную
1. Перейдите на страницу параметров проверки безопасности.  Инструкции по переходу на эту страницу см. в статье [Управление параметрами двухфакторной проверки подлинности](multi-factor-authentication-end-user-manage-settings.md).
2. Нажмите кнопку **Настроить**.

    ![Кнопка "Настроить" на странице параметров проверки безопасности](./media/authenticator-app-how-to/azureauthe.png)

    Откроется страница с QR-кодом.  Запишите код и URL-адрес.

    ![Окно с QR-кодом и URL-адресом](./media/authenticator-app-how-to/barcode2.png)
3. Откройте приложение Microsoft Authenticator. В окне с **учетными записями** щелкните значок **+**, а затем укажите, что вы хотите добавить рабочую или учебную учетную запись.

4. В сканере щелкните **Или введите код вручную**.

    ![Окно для сканирования QR-кода](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Введите код и URL-адрес в соответствующих полях приложения и нажмите кнопку **Готово**.

    ![Окно ввода кода и URL-адреса](./media/authenticator-app-how-to/manual.png)

6. Когда в приложении отобразится имя пользователя с шестизначным кодом под ним, это означает, что все готово.

    ![Окно с учетными записями](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-touch-id"></a>Добавление учетной записи в приложение с использованием Touch ID
Приложение Microsoft Authenticator на iOS поддерживает Touch ID.  Многофакторная идентификация Azure позволяет организациям запрашивать ПИН-код для устройств. При использовании Touch ID пользователям iOS не нужно вводить ПИН-код. Вместо этого можно просканировать отпечатки пальцев и нажать кнопку **Утвердить**.

Настройка Touch ID в приложении Microsoft Authenticator выполняется очень просто. Обычная проверка заканчивается вводом ПИН-кода. Если устройство поддерживает Touch ID, Microsoft Authenticator автоматически настроит его для учетной записи.

![Проверка настройки Touch ID](./media/authenticator-app-how-to/touchid1.png)

С этого момента для подтверждения входа вместо ввода ПИН-кода вам нужно будет только коснуться полученного push-уведомления и просканировать отпечаток пальца.

![push-уведомление](./media/authenticator-app-how-to/touchid2.png)


