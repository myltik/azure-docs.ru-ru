---
title: Руководство по интеграции Azure Active Directory с Symantec Web Security Service (WSS) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Symantec Web Security Service (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 0dc75a1091c56ddbbffcd54047b2465ece231254
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Руководство по интеграции Azure Active Directory с Symantec Web Security Service (WSS)

Изучив это руководство, вы сможете интегрировать учетную запись Symantec Web Security Service (WSS) с учетной записью Azure Active Directory (Azure AD). После этого WSS сможет выполнять аутентификацию SAML для конечных пользователей, подготовленных в Azure AD, и применять к ним правила политик на уровне пользователя или группы.

Интеграция Azure AD с Symantec Web Security Service (WSS) обеспечивает следующие преимущества:

- Управление через портал Azure AD всеми конечными пользователями и группами, используемыми в учетной записи WSS. 

- Аутентификация конечных пользователей в WSS с учетными данными из Azure AD.

- Применение правил политики на уровне пользователей и групп, определенных в учетной записи WSS.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Symantec Web Security Service (WSS), вам потребуется:

- подписка Azure AD;
- Учетная запись Symantec Web Security Service (WSS)

> [!NOTE]
> Мы не рекомендуем для действий, описанных в этом руководстве, использовать учетную запись WSS, которая используется для производственных целей.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Без крайней необходимости не используйте для этого теста учетную запись WSS, которая используется в производственных целях.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
Выполнив это руководство, вы настроите единый вход в Azure AD, что позволит выполнять вход в учетную запись WSS с учетными данными, указанными в Azure AD.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление из коллекции приложения Symantec Web Security Service (WSS)
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Добавление Symantec Web Security Service (WSS) из коллекции
Чтобы настроить интеграцию Symantec Web Security Service (WSS) с Azure AD, вам потребуется добавить Symantec Web Security Service (WSS) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Symantec Web Security Service (WSS) из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Symantec Web Security Service (WSS)**, затем выберите **Symantec Web Security Service (WSS)** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Служба Symantec Web Security Service (WSS) в списке результатов](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Symantec Web Security Service (WSS) с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Symantec Web Security Service (WSS) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Symantec Web Security Service (WSS).

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Symantec Web Security Service (WSS).

Чтобы настроить и проверить единый вход Azure AD в Symantec Web Security Service (WSS), вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Symantec Web Security Service (WSS)](#create-a-symantec-web-security-service-wss-test-user)** требуется для того, чтобы в Symantec Web Security Service (WSS) существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Symantec Web Security Service (WSS).

**Чтобы настроить единый вход Azure AD в Symantec Web Security Service (WSS), выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Symantec Web Security Service (WSS)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Symantec Web Security Service (WSS)** сделайте следующее.

    ![Информация о домене и URL-адресах для единого входа в службе Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес `https://saml.threatpulse.net:8443/saml/saml_realm`.

    Б. В текстовом поле **URL-адрес ответа** введите URL-адрес `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`.

    > [!NOTE]
    > Если по какой-либо причине предложенные значения **идентификатора** и **URL-адреса ответа** не работают, обратитесь в [службу поддержки Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Чтобы настроить единый вход на стороне службы безопасности Symantec Web Symantec Web Security Service (WSS), воспользуйтесь электронной документацией по WSS. Скачанный **файл метаданных в формате XML** следует импортировать на портал WSS. Если вам потребуется помощь в использовании портала WSS, обратитесь в [службу поддержки Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Создание тестового пользователя Symantec Web Security Service (WSS)

В этом разделе описано, как создать пользователя Britta Simon в Symantec Web Security Service (WSS). Соответствующее имя пользователя можно создать вручную на портале WSS. Также можно дождаться автоматической синхронизации с порталом WSS всех пользователей и групп, подготовленных в Azure AD (на это потребуется несколько минут, обычно около 15). Перед использованием единого входа необходимо создать и активировать пользователей. Общедоступный IP-адрес компьютера конечного пользователя, который будет использоваться для просмотра веб-сайтов, также должен быть подготовлен на портале Symantec Web Security Service (WSS).

> [!NOTE]
> [Щелкните здесь](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1), чтобы получить общедоступный IP-адрес своего компьютера.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Symantec Web Security Service (WSS).

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Symantec Web Security Service (WSS), выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Symantec Web Security Service (WSS)**.

    ![Ссылка на Symantec Web Security Service (WSS) в списке приложений](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе вы проверите работу единого входа, который вы настроили для учетной записи WSS, чтобы для нее выполнялась аутентификация SAML через Azure AD.

После того как вы настроите в веб-браузере передачу трафика через WSS, при попытке перехода на любой сайт будет открываться страница входа в Azure. Введите учетные данные тестового конечного пользователя, которые вы подготовили в Azure AD (в нашем примере это BrittaSimon). После аутентификации вы получите доступ к просмотру выбранного веб-сайта. Если вы создадите правило политики, которое запрещает пользователю BrittaSimon просмотр определенного сайта, при попытке входа на этот сайт с учетными данными BrittaSimon вы должны увидеть страницу блокировки WSS.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

