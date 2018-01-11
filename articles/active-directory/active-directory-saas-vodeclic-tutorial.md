---
title: "Учебник: Интеграция Azure Active Directory с Vodeclic | Документы Microsoft"
description: "Подробные сведения о настройке единого входа в Azure Active Directory и Vodeclic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Учебник: Интеграция Azure Active Directory с Vodeclic

В этом учебнике вы узнаете, как интегрировать Vodeclic с Azure Active Directory (Azure AD).

Интеграция с Azure AD Vodeclic предоставляет следующие преимущества:

- Можно управлять в Azure AD, который имеет доступ к Vodeclic.
- Позволяет пользователям автоматически получить вход для Vodeclic (единого входа или единого входа) с помощью своих учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>Технические условия

Чтобы настроить интеграцию Azure AD с Vodeclic, необходимы следующие элементы:

- подписка Azure AD;
- Подписки с поддержкой Vodeclic единого входа

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Vodeclic из галереи
2. настройка и проверка единого входа в Azure AD.

## <a name="add-vodeclic-from-the-gallery"></a>Добавление Vodeclic из коллекции
Чтобы настроить интеграцию Vodeclic в Azure AD, необходимо добавить Vodeclic из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Vodeclic из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, выберите **новое приложение** кнопку в верхней части окна.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Vodeclic**. Выберите **Vodeclic** из панели «результаты», а затем выберите **добавить** кнопку, чтобы добавить приложение.

    ![Vodeclic в списке результатов](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе настройки и тестирования в Azure AD единого входа с Vodeclic основании тестового пользователя с именем «Britta Simon».

Azure AD для единого входа для работы, необходимо знать, кто является пользователем аналога в Vodeclic для пользователя в Azure AD. Другими словами необходимо установить связь между пользователем Azure AD и соответствующих пользователей в Vodeclic.

Vodeclic, дает значение **Username** то же значение, как **имя пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями.

Для настройки и проверки Azure AD единого входа с Vodeclic, выполните следующие стандартные блоки.

1. [Настройка Azure AD единого входа](#configure-azure-ad-single-sign-on) для предоставления пользователям для использования этой функции.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Vodeclic](#create-a-vodeclic-test-user) на аналог Саймон Britta в Vodeclic, связанного с представлением Azure AD пользователя.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверка единого входа](#test-single-sign-on) позволяет убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе включения Azure AD единого входа на портале Azure и настройки единого входа в приложении Vodeclic.

**Чтобы настроить Azure AD единого входа с Vodeclic, выполните следующие действия:**

1. На портале Azure на **Vodeclic** странице интеграции приложений выберите **единого входа**.

    ![Ссылка "Настройка единого входа"][4]

2. В **единого входа** диалогового **-единого входа режим**выберите **входа на базе SAML** для включения единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Если вы хотите настроить приложение в **IDP** инициировал режим, в **URL-адреса и домена Vodeclic** статьи, выполните следующие действия:

    ![Домен Vodeclic и URL-адреса единого входа сведения](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. В поле **Идентификатор** введите URL-адрес в следующем формате: `https://<companyname>.lms.vodeclic.net/auth/saml`.

    Б. В поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Если вы хотите настроить приложение в **SP** инициируемых режиме, **Показывать дополнительные параметры URL-адреса** флажок и выполнить следующие шаги:

    ![Домен Vodeclic и URL-адреса единого входа сведения](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname>.lms.vodeclic.net/auth/saml`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Обновите эти значения фактический идентификатор, URL-адрес ответа и URL-адрес входа. Обратитесь к [Vodeclic клиента поддержки](mailto:hotline@vodeclic.com) для получения этих значений.

5. В разделе **Сертификат подписи SAML** выберите **XML метаданных**. Затем сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Для настройки единого входа на **Vodeclic** стороны, отправьте загруженного **метаданные в формате XML** для [Vodeclic поддержки](mailto:hotline@vodeclic.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткая версия этих инструкций теперь также доступна на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory** > **Корпоративные приложения** выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в статье [Управление параметрами единого входа для корпоративных приложений]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Чтобы отобразился список пользователей, выберите элемент **Пользователи и группы**. Затем выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-vodeclic-test-user"></a>Создание тестового пользователя Vodeclic

В этом разделе создайте пользователя с именем Саймон Britta в Vodeclic. Работать с [Vodeclic поддержки](mailto:hotline@vodeclic.com) для добавления пользователей в платформе Vodeclic. Перед использованием единого входа необходимо создать и активировать пользователей.

> [!NOTE]
> Согласно требованиям приложения может потребоваться получить входят вашего компьютера. Для это нежелательно, которым вы хотите предоставить открытый IP-адреса с [Vodeclic поддержки](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе включите Саймон Britta для использования Azure единого входа, предоставление доступа к Vodeclic.

![Назначение роли пользователя][200] 

**Чтобы назначить Саймон Britta Vodeclic, выполните следующие действия:**

1. На портале Azure откройте представление приложений и перейдите к представлению каталога. Затем перейдите в колонку **Корпоративные приложения** и выберите **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Vodeclic**.

    ![Vodeclic ссылку в список приложений](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

При выборе Vodeclic плитки на панели доступа можно получить автоматически входить в приложение Vodeclic.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

