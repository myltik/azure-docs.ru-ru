---
title: "Руководство по интеграции Azure Active Directory с TOPdesk — Secure | Документация Майкрософт"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и безопасной версии TOPdesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: ca3362bc3f966adaf9940f6eb4bec5235c6ea7d8
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Учебник. Интеграция Azure Active Directory с TOPdesk — Secure

В этом учебнике вы узнаете, как интегрировать TOPdesk - Secure с Azure Active Directory (Azure AD).

Интеграция безопасной версии TOPdesk с Azure AD предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к безопасной версии TOPdesk.
- Позволяет пользователям автоматически получить вошедшего в безопасной версии TOPdesk (единый вход) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграция Azure AD с версией TOPdesk - безопасным, требуются следующие элементы:

- подписка Azure AD;
- Подписка TOPdesk — Secure с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TOPdesk — безопасный из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-topdesk---secure-from-the-gallery"></a>Добавление TOPdesk — безопасный из галереи
Для настройки интеграции TOPdesk - Secure в Azure AD, необходимо добавить TOPdesk — безопасный из коллекции в список управляемых SaaS приложений.

**Чтобы добавить TOPdesk — безопасный из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **безопасной версии TOPdesk**выберите **безопасной версии TOPdesk** из панели результатов щелкните **добавить** кнопку, чтобы добавить приложение.

    ![TOPdesk - Secure в списке результатов](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе Настройка и тестирование Azure AD единого входа с версией TOPdesk — безопасный на основе на тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать пользователя аналога в безопасной версии TOPdesk с пользователем в Azure AD. Другими словами связи между пользователя Azure AD и соответствующих пользователей в безопасной версии TOPdesk необходимо установить.

В версии TOPdesk - Secure, присвойте значение **имя пользователя** в Azure AD в качестве значения **Username** для установления связи.

Для настройки и проверки Azure AD единого входа с версией TOPdesk - уровень безопасности, необходимо выполнить следующие стандартные блоки:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание TOPdesk — безопасный тестового пользователя](#create-a-topdesk---secure-test-user)**  — иметь аналог Саймон Britta в безопасной версии TOPdesk, связанного с представлением Azure AD пользователя.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе вы включите Azure AD единого входа на портале Azure и настроить единый вход в ваш безопасную версию TOPdesk.

**Для настройки Azure AD единого входа с версией TOPdesk - Secure, выполните следующие действия:**

1. На портале Azure на **безопасной версии TOPdesk** странице интеграции приложения щелкните **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. На **TOPdesk - Secure доменов и URL-адреса** выполните следующие действия:

    ![TOPdesk — безопасный домен и URL-адреса единого входа сведения](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.topdesk.net/tas/public/login/saml`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. URL-адрес ответа описан далее в учебнике. Обратитесь к [TOPdesk - группа поддержки защиты клиента](http://www.topdesk.com/us/support) для получения этих значений. 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_400.png)

6. На **TOPdesk - Secure конфигурации** щелкните **Настройка безопасной версии TOPdesk** Открытие **Настройка входа** окна. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![TOPdesk — безопасный конфигурации](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Выполните вход на веб-сайт **TOPdesk — Secure** своей компании в качестве администратора.

8. В меню **TOPdesk** выберите пункт **Settings** (Параметры).

    ![Параметры](./media/active-directory-saas-topdesk-secure-tutorial/ic790598.png "Параметры")

9. Щелкните **Параметры входа**.

    ![Параметры входа](./media/active-directory-saas-topdesk-secure-tutorial/ic790599.png "Параметры входа")

10. Разверните меню **Login Settings** (Параметры входа), а затем выберите **General** (Общие).

    ![Общие](./media/active-directory-saas-topdesk-secure-tutorial/ic790600.png "Общие")

11. В разделе **Secure** (Безопасность) для конфигурации **SAML login** (Вход SAML) сделайте следующее.

    ![Технические параметры](./media/active-directory-saas-topdesk-secure-tutorial/ic790855.png "Технические параметры")
   
    a. Нажмите кнопку **Скачать** , чтобы скачать общедоступный файл метаданных, а затем сохраните его локально на компьютере.
   
    Б. Откройте файл метаданных и определите местоположение узла **AssertionConsumerService** .
    
    ![Служба обработчика утверждений](./media/active-directory-saas-topdesk-secure-tutorial/ic790856.png "Служба обработчика утверждений")
   
    c. Копировать **AssertionConsumerService** значений, вставьте это значение в текстовое поле URL-адрес ответа в **TOPdesk - Secure доменов и URL-адреса** раздела.

12. Чтобы создать файл сертификата, выполните следующие действия:
    
    ![Сертификат](./media/active-directory-saas-topdesk-secure-tutorial/ic790606.png "Сертификат")
    
    a. Откройте скачанный файл метаданных на портале Azure.

    Б. Разверните узел **RoleDescriptor**, содержащий **xsi:type** со значением **fed:ApplicationServiceType**.

    c. Скопируйте значение узла **X509Certificate** .

    d. Сохраните скопированное значение узла **X509Certificate** локально в файл на компьютере.

13. В разделе **Public** (Общедоступные) щелкните **Add** (Добавить).
    
    ![Добавление](./media/active-directory-saas-topdesk-secure-tutorial/ic790607.png "Добавление")

14. На странице диалогового окна **Помощник настройки SAML** сделайте следующее:
    
    ![Помощник по настройке SAML](./media/active-directory-saas-topdesk-secure-tutorial/ic790608.png "Помощник по настройке SAML")
    
    a. Чтобы отправить скачанный файл метаданных через портал Azure, напротив пункта **Federation Metadata** (Метаданные федерации) нажмите кнопку **Browse** (Обзор).

    Б. Чтобы отправить файл сертификата, напротив пункта **Certificate (RSA)** (Сертификат (RSA)) нажмите кнопку **Browse** (Обзор).

    c. Чтобы отправить файл с логотипом, полученный от службы поддержки TOPdesk, напротив пункта **Logo icon** (Значок логотипа) нажмите кнопку **Browse** (Обзор).

    d. В текстовое поле **Атрибут имени пользователя** введите значение `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    д. В текстовом поле **Отображаемое имя** введите имя конфигурации.

    f. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-topdesk---secure-test-user"></a>Создание TOPdesk — безопасный тестового пользователя

Чтобы пользователи Azure AD могли выполнять вход в систему TOPdesk — Secure, они должны быть подготовлены для нее.  
В случае TOPdesk — Secure подготовка пользователей осуществляется вручную.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.
1. Выполните вход на веб-сайт **TOPdesk — Secure** компании в качестве администратора.
2. В меню в верхней части страницы щелкните **TOPdesk \> New (Создать) \> Support Files (Файлы поддержки) \> Operator (Оператор)**.
   
    ![Оператор](./media/active-directory-saas-topdesk-secure-tutorial/ic790610.png "Оператор")

3. В диалоговом окне **Новый оператор** выполните следующие действия:
   
    ![Новый оператор](./media/active-directory-saas-topdesk-secure-tutorial/ic790611.png "Новый оператор")
   
    a. Нажмите кнопку **Общие** вкладки.
   
    Б. В **Фамилия** тип фамилию пользователя в текстовое поле, например **Simon**.
   
    c. Выберите для этой учетной записи **Site** (Веб-сайт) в разделе **Location** (Расположение).
   
    d. В текстовом поле **Login Name** (Имя для входа в систему) в разделе **TOPdesk Login** (Вход в TOPdesk) введите имя для входа пользователя.
   
    д. Выберите команду **Сохранить**.

> [!NOTE]
> Вы можете использовать любые другие инструменты создания учетных записей пользователей TOPdesk — Secure или API-интерфейсы, предоставляемые TOPdesk — Secure для подготовки учетных записей пользователя AAD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к безопасной версии TOPdesk.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta TOPdesk - Secure, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **безопасной версии TOPdesk**.

    ![TOPdesk — безопасный канал связи в списке приложений](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При нажатии кнопки TOPdesk — безопасный плитки на панели доступа вы должны получить автоматически вошедшего в вашей безопасную версию TOPdesk.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_203.png

