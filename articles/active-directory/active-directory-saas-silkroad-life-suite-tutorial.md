---
title: "Руководство по интеграции Azure Active Directory с решением SilkRoad Life Suite | Документация Майкрософт"
description: "Узнайте, как настроить единый вход для Azure Active Directory и SilkRoad Life Suite."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: ecf4e31ecea00d003fc47ea4cebb781ca58957f7
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Руководство по интеграции Azure Active Directory с решением SilkRoad Life Suite
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением SilkRoad Life Suite. 

Интеграция SilkRoad Life Suite с Azure AD обеспечивает следующие преимущества: 

* С помощью Azure AD вы можете контролировать доступ к SilkRoad Life Suite. 
* Вы можете включить автоматический вход пользователей в SilkRoad Life Suite (единый вход) с учетной записью Azure AD.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с SilkRoad Life Suite, вам потребуется следующее:

* подписка Azure AD;
* подписка SilkRoad Life Suite с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде.

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление SilkRoad Life Suite из коллекции. 
2. Настройка и проверка единого входа Azure AD.

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Добавление SilkRoad Life Suite из коллекции
Чтобы настроить интеграцию SilkRoad Life Suite в Azure AD, необходимо добавить SilkRoad Life Suite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SilkRoad Life Suite из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]

6. В поле поиска введите **SilkRoad Life Suite**.
   
    ![Приложения][5]

7. В области результатов выберите **SilkRoad Life Suite** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Приложения][50]

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
Цель этого раздела — показать, как настроить и проверить единый вход Azure AD в SilkRoad Life Suite с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SilkRoad Life Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SilkRoad Life Suite.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в SilkRoad Life Suite.

Чтобы настроить и проверить единый вход Azure AD в SilkRoad Life Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SilkRoad Life Suite](#creating-a-silkroad-life-suite-test-user)** требуется для создания пользователя Britta Simon в SilkRoad Life Suite, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить его в приложении SilkRoad Life Suite.

**Чтобы настроить единый вход в Azure AD в SilkRoad Life Suite, выполните следующие действия:**

1. Войдите на сайт компании SilkRoad от имени администратора. 

  >[!NOTE] 
  > Чтобы получить доступ к приложению SilkRoad Life Suite Authentication для настройки федерации с Microsoft Azure AD, обратитесь в службу поддержки компании SilkRoad или к представителю отдела обслуживания клиентов компании SilkRoad.
  > 

2. В разделе **Service Provider** (Поставщик услуг) щелкните **Federation Details** (Сведения о федерации). 
   
    ![единого входа Azure AD][10] 

3. Щелкните **Download Federation Metadata**(Загрузить метаданные федерации), а затем сохраните файл метаданных на вашем компьютере.
   
    ![единого входа Azure AD][11] 

4. На классическом портале Azure на странице интеграции с приложением **SilkRoad Life Suite** нажмите кнопку **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 

5. На странице **Как пользователи должны входить в SilkRoad Life Suite?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][7] 

6. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![единого входа Azure AD][8]   
 1. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый пользователями для входа на сайт SilkRoad Life Suite (например, *https://defcompanytest-test-redcarpet.silkroad-eng.com/Authentication/*).  
 2. Откройте скачанный файл метаданных **Silkroad** . 
 3. Найдите тег **AssertionConsumerService**, а затем скопируйте атрибут **Location**.         
   
    ![единого входа Azure AD][21] 
 4. Вставьте значение в текстовое поле **URL-адрес ответа**.  
 5. Нажмите кнопку **Далее**.

6. На странице **Настройка единого входа в SilkRoad Life Suite** выполните следующие действия:
   
    ![единого входа Azure AD][9]  
 1. Нажмите "Загрузить сертификат" и сохраните файл сертификата на свой компьютер.  
 2. Нажмите кнопку **Далее**.

7. В приложении **SilkRoad** щелкните **Authentication Sources** (Источники проверки подлинности).
   
    ![единого входа Azure AD][12] 

8. Щелкните **Add Authentication Source**(Добавить источник аутентификации). 
   
    ![Единый вход в Azure AD][13] 

9. В разделе **Add Authentication Source** (Добавление источника аутентификации) выполните следующие действия: 
   
    ![единого входа Azure AD][14]  
 1. В разделе **Option 2 - Metadata File** (Вариант 2 — файл метаданных) нажмите кнопку **Browse** (Обзор), чтобы отправить скачанный файл метаданных.  
 2. Нажмите кнопку **Создать поставщик удостоверений с помощью данных из файла**.

10. В разделе **Authentication Sources** (Источники проверки подлинности) нажмите кнопку **Edit** (Изменить). 
    
     ![единого входа Azure AD][15] 

11. В диалоговом окне **Edit Authentication Source** (Изменение источника аутентификации) выполните следующие действия: 
    
     ![единого входа Azure AD][16] 
 1. В поле **Enabled** (Включено) выберите **Yes** (Да).   
 2. В текстовом поле **Описание IdP** введите описание вашей конфигурации (например, *Azure AD SSO*).  
 3. В текстовом поле **IdP Name** (Имя IdP) введите уникальное имя вашей конфигурации (например, *Azure SP*).  
 4. Щелкните **Сохранить**.

12. Отключите все остальные источники аутентификации. 
    
     ![единого входа Azure AD][17]

13. На классическом портале Azure на странице **Подтверждение единого входа** щелкните **Далее**.  
    
     ![единого входа Azure AD][18]

14. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.
    
     ![единого входа Azure AD][19]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_09.png)  

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png) 

4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png) 

5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_05.png)  
 1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».  
 2. В текстовом поле **Имя пользователя** введите **BrittaSimon**. 
 3. Нажмите кнопку **Далее**.

6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_06.png)  
 1. В текстовом поле **Имя** введите **Britta**.    
 2. В текстовом поле **Фамилия** введите **Simon**. 
 3. В текстовом поле **Отображаемое имя** введите **Britta Simon**. 
 4. В списке **Роль** выберите **Пользователь**.
 5. Нажмите кнопку **Далее**.

7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_07.png) 

8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_08.png)  
 1. Запишите значение поля **Новый пароль**. 
 2. Нажмите **Завершено**.   

### <a name="create-a-silkroad-life-suite-test-user"></a>Создание тестового пользователя SilkRoad Life Suite
Цель этого раздела — создать в SilkRoad Life Suite пользователя с именем Britta Simon. Пользователь Britta должен иметь идентификатор единого входа (иногда называемый *AuthParam*), который соответствует **адресу электронной почты** этого пользователя в Azure AD.

**Чтобы создать пользователя с именем Britta Simon в SilkRoad Life Suite, выполните следующие действия:**

- Попросите службу поддержки SilkRoad Life Suite создать пользователя, у которого значение атрибута **Код SSO** совпадает с **адресом электронной почты** пользователя Britta Simon в Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — позволить пользователю Britta Simon использовать единый вход Azure, предоставив ей доступ к SilkRoad Life Suite.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon в SilkRoad Life Suite, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Назначение пользователя][201] 

2. В списке приложений выберите **SilkRoad Life Suite**.
   
    ![Назначение пользователя][202] 

3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 

4. В списке пользователей выберите **Britta Simon**.

5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.  

Щелкнув плитку SilkRoad Life Suite на панели доступа, вы автоматически войдете в приложение SilkRoad Life Suite.

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_01.png
[50]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_02.png

[6]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_03.png
[8]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_04.png
[9]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_05.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
[18]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_06.png
[19]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_15.png


[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_14.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_205.png






