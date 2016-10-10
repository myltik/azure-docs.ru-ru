<properties
	pageTitle="Руководство. Интеграция Azure Active Directory с Facebook at Work | Microsoft Azure"
	description="Узнайте, как настроить единый вход между Azure Active Directory и Facebook at Work."
	services="active-directory"
	documentationCenter=""
	authors="asmalser-msft"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="asmalser"/>


# Руководство. Интеграция Azure Active Directory с Facebook at Work

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с Facebook at Work.

Интеграция Facebook at Work с Azure AD дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Facebook at Work.
- Вы можете автоматически подготовить учетную запись для пользователей, которым предоставлен доступ к Facebook at Work.
- Вы можете включить автоматический вход пользователей в Facebook at Work (единый вход) с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно.

Подробные сведения об интеграции приложений SaaS с Azure AD см. в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](active-directory-appssoaccess-whatis.md).


## Предварительные требования 

Чтобы настроить интеграцию Azure AD с CS Stars, вам потребуется:

- подписка Azure AD;
- Facebook at Work с включенным единым входом.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## Добавление Facebook at Work из коллекции
Чтобы настроить интеграцию Facebook at Work с Azure AD, необходимо добавить Facebook at Work из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Facebook at Work из коллекции, выполните следующие действия:**

1. На **классическом портале Azure** в области навигации слева щелкните **Active Directory**.

	![Active Directory][1]

2. Из списка **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.

3. Чтобы открыть представление приложений, в представлении каталога нажмите **Приложения** в верхнем меню.

	![Приложения][2]

4. В нижней части страницы нажмите кнопку **Добавить**.
	
	![Приложения][3]

5. В диалоговом окне **Что необходимо сделать?** нажмите **Добавить приложение из коллекции**.

	![Приложения][4]

6. В поле поиска введите **Facebook at Work**.

7. В области результатов выберите **Facebook at Work** и нажмите кнопку **Завершить**, чтобы добавить приложение.


### Настройка единого входа в Azure AD

В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Facebook at Work с помощью своей учетной записи Azure Active Directory, используя федерацию на основе протокола SAML.

**Чтобы настроить единый вход Azure AD в Facebook at Work, выполните следующие действия:**

1.	Добавив Facebook at Work на классическом портале Azure, нажмите кнопку **Настроить единый вход**.

2.	На экране **Настройка URL-адреса приложения** введите URL-адрес, по которому пользователи будут входить в приложение Facebook at Work. Это клиентский URL-адрес Facebook at Work (например, https://example.facebook.com/). По завершении нажмите кнопку **Далее**.

3.	В другом окне браузера войдите на корпоративный сайт Facebook at Work в качестве администратора.

4. Чтобы настроить Facebook at Work для использования Azure AD в качестве поставщика удостоверений, следуйте инструкциям по этому URL-адресу: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers).

5.	После настройки вернитесь в окно браузера с классическим порталом Azure, установите флажок, чтобы подтвердить выполнение процедуры, затем нажмите кнопки **Далее** и **Завершить**.


## Автоматическая подготовка пользователей для Facebook at Work

Azure AD поддерживает автоматическую синхронизацию сведений об учетных записях назначенных пользователей с Facebook at Work. Эта автоматическая синхронизация позволяет Facebook at Work получить данные, необходимые для авторизации доступа пользователей, до того как пользователи впервые выполнят вход. Она также отменяет подготовку пользователей для Facebook at Work, если доступ для них отозван в Azure AD.

Чтобы настроить автоматическую подготовку, щелкните **Настройка подготовки учетной записи** в окне классического портала Azure.

Дополнительные сведения о настройке автоматической подготовки см. по адресу [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).


## Назначение пользователям доступа в Facebook at Work

Чтобы подготовленные пользователи AAD могли видеть Facebook at Work на своей панели доступа, им нужно назначить доступ на классическом портале Azure.

**Назначение пользователям доступа в Facebook at Work.**

1.	На классическом портале Azure на начальной странице для Facebook at Work щелкните **Назначить учетные записи**.

2.	В меню **Показать** выберите, кому нужно назначить доступ в Facebook at Work — пользователям или группе, — и нажмите кнопку с галочкой.

3.	В списке результатов выберите пользователей или группы, которым вы хотите назначить доступ в Facebook at Work.

4.	В нижнем колонтитуле страницы нажмите кнопку **Назначить**.


## дополнительные ресурсы.

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png

<!---HONumber=AcomDC_0928_2016-->