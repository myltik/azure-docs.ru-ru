---
title: "Руководство по интеграции Azure Active Directory c Google Apps | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 1d92e673a948dd139ff2d4a24f2e602180be43c5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Руководство по интеграции Azure Active Directory с Google Apps

В этом руководстве описано, как интегрировать приложение Google Apps с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Google Apps обеспечивает следующие преимущества:

- С помощью Azure AD вы можете управлять доступом к Google Apps.
- Вы можете включить автоматический вход пользователей в Google Apps (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Google Apps, вам потребуется:

- подписка Azure AD;
- подписка Google Apps с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="video-tutorial"></a>Видеоруководство
Как включить единый вход в Google Apps за 2 минуты.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
1. **Вопрос. Поддерживают ли Chromebooks и другие устройства Chrome единый вход Azure AD?**
   
    Ответ. Да, пользователи могут войти на свои устройства Chromebook, используя свои учетные данные Azure AD. Сведения о том, почему учетные данные могут быть запрошены у пользователей дважды, см. в этой [статье о поддержке Google Apps](https://support.google.com/chrome/a/answer/6060880).

2. **Вопрос. Если включить единый вход, смогут ли пользователи использовать свои учетные данные Azure AD для входа в любой продукт Google, такой как Google Classroom, GMail, Google Drive, YouTube и т. д?**
   
    Ответ. Да, в зависимости от того, [какие приложения Google](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) вы решили включить или отключить для своей организации.

3. **Вопрос. Можно ли включить единый вход только для подмножества пользователей Google Apps?**
   
    Ответ. Нет, при включении единого входа всем пользователям Google Apps вашей организации сразу же потребуется использовать свои учетные данные Azure AD для аутентификации. Поскольку Google Apps не поддерживает наличие нескольких поставщиков удостоверений, то в качестве поставщика удостоверений для среды Google Apps может быть использован либо Azure AD, либо Google — но не оба одновременно.

4. **Вопрос. Если пользователь выполнил вход в Windows, пройдет ли он автоматическую аутентификацию в Google Apps без запроса пароля?**
   
    Ответ. Существует два варианта включения этого сценария. Во-первых, пользователи могут входить в устройства Windows 10 посредством [присоединения к Azure Active Directory](active-directory-azureadjoin-overview.md). Кроме того, пользователи могут входить на устройства с Windows, присоединенные к домену локального каталога Active Directory, для которого разрешен единый вход Azure AD посредством развертывания [служб федерации Active Directory (AD FS)](active-directory-aadconnect-user-signin.md). В обоих случаях необходимо выполнить действия следующего руководства для включения единого входа между Azure AD и Google Apps.

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Google Apps из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="adding-google-apps-from-the-gallery"></a>Добавление Google Apps из коллекции
Чтобы настроить интеграцию Google Apps с Azure AD, необходимо добавить Google Apps из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Google Apps из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Google Apps**, выберите **Google Apps** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Google Apps в списке результатов](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Google Apps с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Google Apps соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Google Apps.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Google Apps.

Чтобы настроить и проверить единый вход Azure AD в Google Apps, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Google Apps](#create-a-google-apps-test-user)** требуется для того, чтобы в Google Apps существовал пользователь Britta Simon, связанный с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Google Apps.

**Чтобы настроить единый вход Azure AD в Google Apps, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Google Apps** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Google Apps** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    а. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://mail.google.com/a/<yourdomain.com>`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | |
    |--|
    | `http://google.com/a/<yourdomain.com>`|
    | `http://google.com`|    
    | `google.com/<yourdomain.com>`|
    | `google.com`|

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Google Apps](https://www.google.com/contact/). 

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Google Apps** щелкните **Настроить Google Apps**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, URL-адрес службы единого входа SAML и URL-адрес изменения пароля** из раздела **Краткий справочник**.

    ![Настройка Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. Откройте в браузере новую вкладку и войдите в [консоль администратора Google Apps](http://admin.google.com/) с использованием учетной записи администратора.

8. Щелкните **Security**(Безопасность). Если эта ссылка не отображается, она может быть скрыта в меню **More Controls** (Другие элементы управления) в нижней части экрана.
   
    ![Выбор элемента "Безопасность"][10]

9. На странице **Security** (Безопасность) выберите **Set up single sign-on (SSO)** (Настроить единый вход).
   
    ![Выбор элемента "Единый вход"][11]

10. Выполните следующие настройки.
   
    ![Настройка единого входа][12]
   
    а. Выберите **Setup SSO with third party identity provider**(Настройка единого входа с помощью стороннего поставщика удостоверений).

    b. В поле **Sign-in page URL** (URL-адрес страницы входа) в Google Apps вставьте значение поля **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. В поле **Sign-out page URL** (URL-адрес страницы выхода) в Google Apps вставьте значение поля **URL-адрес выхода**, скопированное на портале Azure. 

    d. В поле **Change password URL** (URL-адрес изменения пароля) в Google Apps вставьте значение поля **Изменить URL-адрес пароля**, скопированное на портале Azure. 

    д. В Google Apps в поле **Verification certificate** (Сертификат проверки) передайте сертификат, скачанный с портала Azure.

    f. Нажмите кнопку **Сохранить изменения**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    а. В поле **Имя** введите **BrittaSimon**.

    b. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    г) Щелкните **Создать**.
 
### <a name="create-a-google-apps-test-user"></a>Создание тестового пользователя Google Apps

Цель этого раздела — создать пользователя с именем Britta Simon в Google Apps. Google Apps поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Google Apps еще не существует, он создается при попытке доступа к Google Apps.

>[!NOTE] 
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Google Apps.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Google Apps, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Google Apps**.

    ![Ссылка на Google Apps в списке приложений](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Google Apps на панели доступа, вы автоматически войдете в приложение Google Apps.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

