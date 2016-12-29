---
title: "Руководство по интеграции Azure Active Directory с Workplace by Facebook | Документация Майкрософт"
description: "Сведения о настройке единого входа между Azure Active Directory и Workplace by Facebook."
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: efd29518fc207eb3e554f1389a6109ae4f1f3205
ms.openlocfilehash: 252e7b3c198a3433ecd21aef2222b2ca14b7ac04


---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Руководство по интеграции Azure Active Directory с Workplace by Facebook
Цель этого руководства — показать, как интегрировать Workplace by Facebook с Azure Active Directory.

Интеграция Workplace by Facebook с Azure AD дает следующие преимущества. 

* С помощью Azure AD вы можете контролировать доступ к Workplace by Facebook. 
* Вы можете автоматически подготовить учетную запись для пользователей, которым предоставлен доступ к Workplace by Facebook.
* Вы можете включить автоматический вход пользователей в Workplace by Facebook (единый вход) с учетной записью Azure AD.
* Вы можете управлять учетными записями централизованно. 

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Предварительные требования
Чтобы настроить интеграцию Azure AD с CS Stars, вам потребуется:

* подписка Azure AD;
* подписка на Workplace by Facebook с поддержкой единого входа.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

* Не следует использовать рабочую среду при отсутствии необходимости.
* Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Добавление Workplace by Facebook из коллекции
Чтобы настроить интеграцию Workplace by Facebook с Azure AD, необходимо добавить Workplace by Facebook из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workplace by Facebook из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**. 
   
    ![Active Directory][1]
2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.
   
    ![Приложения][2]
4. В нижней части страницы нажмите кнопку **Добавить** .
   
    ![Приложения][3]
5. В диалоговом окне **Что необходимо сделать?** щелкните **Добавить приложение из коллекции**.
   
    ![Приложения][4]
6. В поле поиска введите **Workplace by Facebook**.
7. В области результатов выберите **Workplace by Facebook** и щелкните **Завершить**, чтобы добавить приложение.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Workplace by Facebook с помощью своей учетной записи Azure Active Directory, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход Azure AD в Workplace by Facebook, выполните следующие действия:**

1. Добавив Workplace by Facebook на классическом портале Azure, щелкните **Настройка единого входа**.
2. На экране **Configure App URL** (Настройка URL-адреса приложения) введите URL-адрес, по которому пользователи будут входить в приложение Workplace by Facebook. Это URL-адрес клиента Workplace by Facebook (например, https://example.facebook.com/). По завершении нажмите кнопку **Далее**.
3. В другом окне браузера войдите на сайт компании Workplace by Facebook в качестве администратора.
4. Чтобы настроить Workplace by Facebook для использования Azure AD в качестве поставщика удостоверений, следуйте инструкциям по этому URL-адресу: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. После настройки вернитесь в окно браузера с классическим порталом Azure, установите флажок, чтобы подтвердить выполнение процедуры, затем нажмите кнопки **Далее** и **Завершить**.


## <a name="automatically-provisioning-users-to-workplace-by-facebook"></a>Автоматическая подготовка пользователей для Workplace by Facebook
Azure AD поддерживает автоматическую синхронизацию сведений об учетных записях назначенных пользователей с Workplace by Facebook. Эта автоматическая синхронизация позволяет Workplace by Facebook получить данные, необходимые для авторизации доступа пользователей, до того как пользователи впервые выполнят вход. Она также отменяет подготовку пользователей для Workplace by Facebook, если доступ для них отозван в Azure AD.

Чтобы настроить автоматическую подготовку, щелкните **Настройка подготовки учетной записи** в окне классического портала Azure.

Дополнительные сведения о настройке автоматической подготовки см. по адресу [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

## <a name="assigning-users-to-workplace-by-facebook"></a>Назначение пользователей для Workplace by Facebook
Чтобы подготовленные пользователи AAD могли видеть Workplace by Facebook на своей панели доступа, им нужно назначить доступ на классическом портале Azure.

**Чтобы назначить пользователей для Workplace by Facebook, выполните следующие действия:**

1. На классическом портале Azure на начальной странице для Workplace by Facebook щелкните **Назначить учетные записи**.
2. В меню **Показать** выберите, кому нужно назначить доступ в Workplace by Facebook — пользователям или группе, — и нажмите кнопку с галочкой.
3. В списке результатов выберите пользователей или группы, которым вы хотите назначить доступ в Workplace by Facebook.
4. В нижнем колонтитуле страницы нажмите кнопку **Назначить** .

## <a name="additional-resources"></a>дополнительные ресурсы.
* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png







<!--HONumber=Nov16_HO5-->


