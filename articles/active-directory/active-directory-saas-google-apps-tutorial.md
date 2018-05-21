---
title: Руководство по интеграции Azure Active Directory с G Suite | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и G Suite.
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
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: f785a74d5cab7c2fbfb0e2e1036dfddbb0f4838b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Руководство. Интеграция Azure Active Directory с G Suite

В этом руководстве описано, как интегрировать G Suite с Azure Active Directory (Azure AD).

Интеграция приложения G Suite с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к G Suite.
- Вы можете включить автоматический вход пользователей в G Suite (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с G Suite, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа G Suite;
- подписка Google Apps или Google Cloud Platform.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы
1.  **Вопрос. Поддерживается ли интеграция единого входа Google Cloud Platform с Azure AD?**
    
    Ответ. Да. Google Cloud Platform и Google Apps используют одну и ту же платформу проверки подлинности. Таким образом, чтобы обеспечить интеграцию с Google Cloud Platform, необходимо настроить единый вход в Google Apps.


2. **Вопрос. Поддерживают ли Chromebooks и другие устройства Chrome единый вход Azure AD?**
   
    Ответ. Да, пользователи могут войти на свои устройства Chromebook, используя свои учетные данные Azure AD. Сведения о том, почему учетные данные могут быть запрошены у пользователей дважды, см. в этой [статье о поддержке G Suite](https://support.google.com/chrome/a/answer/6060880).

3. **Вопрос. Если включить единый вход, смогут ли пользователи использовать свои учетные данные Azure AD для входа в любой продукт Google, такой как Google Classroom, GMail, Google Drive, YouTube и т. д?**
   
    Ответ. Да, в зависимости от того, [какие приложения G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) вы решили включить или отключить для своей организации.

4. **Вопрос. Можно ли включить единый вход только для подмножества пользователей G Suite?**
   
    Ответ. Нет, при включении единого входа всем пользователям G Suite вашей организации сразу же потребуется использовать свои учетные данные Azure AD для проверки подлинности. Поскольку G Suite не поддерживает наличие нескольких поставщиков удостоверений, то в качестве поставщика удостоверений для среды G Suite можно использовать либо Azure AD, либо Google — но не оба одновременно.

5. **Вопрос. Если пользователь выполнил вход в Windows, пройдет ли он автоматическую проверку подлинности в G Suite без запроса пароля?**
   
    Ответ. Существует два варианта включения этого сценария. Во-первых, пользователи могут входить в устройства Windows 10 посредством [присоединения к Azure Active Directory](active-directory-azureadjoin-overview.md). Кроме того, пользователи могут входить на устройства с Windows, присоединенные к домену локального каталога Active Directory, для которого разрешен единый вход Azure AD посредством развертывания [служб федерации Active Directory (AD FS)](active-directory-aadconnect-user-signin.md). В обоих случаях необходимо выполнить действия следующего руководства для включения единого входа между Azure AD и G Suite.

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление G Suite из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-g-suite-from-the-gallery"></a>Добавление G Suite из коллекции
Чтобы настроить интеграцию приложения G Suite с Azure AD, вам нужно добавить это приложение из коллекции в свой список управляемых приложений SaaS.

**Чтобы добавить G Suite из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **G Suite**, выберите **G Suite** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![G Suite в списке результатов](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в G Suite с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в G Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в G Suite.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в G Suite.

Чтобы настроить и проверить единый вход Azure AD в G Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения G Suite](#create-a-g-suite-test-user)** требуется для того, чтобы в G Suite существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении G Suite.

**Чтобы настроить единый вход Azure AD в G Suite, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **G Suite** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. Чтобы настроить **Gmail**, в разделе **Домены и URL-адреса приложения G Suite** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_urlgmail.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [группу поддержки клиентов G Suite](https://www.google.com/contact/).

4. Чтобы настроить **Google Cloud Platform**, в разделе **Домены и URL-адреса приложения G Suite** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url1.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [группу поддержки клиентов G Suite](https://www.google.com/contact/). 

5. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

7. В разделе **Настройка G Suite** щелкните **Настроить G Suite**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, URL-адрес службы единого входа SAML и URL-адрес изменения пароля** из раздела **Краткий справочник**.

    ![Настройка G Suite](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

8. Откройте в браузере новую вкладку и войдите в [консоль администратора G Suite](http://admin.google.com/) с использованием учетной записи администратора.

9. Щелкните **Security**(Безопасность). Если эта ссылка не отображается, она может быть скрыта в меню **More Controls** (Другие элементы управления) в нижней части экрана.
   
    ![Выбор элемента "Безопасность"][10]

10. На странице **Security** (Безопасность) выберите **Set up single sign-on (SSO)** (Настроить единый вход).
   
    ![Выбор элемента "Единый вход"][11]

11. Выполните следующие настройки.
   
    ![Настройка единого входа][12]
   
    a. Выберите **Setup SSO with third party identity provider**(Настройка единого входа с помощью стороннего поставщика удостоверений).

    Б. В поле **Sign-in page URL** (URL-адрес страницы входа) в G Suite вставьте значение поля **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    c. В поле **Sign-out page URL** (URL-адрес страницы выхода) в G Suite вставьте значение поля **URL-адрес выхода**, скопированное на портале Azure. 

    d. В поле **Change password URL** (URL-адрес изменения пароля) в G Suite вставьте значение поля **Изменить URL-адрес пароля**, скопированное на портале Azure. 

    д. В G Suite в поле **Verification certificate** (Сертификат проверки) передайте сертификат, скачанный с портала Azure.

    f. Выберите **Use a domain specific issuer** (Использовать издателя определенного домена).

    ж. Нажмите кнопку **Сохранить изменения**.

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

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-g-suite-test-user"></a>Создание тестового пользователя G Suite

Цель этого раздела — создать пользователя с именем Britta Simon в G Suite. G Suite поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в G Suite еще не существует, он создается при попытке доступа к G Suite.

>[!NOTE] 
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Google](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе мы предоставим пользователю Britta Simon доступ к G Suite, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в приложении G Suite, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **G Suite**.

    ![Ссылка на G Suite в списке "Приложения"](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку G Suite на панели доступа, вы автоматически войдете в приложение G Suite.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

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

