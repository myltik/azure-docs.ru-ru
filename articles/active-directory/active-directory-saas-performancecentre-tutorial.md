---
title: Руководство по интеграции Azure Active Directory с PerformanceCentre | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: afee73a18502c48a6ff933f269f2572333d14196
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349232"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Руководство по интеграции Azure Active Directory с PerformanceCentre

В этом руководстве описано, как интегрировать PerformanceCentre с Azure Active Directory (Azure AD).

Интеграция PerformanceCentre с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к PerformanceCentre.
- Вы можете включить автоматический вход пользователей в PerformanceCentre (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с PerformanceCentre, вам потребуется следующее:

- подписка Azure AD;
- подписка PerformanceCentre с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление PerformanceCentre из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-performancecentre-from-the-gallery"></a>Добавление PerformanceCentre из коллекции
Чтобы настроить интеграцию PerformanceCentre с Azure AD, вам потребуется добавить PerformanceCentre из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PerformanceCentre из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **PerformanceCentre**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_search.png)

5. На панели результатов выберите **PerformanceCentre** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение PerformanceCentre с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в PerformanceCentre соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PerformanceCentre.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PerformanceCentre.

Чтобы настроить и проверить единый вход в Azure AD в PerformanceCentre, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя PerformanceCentre](#creating-a-performancecentre-test-user)** требуется для того, чтобы в PerformanceCentre существовал пользователь Britta Simon, связанный с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении PerformanceCentre.

**Чтобы настроить единый вход Azure AD в PerformanceCentre, выполните следующие действия.**

1. На портале Azure на странице интеграции приложений **PerformanceCentre** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

3. В разделе **Домены и URL-адреса приложения PerformanceCentre** выполните следующие действия:

    ![Настройка единого входа](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `http://companyname.performancecentre.com/saml/SSO`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов PerformanceCentre](https://www.performancecentre.com/contact-us/). 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/active-directory-saas-performancecentre-tutorial/tutorial_general_400.png)

6. В разделе **Настройка PerformanceCentre** щелкните **Настройка PerformanceCentre**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка единого входа](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_configure.png) 

7. Войдите на корпоративный сайт **PerformanceCentre** с правами администратора.

8. На вкладке слева щелкните **Configure**(Настройка).
   
    ![единого входа Azure AD][10]

9. На вкладке слева щелкните **Miscellaneous** (Разное), а затем — **Single Sign On** (Единый вход).
   
    ![единого входа Azure AD][11]

10. В поле **Protocol** (Протокол) выберите **SAML**.
   
    ![единого входа Azure AD][12]

11. Откройте скачанный файл метаданных, скопируйте и вставьте его содержимое в текстовое поле **Identity Provider Metadata** (Метаданные поставщика удостоверений), а затем нажмите кнопку **Save** (Сохранить).
   
    ![единого входа Azure AD][13]

12. Проверьте правильность значений в полях **Entity Base URL** (Базовый URL-адрес сущности) и **Entity ID URL** (URL-адрес идентификатора сущности).
    
     ![единого входа Azure AD][14]

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-performancecentre-test-user"></a>Создание тестового пользователя PerformanceCentre

Цель этого раздела — создать пользователя с именем Britta Simon в PerformanceCentre.

**Чтобы создать пользователя с именем Britta Simon в PerformanceCentre, выполните следующие действия.**

1. Войдите на корпоративный сайт PerformanceCentre с правами администратора.

2. В меню слева выберите **Interrelate** (Установить взаимосвязь), а затем нажмите кнопку **Create Participant** (Создать участника).
   
    ![Создать пользователя][400]

3. В диалоговом окне **Interrelate — Create Participant** (Взаимосвязь — создание участника) выполните следующие действия.
   
    ![Создать пользователя][401]
    
    a. Введите необходимые атрибуты для пользователя Britta Simon в соответствующие текстовые поля.
    
    >[!IMPORTANT]
    >Атрибут User Name этого пользователя в PerformanceCentre должен соответствовать имени пользователя в Azure AD.
    
    Б. Выберите значение **Client Administrator** (Администратор клиента) в поле **Choose Role** (Выберите роль).
    
    c. В нижней части страницы нажмите кнопку **Save**. 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к PerformanceCentre, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в PerformanceCentre, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **PerformanceCentre**.

    ![Настройка единого входа](./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент PerformanceCentre на панели доступа, вы автоматически войдете в приложение PerformanceCentre.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png

