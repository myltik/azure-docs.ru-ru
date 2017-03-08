---
title: "Учебник. Интеграция Azure Active Directory с Lifesize Cloud | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Lifesize Cloud."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Учебник. Интеграция Azure Active Directory с Lifesize Cloud
В этом учебнике описано, как интегрировать приложение Lifesize Cloud с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Lifesize Cloud обеспечивает следующие преимущества:

* С помощью Azure AD вы можете контролировать доступ к Lifesize Cloud.
* Вы можете включить автоматический вход пользователей в Lifesize Cloud (единый вход) с их учетными записями Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с приложением Lifesize Cloud, вам потребуется:

* подписка Azure AD;
* подписка Lifesize Cloud с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.

Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

* Добавление Lifesize Cloud из коллекции
* Настройка и проверка единого входа Azure AD.

## <a name="add-lifesize-cloud-from-the-gallery"></a>Добавление Lifesize Cloud из коллекции
Чтобы настроить интеграцию Lifesize Cloud с Azure AD, необходимо добавить Lifesize Cloud из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Lifesize Cloud из коллекции, выполните следующие действия:**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**.
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Lifesize Cloud**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. В области результатов выберите **Lifesize Cloud** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Lifesize Cloud с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Lifesize Cloud соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Lifesize Cloud.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Lifesize Cloud.

Чтобы настроить и проверить единый вход Azure AD в Lifesize Cloud, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Lifesize Cloud](#creating-a-lifesize-cloud-test-user)** требуется для создания в Lifesize Cloud пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD
В этом разделе описано, как включить единый вход Azure AD на классическом портале и настроить его в приложении Lifesize Cloud.

**Чтобы настроить единый вход Azure AD в Lifesize Cloud, выполните следующие действия:**

1. На странице интеграции с приложением **Lifesize Cloud** классического портала щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][6] 
2. На странице **Как пользователи должны входить в Lifesize Cloud?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
   ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. В текстовом поле **URL-адрес для входа** введите URL-адрес, используемый для входа в приложение Lifesize Cloud, в следующем формате: **https://login.lifesizecloud.com/ls/?acs**.
  2. Нажмите кнопку **Далее**.
4. На странице **Настройка единого входа в Lifesize Cloud** выполните следующие действия:
   
   ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. Нажмите **Загрузить сертификат**и сохраните файл сертификата на свой компьютер.
   2. Нажмите кнопку **Далее**.
5. Для настройки единого входа в вашем приложении необходимо войти в приложение Lifesize Cloud с правами администратора.
6. В правом верхнем углу страницы щелкните свое имя и выберите **Advance Settings** (Дополнительные параметры).
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. В меню Advance Settings (Дополнительные параметры) щелкните ссылку **SSO Configuration** (Конфигурация единого входа). Откроется страница "SSO Configuration" (Конфигурация единого входа) для вашего экземпляра.
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. Теперь настройте следующие значения в пользовательском интерфейсе единого входа.    
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. Скопируйте значение URL-адреса издателя из Azure AD и вставьте его в текстовое поле **Identity Provider Issuer** (Издатель поставщика удостоверений). 
  2. Скопируйте значение URL-адреса удаленного входа из Azure AD и вставьте его в текстовое поле **Login URL** (URL-адрес для входа).   
  3. Откройте скачанный сертификат в Блокноте, скопируйте содержимое сертификата (кроме первой и последней строк), а затем вставьте его в текстовое поле **X.509 Certificate** (Сертификат X.509).
  4. В разделе "SAML Attribute Mappings" (Сопоставления атрибутов SAML) в текстовом поле **First Name** (Имя) введите значение **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  5. В разделе "SAML Attribute Mappings" (Сопоставления атрибутов SAML) в текстовом поле **Last Name** (Фамилия) введите значение **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
  6. В разделе "SAML Attribute Mappings" (Сопоставления атрибутов SAML) в текстовом поле **Email** (Электронный адрес) введите значение **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
9. Чтобы проверить конфигурацию, можно нажать кнопку **Test** (Проверить).
   
   >[!NOTE]
   >Чтобы проверка прошла успешно, необходимо завершить работу мастера настройки в Azure AD, а также предоставить доступ пользователям или группам, которые будут выполнять эту проверку.
   >  
10. Чтобы включить единый вход, установите флажок **Enable SSO** (Включить единый вход).
11. Затем нажмите кнопку **Update** (Обновить), чтобы сохранить все параметры. При этом будет создано значение RelayState. Скопируйте значение RelayState, которое отобразится в текстовом поле. Оно понадобится вам на последующих шагах.
12. На классическом портале подтвердите конфигурацию единого входа и нажмите кнопку **Далее**.
    
   ![единого входа Azure AD][10]
13. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
    ![единого входа Azure AD][11]
    
**Вход на портал управления Azure**

1. Войдите на портал **https://portal.azure.com** с учетными данными администратора.
2. В области навигации слева щелкните ссылку **Больше служб**.
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. Выполните поиск Azure Active Directory и щелкните ссылку **Azure Active Directory**.
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. Вы можете просмотреть все свои приложения SaaS, нажав кнопку **Корпоративные приложения**.
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. Теперь щелкните ссылку **Все приложения** в следующей колонке.
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. Выполните поиск приложения Lifesize, для которого требуется настроить RelayState. 
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. Затем щелкните ссылку **Единый вход** в колонке.
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. Отобразится параметр **Показать дополнительные параметры URL-адресов**. Установите флажок.
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. Теперь настройте RelayState для приложения, которое отображается на странице настройки единого входа приложения Lifesize. 
    
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. Сохраните параметры.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.

    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
   2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
   3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.

   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. В текстовом поле **Имя** введите **Britta**.   
   2. В текстовом поле **Фамилия** введите **Simon**.
   3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
   4. В списке **Роль** выберите **Пользователь**.
   5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. Запишите значение поля **Новый пароль**.
    2. Нажмите **Завершено**.   

### <a name="create-an-lifesize-cloud-test-user"></a>Создание тестового пользователя Lifesize Cloud
В этом разделе описано, как создать пользователя Britta Simon в приложении Lifesize Cloud. Lifesize Cloud поддерживает автоматическую подготовку пользователей. После успешной аутентификации в Azure AD для пользователя будет автоматически выполняться подготовка в приложении. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Lifesize Cloud.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в FileCloud, выполните следующие действия:**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. В списке приложений выберите **Lifesize Cloud**.
   
    ![Настройка единого входа](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203]
4. В списке пользователей выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Lifesize Cloud на панели доступа, вы автоматически войдете в приложение Lifesize Cloud.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

