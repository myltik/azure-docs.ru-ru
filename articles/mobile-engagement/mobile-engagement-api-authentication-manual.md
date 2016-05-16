<properties 
	pageTitle="Проверка подлинности с помощью интерфейсов REST API Mobile Engagement — настройка вручную"
	description="Описание ручной настройки проверки подлинности для REST API Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="05/03/2016"
	ms.author="piyushjo"/>

# Проверка подлинности с помощью интерфейсов REST API Mobile Engagement — настройка вручную

Это приложение к статье [Проверка подлинности с помощью интерфейсов REST API Mobile Engagement](mobile-engagement-api-authentication.md). Сначала следует ознакомиться с данными в статье. Здесь описывается альтернативный способ выполнения однократной настройки для настройки проверки подлинности с помощью интерфейсов REST API Mobile Engagement на портале Azure.

>[AZURE.NOTE] Приведенные ниже инструкции основаны на этом [руководстве по Active Directory](../resource-group-create-service-principal-portal.md). Они настроены в соответствии с требованиями к проверке подлинности с помощью API Mobile Engagement. Ознакомьтесь с этим руководством, если хотите получить более подобные сведения о выполняемых действиях.

1. Войдите в свою учетную запись Azure через [классическую версию портала](https://manage.windowsazure.com/).

2. Выберите **Active Directory** на левой панели.

     ![выбор Active Directory][1]

3. На портале Azure выберите **Active Directory по умолчанию**.

     ![выбор каталога][2]

	>[AZURE.IMPORTANT] Этот подход работает только в том случае, если вы используете службу Active Directory по умолчанию вашей учетной записи. Он не будет работать, если вы выполняете действия в службе Active Directory, созданной в вашей учетной записи.

4. Чтобы просмотреть приложения в каталоге, щелкните **Приложения**.

     ![просмотр приложений][3]

5. Нажмите кнопку **Добавить**.

     ![добавление приложения][4]

6. Щелкните **Добавить приложение, которое разрабатывает моя организация**.

     ![новое приложение][5]

6. Введите имя приложения и в качестве типа приложения выберите **Веб-приложение и (или) веб-API**.

     ![указание имени приложения][6]

7. В полях **URL-адрес входа** и **URI идентификатора приложения** можно указать любые фиктивные URL-адреса. Они не используются в этом сценарии и не проверяются.

     ![свойства приложения][7]

8. В результате вы получите приложение AAD с указанным ранее именем. Это имя **AD\_APP\_NAME**. Запомните его.

     ![имя приложения][8]

9. Щелкните имя приложения и выберите **Настройка**.

     ![настройка приложения][9]

10. Запишите значение в поле ИД КЛИЕНТА, которое будет использоваться в качестве **CLIENT\_ID** для вызовов API.

     ![настройка приложения][10]

11. Прокрутите вниз до раздела **Ключи** и добавьте ключ с предпочтительной длительностью 2 года (срок действия), а затем нажмите кнопку **Сохранить**.

     ![настройка приложения][11]


12. Сразу же скопируйте значение, которое отображается для ключа, так как оно доступно только в этот момент и не сохраняется. Если вы потеряете его, потребуется создать новый ключ. Это будет значение **CLIENT\_SECRET** для вызовов API.

     ![настройка приложения][12]

	>[AZURE.IMPORTANT] Срок действия ключа завершится при наступлении указанного времени, поэтому в нужный момент продлите срок действия, иначе проверка подлинности API больше не будет работать. Если вам кажется, что ключ был скомпрометирован, удалите его и создайте заново.
 
13. Нажмите кнопку **ПРОСМОТР КОНЕЧНЫХ ТОЧЕК**, после чего откроется диалоговое окно **Конечные точки приложения**.

	![][13]

14. В этом диалоговом окне скопируйте значение в поле **КОНЕЧНАЯ ТОЧКА МАРКЕРА OAUTH 2.0**.

	![][14]

15. Эта конечная точка будет иметь следующий вид, где GUID в URL-адресе — это значение **TENANT\_ID**, поэтому запишите его:

		https://login.microsoftonline.com/<GUID>/oauth2/token

16. Перейдем к настройке разрешений для этого приложения. Для этого необходимо открыть [портал Azure](https://portal.azure.com).

17. Щелкните **Группы ресурсов** и найдите группу **Mobile Engagement**.

	![][15]

18. Щелкните группу ресурсов **Mobile Engagement** и перейдите к колонке **Параметры**.

	![][16]

19. В колонке "Параметры" щелкните **Пользователи**, а затем нажмите кнопку **Добавить**, чтобы добавить пользователя.

	![][17]

20. Щелкните **Выбрать роль**.

	![][18]

21. Щелкните **Владелец**.

	![][19]

22. В поле поиска найдите имя приложения **AD\_APP\_NAME**. Здесь оно не отображается по умолчанию. Найдя имя, выберите его и щелкните **Выбрать** в нижней части колонки.

	![][20]

23. В колонке **Добавление доступа** будет отображаться **1 пользователь, 0 групп**. В этой колонке нажмите кнопку **ОК**, чтобы подтвердить изменение.

	![][21]

Обязательная конфигурация AAD завершена, и все готово для вызова API-интерфейсов.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png

<!---HONumber=AcomDC_0504_2016-->