---
title: "Учебник. Интеграция Azure Active Directory с Cisco Spark | Документация Майкрософт"
description: "Узнайте, как настроить единый вход между Azure Active Directory и Cisco Spark."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 931004d458fd0cbf3e002805ca701d777ddc57c2
ms.openlocfilehash: f0f12b1667b7f45fd164fac658502c93e8afb855
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Учебник. Интеграция Azure Active Directory с Cisco Spark
В этом учебнике описано, как интегрировать Cisco Spark с Azure Active Directory (Azure AD).

Интеграция Azure AD с Cisco Spark обеспечивает следующие преимущества.

* С помощью Azure AD вы можете контролировать доступ к Cisco Spark.
* Вы можете включить автоматический вход пользователей в Cisco Spark (единый вход) с помощью учетных записей Azure AD.
* Вы можете управлять учетными записями централизованно — через классический портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с Cisco Spark, вам потребуется:

* подписка Azure AD;
* подписка на **Cisco Spark** с поддержкой единого входа.

>[!NOTE]
>Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике. 
> 

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить [пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом руководстве, состоит из двух стандартных блоков.

1. Добавление Cisco Spark из коллекции
2. Настройка и проверка единого входа Azure AD.

## <a name="add-cisco-spark-from-the-gallery"></a>Добавление Cisco Spark из коллекции
Чтобы настроить интеграцию Cisco Spark с Azure AD, необходимо добавить Cisco Spark из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cisco Spark из коллекции, выполните следующие действия.**

1. На **классическом портале Azure**в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Cisco Spark**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)
7. В области результатов выберите **Cisco Spark** и нажмите кнопку **Завершить**, чтобы добавить приложение.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Настройка и проверка единого входа Azure AD
В этом разделе описана настройка и проверка единого входа Azure AD в Cisco Spark с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Cisco Spark соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cisco Spark.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Cisco Spark. Чтобы настроить и проверить единый вход Azure AD в Cisco Spark, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configuring-azure-ad-single-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Cisco Spark](#creating-a-cisco-spark-test-user)** нужно для того, чтобы в Cisco Spark также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход в Azure AD.
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-sso"></a>Настройка единого входа Azure AD
Цель этого раздела — включить единый вход Azure AD на классическом портале Azure и настроить единый вход в приложение Cisco Spark.

Приложение Cisco Spark ожидает, что утверждения SAML должны содержать определенные атрибуты. Настройте приведенные ниже атрибуты для этого приложения. Управлять значениями этих атрибутов можно на вкладке **Atrributes** (Атрибуты) приложения. На следующем снимке экрана приведен пример.

![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Чтобы настроить единый вход Azure AD в Cisco Spark, сделайте следующее:**

1. На классическом портале Azure на странице интеграции с приложением **Cisco Spark** в меню в верхней части страницы щелкните **Атрибуты**.
   
    ![Настройка единого входа][5]
2. В диалоговом окне **Атрибуты токена SAML** сделайте следующее.
  1. Щелкните **Добавить атрибут пользователя**, чтобы открыть диалоговое окно **Добавить атрибут пользователя**.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
  2. В текстовом поле **Имя атрибута** введите значение **uid**.
  3. Из списка **Значение атрибута** выберите **user.userprincipal**.
  4. Нажмите **Завершено**. Нажмите кнопку **Применить изменения** в нижней части страницы.
3. В меню вверху щелкните **Быстрый запуск**.
   
    ![Настройка единого входа][6]
4. На классическом портале на странице интеграции с приложением **Cisco Spark** щелкните **Настройка единого входа**, чтобы открыть диалоговое окно **Настройка единого входа**.
   
    ![Настройка единого входа][7] 
5. На странице **Как пользователи должны входить в Cisco Spark?** выберите **Единый вход Azure AD** и нажмите кнопку **Далее**.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)
6. В диалоговом окне на странице **Настройка параметров приложения** выполните следующие действия.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)
  1. В текстовом поле URL-адрес для входа введите URL-адрес в следующем формате: `https://web.ciscospark.com/#/signin`.
  2. Нажмите кнопку **Далее**.
7. На странице **Настройка единого входа в Cisco Spark** нажмите кнопку **Скачать метаданные**, а затем сохраните файл метаданных на свой компьютер.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)
8. Войдите в [службу Cisco для управления совместной работой в облаке](https://admin.ciscospark.com/), указав учетные данные администратора с полными правами.
9. Выберите **Settings** (Параметры) и в разделе **Authentication** (Аутентификация) щелкните **Modify** (Изменить).
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
9. Выберите **Integrate a 3rd-party identity provider. (Advanced)** (Интеграция стороннего поставщика удостоверений. (Дополнительно)) и перейдите к следующему экрану.
10. Щелкните **Download Metadata File** (Скачать файл метаданных) и сохраните файл на свой компьютер.
11. На странице **Import Idp Metadata** (Импорт метаданных IdP) либо перетащите файл метаданных Azure AD на страницу, либо воспользуйтесь обозревателем файлов, чтобы найти и передать файл метаданных Azure AD. Затем выберите **Require certificate signed by a certificate authority in Metadata (more secure)** (Требовать наличия в метаданных сертификата, подписанного центром сертификации) и нажмите кнопку **Next** (Далее). 
   
   ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)
12. Выберите **Test SSO Connection** (Проверить подключение для единого входа) и, когда откроется новая вкладка браузера, пройдите аутентификацию Azure AD, выполнив вход.
13. Вернитесь к на вкладку браузера **Служба Cisco для управления совместной работой в облаке**. Если проверка прошла успешно, выберите **This test was successful. Enable Single Sign-On option** (Этот тест прошел успешно. Включить единый вход) и нажмите кнопку **Next** (Далее).
14. На классическом портале Azure выберите подтверждение конфигурации единого входа и нажмите кнопку **Далее**.
   
    ![единого входа Azure AD][10]
15. На странице **Подтверждение единого входа** нажмите кнопку **Завершить**.  
    
     ![единого входа Azure AD][11]

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
В этом разделе описано, как создать на классическом портале тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][20]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы отобразить список пользователей, в меню вверху выберите **Пользователи**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 
4. Чтобы открыть диалоговое окно **Добавление пользователя**, на панели инструментов внизу нажмите кнопку **Добавить пользователя**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 
5. На странице диалогового окна **Тип учетной записи пользователя** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 
  1. В поле «Тип пользователя» выберите значение «Новый пользователь в вашей организации».
  2. В текстовом поле **Имя пользователя** введите **BrittaSimon**.
  3. Нажмите кнопку **Далее**.
6. На странице диалогового окна **Профиль пользователя** выполните следующие действия.
   
   ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 
  1. В текстовом поле **Имя** введите **Britta**.  
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Отображаемое имя** введите **Britta Simon**.
  4. В списке **Роль** выберите **Пользователь**.
  5. Нажмите кнопку **Далее**.
7. На странице диалогового окна **Получить временный пароль** нажмите кнопку **Создать**.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 
8. На странице диалогового окна **Получить временный пароль** выполните следующие действия.
   
    ![Создание тестового пользователя Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 
  1. Запишите значение поля **Новый пароль**.
  2. Нажмите **Завершено**.   

### <a name="create-a-cisco-spark-test-user"></a>Создание тестового пользователя Cisco Spark
В этом разделе описано, как создать пользователя Britta Simon в Cisco Spark. В этом разделе описано, как создать пользователя Britta Simon в Cisco Spark.

1. Войдите в [службу Cisco для управления совместной работой в облаке](https://admin.ciscospark.com/), указав учетные данные администратора с полными правами.
2. Щелкните **Users** (Пользователи) и **Manage Users** (Управление пользователями).
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 
3. В окне **Manage User** (Управление пользователем) выберите **Manually add or modify users** (Добавить или изменить пользователей вручную) и нажмите кнопку **Next** (Далее).
4. Выберите **Names and Email address** (Имя, фамилия и электронный адрес). Затем заполните текстовые поля следующим образом.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
  1. В текстовом поле **Имя** введите **Britta**. 
  2. В текстовом поле **Фамилия** введите **Simon**.
  3. В текстовом поле **Email address** (Электронный адрес) введите **britta.simon@contoso.com**.
5. Щелкните знак "плюс", чтобы добавить пользователя Britta Simon. Затем щелкните **Далее**.
6. В окне **Add Services for Users** (Добавление служб для пользователей) нажмите кнопку **Save** (Сохранить), затем — кнопку **Finish** (Готово).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD
Цель этого раздела — предоставить пользователю Britta Simon доступ к Cisco Spark, чтобы он мог использовать единый вход Azure.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Cisco Spark, выполните следующие действия.**

1. Чтобы открыть представление приложений, в представлении каталога на классическом портале щелкните **Приложения** в верхнем меню.
   
    ![Назначение пользователя][201] 
2. Из списка приложений выберите **Cisco Spark**.
   
    ![Настройка единого входа](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 
3. В меню в верхней части страницы щелкните **Пользователи**.
   
    ![Назначение пользователя][203] 
4. В списке "Все пользователи" выберите **Britta Simon**.
5. На панели инструментов внизу щелкните **Назначить**.
   
    ![Назначение пользователя][205]

### <a name="test-single-sign-on"></a>Проверка единого входа
Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Cisco Spark" на панели доступа, вы автоматически войдете в приложение Cisco Spark.

## <a name="additional-resources"></a>Дополнительные ресурсы
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png

