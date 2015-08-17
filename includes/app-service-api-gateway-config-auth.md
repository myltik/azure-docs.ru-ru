4. Перейдите к колонке шлюза приложения API.

	![Выберите шлюз](./media/app-service-api-gateway-config-auth/gateway.png)

7. В колонке **Шлюз** щелкните пункт **Параметры**, а затем выберите **Удостоверение**.

	![Щелкните «Параметры»](./media/app-service-api-gateway-config-auth/clicksettingsingateway.png)

	![Щелкните «Удостоверение»](./media/app-service-api-gateway-config-auth/clickidentity.png)

	Из колонки **Удостоверение** можно переходить к другим колонкам для настройки проверки подлинности с помощью Azure Active Directory и некоторых других поставщиков.

	![Колонка «Удостоверение»](./media/app-service-api-gateway-config-auth/identityblade.png)
  
3. Выберите поставщика удостоверений, которого следует использовать, и выполните шаги в соответствующей статье, чтобы настроить приложение API с помощью того же поставщика. Эти статьи были написаны для мобильных приложений, но действия не отличаются от действий для приложений API. В некоторых процедурах необходимо использовать [портал Azure].

 - [Учетная запись Майкрософт](../articles/app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Вход в Facebook](../articles/app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Вход в Twitter](../articles/app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Вход в Google](../articles/app-service-mobile/app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](../articles/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Например, на снимках экрана ниже показано, что должно отображаться на страницах [портала Azure] и в колонках [портала предварительной версии Azure] после настройки функции проверки подлинности Azure Active Directory.

На портале предварительной версии Azure в колонке **Azure Active Directory** содержится **идентификатор клиента** из приложения, созданного на вкладке «Azure Active Directory» портала Active, а в колонке **Разрешенные клиенты** содержится ваш клиент Azure Active Directory (например, contoso.onmicrosoft.com).

![Колонка Azure Active Directory](./media/app-service-api-gateway-config-auth/tdinaadblade.png)

На портале Azure на вкладке **Настройка** для приложения, созданного на вкладке **Azure Active Directory**, содержатся значения **URL-адрес единого входа**, **URI идентификатора приложения** и **URL-адрес ответа** из колонки **Azure Active Directory** на портале предварительной версии Azure.

![](./media/app-service-api-gateway-config-auth/oldportal1.png)

![](./media/app-service-api-gateway-config-auth/oldportal2.png)

![](./media/app-service-api-gateway-config-auth/oldportal3.png)

![](./media/app-service-api-gateway-config-auth/oldportal4.png)

(В поле «URL-адрес ответа», показанном на рисунке, один и тот же URL-адрес отображается дважды, один раз с префиксом `http:` и второй раз с префиксом `https:`.)

<!---HONumber=August15_HO6-->