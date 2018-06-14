---
title: Руководство по интеграции Azure Active Directory с TargetProcess | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и TargetProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 7cb91628-e758-480d-a233-7a3caaaff50d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 0f2dba144f0b7ff70e7c9996e82d0f841a76854b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346536"
---
# <a name="tutorial-azure-active-directory-integration-with-targetprocess"></a>Руководство. Интеграция Azure Active Directory с TargetProcess

В этом учебнике описано, как интегрировать приложение TargetProcess с Azure Active Directory (Azure AD).

Интеграция TargetProcess с Azure AD дает приведенные далее преимущества:

- С помощью Azure AD вы можете контролировать доступ к TargetProcess.
- Вы можете включить автоматический вход пользователей в TargetProcess (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с приложением TargetProcess, вам потребуется следующее:

- подписка Azure AD;
- подписка TargetProcess с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление TargetProcess из коллекции.
2. Настройка и проверка единого входа в Azure AD

## <a name="add-targetprocess-from-the-gallery"></a>Добавление TargetProcess из коллекции
Чтобы настроить интеграцию приложения TargetProcess с Azure AD, необходимо добавить TargetProcess из коллекции в список управляемых приложений SaaS.

**Чтобы добавить TargetProcess из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **TargetProcess**, выберите **TargetProcess** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Добавление TargetProcess из коллекции](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в TargetProcess с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в TargetProcess соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в TargetProcess.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в TargetProcess.

Чтобы настроить и проверить единый вход Azure AD в TargetProcess, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя TargetProcess](#create-a-targetprocess-test-user)** требуется для того, чтобы в TargetProcess существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении TargetProcess.

**Чтобы настроить единый вход Azure AD в TargetProcess, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **TargetProcess** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Параметр "Вход на основе SAML"](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_samlbase.png)

3. В разделе **Домены и URL-адреса приложения TargetProcess** сделайте следующее.

    ![Раздел "Домены и URL-адреса приложения TargetProcess"](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.tpondemand.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.tpondemand.com/`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов TargetProcess](mailto:support@targetprocess.com). 
 
4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Раздел "Сертификат подписи SAML"](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить"](./media/active-directory-saas-target-process-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация TargetProcess** щелкните **Настроить TargetProcess**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Раздел "Конфигурация TargetProcess"](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_configure.png) 

7. Войдите в приложение TargetProcess с правами администратора.

8. В верхнем меню щелкните **Настройка**.
   
    ![Настройка](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_05.png)

9. Щелкните **Параметры**.
   
    ![Параметры](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_06.png) 

10. Щелкните **Single Sign-on**(Единый вход).
   
    ![Щелчок "Single Sign-on" (Единый вход)](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_07.png) 

11. В диалоговом окне "Параметры единого входа" выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-target-process-tutorial/tutorial_target_process_08.png)
    
    a. Щелкните **Enable Single Sign-on**(Включить единый вход).
    
    Б. В текстовое поле **Sign-on URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    c. Откройте скачанный сертификат в Блокноте, скопируйте его содержимое в буфер обмена, а затем вставьте в текстовое поле **Certificate** (Сертификат).
    
    d. Установите флажок **Enable JIT Provisioning**(Включить JIT-подготовку).

    д. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-target-process-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Отображение списка пользователей](./media/active-directory-saas-target-process-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Кнопка "Добавить"](./media/active-directory-saas-target-process-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Раздел "Пользователь"](./media/active-directory-saas-target-process-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-targetprocess-test-user"></a>Создание тестового пользователя TargetProcess

Цель этого раздела — создать в приложении TargetProcess пользователя с именем Britta Simon.

Приложение TargetProcess поддерживает JIT-подготовку. Эта функция уже включена в ходе [настройки единого входа Azure AD](#configuring-azure-ad-single-sign-on).

В этом разделе никакие действия с вашей стороны не требуются.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к TargetProcess.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению TargetProcess, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **TargetProcess**.

    ![TargetProcess в списке приложений](./media/active-directory-saas-target-process-tutorial/tutorial_target-process_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент TargetProcess на панели доступа, вы автоматически войдете в приложение TargetProcess. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-target-process-tutorial/tutorial_general_203.png

