---
title: "Руководство по интеграции Azure Active Directory с PerformanceCentre | Документация Майкрософт"
description: "Узнайте, как настроить единый вход Azure Active Directory в PerformanceCentre."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 801c0f7085034cc3a3ed74722a11204f6421c27d


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Руководство по интеграции Azure Active Directory с PerformanceCentre
Цель этого учебника — показать, как интегрировать Azure Active Directory (Azure AD) с приложением PerformanceCentre.  

Интеграция PerformanceCentre с Azure AD обеспечивает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к PerformanceCentre. 
* Вы можете включить автоматический вход пользователей в PerformanceCentre (единый вход) с использованием учетной записи Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure Active Directory.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с PerformanceCentre, вам потребуется следующее:

* подписка Azure AD;
* подписка PerformanceCentre с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого учебника — научить вас проверять единый вход в Azure AD в пробной среде.  

Сценарий, описанный в этом учебнике, состоит из следующих основных блоков.

*  Добавление PerformanceCentre из коллекции 
*  Настройка и проверка единого входа в Azure AD

## <a name="adding-performancecentre-from-the-gallery"></a>Добавление PerformanceCentre из коллекции
Чтобы настроить интеграцию PerformanceCentre с Azure AD, вам потребуется добавить PerformanceCentre из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PerformanceCentre из коллекции, выполните следующие действия.**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **PerformanceCentre**.
   
    ![Приложения][5]
7. В области результатов выберите **PerformanceCentre** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в PerformanceCentre с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в PerformanceCentre соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PerformanceCentre.  

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PerformanceCentre.

**Чтобы настроить и проверить единый вход в Azure AD в PerformanceCentre, вам потребуется выполнить действия в следующих стандартных блоках.**

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя PerformanceCentre](#creating-a-halogen-software-test-user)** требуется для создания пользователя Britta Simon в PerformanceCentre, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы разрешить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении PerformanceCentre.

**Чтобы настроить единый вход Azure AD в PerformanceCentre, выполните следующие действия.**

1. На классическом портале Azure AD на странице интеграции с приложением **PerformanceCentre** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в PerformanceCentre?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![единого входа Azure AD][8] 
   
   1. В текстовое поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа на сайт PerformanceCentre (например, *http://companyname.performancecentre.com/saml/SSO*).
   2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в PerformanceCentre** выполните следующие действия.
   
    ![единого входа Azure AD][9] 
   
  * Нажмите **Загрузить метаданные**и сохраните файл на свой компьютер.
5. Войдите на корпоративный сайт **PerformanceCentre** с правами администратора.
6. На вкладке слева щелкните **Configure**(Настройка).
   
    ![единого входа Azure AD][10]
7. На вкладке слева щелкните **Miscellaneous** (Разное), а затем — **Single Sign On** (Единый вход).
   
    ![единого входа Azure AD][11]
8. В поле **Protocol** (Протокол) выберите **SAML**.
   
    ![единого входа Azure AD][12]
9. Откройте скачанный файл метаданных, скопируйте и вставьте его содержимое в текстовое поле **Identity Provider Metadata** (Метаданные поставщика удостоверений), а затем нажмите кнопку **Save** (Сохранить).
   
    ![единого входа Azure AD][13]
10. Проверьте правильность значений в полях **Entity Base URL** (Базовый URL-адрес сущности) и **Entity ID URL** (URL-адрес идентификатора сущности).
    
     ![единого входа Azure AD][14]
11. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**. 
    
     ![единого входа Azure AD][15]
12. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
     ![единого входа Azure AD][16]

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.  

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
   
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   1. В текстовом поле **Имя** введите **Britta**.  
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
   
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
    1. Запишите значение поля **Новый пароль**.
    2. Нажмите **Завершено**.   

### <a name="creating-a-performancecentre-test-user"></a>Создание тестового пользователя PerformanceCentre
Цель этого раздела — создать пользователя с именем Britta Simon в PerformanceCentre.

**Чтобы создать пользователя с именем Britta Simon в PerformanceCentre, выполните следующие действия.**

1. Войдите на корпоративный сайт PerformanceCentre с правами администратора.
2. В меню слева выберите **Interrelate** (Установить взаимосвязь), а затем нажмите кнопку **Create Participant** (Создать участника).
   
    ![Создать пользователя][400]
3. В диалоговом окне **Interrelate — Create Participant** (Взаимосвязь — создание участника) выполните следующие действия.
   
    ![Создать пользователя][401]
   
   1. Введите необходимые атрибуты для пользователя Britta Simon в соответствующие текстовые поля.
   > [!IMPORTANT]
   > Атрибут User Name этого пользователя в PerformanceCentre должен соответствовать имени пользователя в Azure AD.
   > 
   > 
 
   2. Выберите значение **Client Administrator** (Администратор клиента) в поле **Choose Role** (Выберите роль).
   3. Щелкните **Сохранить**.   


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — разрешить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к PerformanceCentre.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в PerformanceCentre, выполните следующие действия.**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201]
2. В списке приложений выберите **PerformanceCentre**.
   
    ![Назначение пользователя][202]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="testing-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув элемент PerformanceCentre на панели доступа, вы автоматически войдете в приложение PerformanceCentre.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png






<!--HONumber=Feb17_HO1-->


