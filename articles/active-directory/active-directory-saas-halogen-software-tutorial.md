---
title: "Учебник. Интеграция Azure Active Directory с Halogen Software"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Halogen Software."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 06d4b7495f4201387500944758ba2a0916b619d2
ms.openlocfilehash: cbe20975792ab0f5fe8fac64110ede3a964d443a
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Учебник. Интеграция Azure Active Directory с Halogen Software
Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Halogen Software.

Интеграция Halogen Software с Azure AD обеспечивает следующие преимущества: 

* С помощью Azure AD вы можете контролировать доступ к Halogen Software. 
* Вы можете включить автоматический вход пользователей в Halogen Software (единый вход) под учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Halogen Software, вам потребуется следующее:

* подписка Azure AD;
* подписка Halogen Software с включенным единым входом.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.
> 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Описание сценария
Цель этого руководства — научить вас проверять единый вход Azure AD в тестовой среде. 

Сценарий, описанный в этом учебнике, состоит из двух основных блоков:

1. Добавление Halogen Software из коллекции. 
2. Настройка и проверка единого входа Azure AD.

## <a name="adding-halogen-software-from-the-gallery"></a>Добавление Halogen Software из коллекции.
Чтобы настроить интеграцию Halogen Software с Azure AD, вам потребуется добавить Halogen Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Halogen Software из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .    
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **halogen software**.
   
    ![Приложения][5]
7. В области результатов выберите **Halogen Software** и нажмите кнопку **Завершить**, чтобы добавить приложение.

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
Цель этого раздела — показать вам, как настроить и проверить единый вход Azure AD в Halogen Software с использованием тестового пользователя с именем Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Halogen Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Halogen Software.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Halogen Software.

Чтобы настроить и проверить единый вход Azure AD в Halogen Software, вам потребуется выполнить действия в следующих блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Halogen Software](#creating-a-halogen-software-test-user)** требуется для создания дублирующего Britta Simon пользователя в Halogen Software, связанного с ее представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложении Halogen Software.

**Чтобы настроить единый вход Azure AD с помощью Halogen Software, выполните следующие действия:**

1. На классическом портале Azure на странице интеграции с приложением **Halogen Software** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][8]
2. На странице **Как пользователи должны входить в Halogen Software?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][9]
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.

    ![Настройка параметров приложения][10]
   1. В текстовом поле **URL-адрес входа** введите URL-адрес, используемый пользователями для входа в приложение Halogen Software по следующей схеме: *https://global.hgncloud.com/fabrikam/welcome.jsp*.
   2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Halogen Software** щелкните **Скачать метаданные**, а затем сохраните файл метаданных на локальном компьютере.
   
    ![Что такое Azure AD Connect?][11]
5. В отдельном окне браузера войдите в приложение **Halogen Software** под учетной записью администратора.
6. Откройте вкладку **Параметры** . 
   
    ![Что такое Azure AD Connect?][12]
7. На панели навигации слева щелкните **Настройка SAML**. 
   
    ![Что такое Azure AD Connect?][13]
8. На странице **Настройка SAML** сделайте следующее: 

    ![Что такое Azure AD Connect?][14]
  1. В качестве значения поля **Уникальный идентификатор** выберите **NameID**.
  2. В качестве значения поля **Unique Identifier Maps To** (Уникальный идентификатор сопоставляется с) выберите **Имя пользователя**.
  3. Для отправки скачанного файла метаданных нажмите кнопку **Обзор**, чтобы выбрать файл, а затем щелкните **Отправить файл**.
  4. Чтобы проверить конфигурацию, нажмите кнопку **Запустить тест**. 
    >[!NOTE]
    >Подождите, пока не появится сообщение "*Проверка SAML завершена. Закройте это окно*". Закройте окно браузера. Флажок **Включить SAML** установлен, только если проверка завершена. 
    >
  5. Выберите **Включить SAML**.
  6. Нажмите кнопку **Сохранить изменения**. 
9. На классическом портале Azure выберите подтверждение конфигурации единого входа, а затем нажмите кнопку **Завершить**, чтобы закрыть диалоговое окно **Настройка единого входа**. 
   
    ![Что такое Azure AD Connect?][15]
10. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![Что такое Azure AD Connect?][16]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на классическом портале Azure тестового пользователя с именем Britta Simon.

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Что такое Azure AD Connect?][100] 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Что такое Azure AD Connect?][101] 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**. 
   
    ![Что такое Azure AD Connect?][102] 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Что такое Azure AD Connect?][103] 
  1. В поле **Тип пользователя** выберите значение **Новый пользователь в вашей организации**.
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку Далее.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия. 
   
   ![Что такое Azure AD Connect?][104] 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Что такое Azure AD Connect?][105]  
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Что такое Azure AD Connect?][106]   
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-halogen-software-test-user"></a>Создание тестового пользователя Halogen Software
Цель этого раздела — создать пользователя с именем Britta Simon в Halogen Software.

**Чтобы создать пользователя с именем Britta Simon в Halogen Software, выполните следующие действия:**

1. Войдите в приложение **Halogen Software** под учетной записью администратора.
2. Щелкните вкладку **Центр пользователей**, затем щелкните **Создать пользователя**.
   
    ![Что такое Azure AD Connect?][300]  
3. На странице диалогового окна **Новый пользователь** выполните следующие действия.
   
    ![Что такое Azure AD Connect?][301]
  1. В текстовом поле **Имя** введите **Britta**. 
  2. В текстовом поле **Фамилия** введите **Simon**. 
  3. В текстовое поле **Имя пользователя** введите **имя пользователя Britta Simon, используемое на классическом портале Azure**.
  4. В текстовом поле **Пароль** введите пароль для пользователя Britta.
  5. Щелкните **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Halogen Software, чтобы он мог использовать единый вход Azure.

![Что такое Azure AD Connect?][200]

**Чтобы назначить Britta Simon в Halogen Software, выполните следующие действия:**

1. Чтобы открыть представление приложений, на классическом портале Azure в представлении каталога щелкните **Приложения** в меню вверху.
   
    ![Что такое Azure AD Connect?][201]
2. В списке приложений выберите **Halogen Software**.
   
    ![Что такое Azure AD Connect?][202]
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Что такое Azure AD Connect?][203]
4. В списке пользователей выберите **Britta Simon**.
   
    ![Что такое Azure AD Connect?][204]
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Что такое Azure AD Connect?][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Нажав элемент Halogen Software на панели доступа, вы автоматически войдете в приложение Halogen Software.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

