<properties
	pageTitle="Включение собственных клиентских приложений для взаимодействия с приложениями прокси | Microsoft Azure"
	description="В этой статье описано, как включить собственные клиентские приложения для взаимодействия с соединителем прокси приложений Azure AD, чтобы обеспечить безопасный удаленный доступ к локальным приложениям."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="kgremban"/>

# Включение собственных клиентских приложений для взаимодействия с приложениями прокси
Прокси приложений Azure Active Directory часто используется для публикации приложений браузера, например SharePoint, Outlook Web Access и пользовательских бизнес-приложений. Его также можно использовать для публикации серверных приложений HTTP, которые используются с помощью собственных клиентов. Это реализуется с помощью выданных маркеров Azure AD, отправляемых в стандартные заголовки HTTP авторизации.


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


Для публикации таких приложений рекомендуется использовать библиотеку проверки подлинности Azure AD, которая автоматически выполняет проверку подлинности и поддерживает множество различных клиентских сред. Прокси приложения вписывается в [сценарий вызова веб-API собственным приложением](active-directory-authentication-scenarios.md#native-application-to-web-api). Чтобы этого добиться, выполните следующие действия.

1. Опубликуйте приложение прокси, как любое другое приложение, назначьте пользователей и предоставьте им лицензии категории «Премиум» или «Базовый». Дополнительные сведения см. в статье [Публикация приложений с помощью прокси приложения Azure AD](active-directory-application-proxy-publish.md).
2. Настройте собственное приложение, следуя инструкциям ниже.
  3. Войдите на портал управления Azure.
  4. Щелкните значок Active Directory в левом меню, а затем выберите нужный каталог.
  5. В верхнем меню щелкните Приложения. Если в каталоге нет добавленных приложений, на этой странице отображается только ссылка "Добавить приложение". Щелкните ссылку или, как вариант, можно нажать кнопку "Добавить" на панели команд.
  4. На странице **Что необходимо сделать?** щелкните ссылку **Добавить приложение, разрабатываемое моей организацией**.
  5. На странице **Расскажите о своем приложении** укажите имя приложения и выберите **собственное клиентское приложение**, представляющее приложение, которое устанавливается на телефон или компьютер. По завершении нажмите кнопку со стрелкой в правом нижнем углу страницы.
  6. На странице **Свойства приложения** укажите **URI перенаправления** собственного клиентского приложения, затем установите флажок в нижнем правом углу страницы. </br>Приложение добавляется в систему, и выполняется переход на страницу быстрого запуска для вашего приложения.
8. Включите собственное приложение, которое должно быть доступно для других приложений в каталоге.
  9. В верхнем меню щелкните элемент **Приложения**, выберите новое собственное приложение и нажмите кнопку **Настроить**.
  10. Прокрутите вниз до раздела **Разрешения для других приложений**. Нажмите кнопку **Добавить приложение**, выберите приложение прокси, к которому нужно предоставить доступ в собственном приложении, и установите флажок в нижнем правом углу. В раскрывающемся меню **Делегированные разрешения** выберите новое разрешение. </br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4. Измените код собственного приложения с учетом проверки подлинности с помощью библиотеки Active Directory Authentication Library (ADAL). Для этого включите в код следующее:

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Переменные должны быть заменены следующим образом.


- **TenantId** находится в GUID URL-адреса приложения на странице **Конфигурация** сразу после элемента /Directory/.
- **Frontend URL** — это URL-адрес внешнего интерфейса, который вы ввели в приложение прокси. Он находится на странице **Конфигурация** приложения прокси.
- **Client Id** — это собственное приложение, которое находится на странице **Настройка** собственного приложения.
- **URI перенаправления собственного приложения** находится на странице **Настройка** собственного приложения.

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>Дополнительные сведения о блок-схеме приложения см. в разделе [Из собственного приложения к веб-интерфейсу API](active-directory-authentication-scenarios.md#native-application-to-web-api).






## Что дальше?
У прокси приложения гораздо больше возможностей:


- [Публикация приложений с помощью доменного имени](active-directory-application-proxy-custom-domains.md)
- [Включение единого входа](active-directory-application-proxy-sso-using-kcd.md)
- [Работа с приложениями, поддерживающими утверждения](active-directory-application-proxy-claims-aware-apps.md)
- [Включение условного доступа](active-directory-application-proxy-conditional-access.md)


### Узнайте больше о прокси приложения
- [Просмотрите нашу справку в Интернете](active-directory-application-proxy-enable.md)
- [Ознакомьтесь с блогом о прокси приложения](http://blogs.technet.com/b/applicationproxyblog/)
- [Смотрите наши видео на Channel 9!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Дополнительные ресурсы
* [Регистрация организации в Azure](sign-up-organization.md)
* [Удостоверение Azure](fundamentals-identity.md)

<!---HONumber=AcomDC_1125_2015-->