<properties umbracoNaviHide="0" pageTitle="Интеграция Azure AD с Google Apps" metaKeywords="интеграция Azure Google Apps" description="Узнайте, как можно интегрировать Azure AD с Google Apps." linkid="documentation-services-identity-windows-azure-ad-integration-with-google=apps" urlDisplayName="Интеграция Azure AD с Google Apps" headerExpose="" footerExpose="" disqusComments="0" editor="lisatoft" manager="terrylan" title="Интеграция Azure AD с Google Apps" authors="" />

# Интеграция Azure AD с Google Apps
Цель данного учебника — показать интеграцию Azure и Google Apps. Сценарий, описанный в этом учебнике, предполагает, что у вас уже имеется:

- Действующая подписка на Azure
- Тестовый клиент в Google Apps

Если у вас еще нет действительного клиента в Google Apps, вы можете, например, зарегистрироваться и получить пробную учетную запись на веб-сайте Google Apps для бизнеса.

Сценарий, описанный в этом учебнике, состоит из следующих блоков:

Включение интеграции приложений для Google Apps
Настройка единого входа
Включение доступа к API Google Apps
Добавление пользовательских доменов
Настройка подготовки учетных записей пользователей

# Включение интеграции приложений для Google Apps #
В этом разделе показано, как включить интеграцию приложений для Google Apps.

## Чтобы включить интеграцию приложений для Google Apps, выполните следующие действия: ##

1.  На портале управления Azure в левой области навигации щелкните **Active Directory**.
2.  В списке **Каталог** выберите каталог, для которого нужно включить интеграцию каталогов.
3.  Чтобы открыть представление приложений, в представлении каталога щелкните **Приложения** в верхнем меню.
4.  Внизу щелкните **Добавить**, чтобы открыть диалоговое окно **Добавить приложение**.
5.  В диалоговом окне **Интегрировать приложение с Azure AD** щелкните **Управление доступом к приложению**.
6.  На странице управления **Выберите приложение** в списке приложений выберите **Google Apps**.
7.  Нажмите кнопку **Завершить**, чтобы добавить приложение и закрыть диалоговое окно.

# Настройка единого входа #
В этом разделе показано, как разрешить пользователям проходить проверку подлинности в Google Apps со своей учетной записью Azure AD, используя федерацию на основе протокола SAML.

## Чтобы настроить федеративный единый вход, выполните следующие действия:

1. На портале управления Azure в левой области навигации выберите <strong>Active Directory</strong>, чтобы открыть страницу диалогового окна Active Directory.
2. В списке каталогов выберите каталог, чтобы открыть страницу настройки каталога.
3. В меню верхнего уровня выберите <strong>ПРИЛОЖЕНИЯ</strong>.
4. В списке приложений выберите <strong>Google Apps</strong>, чтобы открыть окно настройки Google Apps.
5. Чтобы открыть диалоговое окно <strong>НАСТРОЙКА ЕДИНОГО ВХОДА</strong>, щелкните <strong>Настройка единого входа</strong>.

	![Google_Tutorial_01](./media/integration-azure-google-apps/Google_Tutorial_01.png)

6. На странице диалогового окна "Выбрать режим единого входа для этого приложения" выберите РЕЖИМ "Пользователи проходят проверку подлинности с помощью своей учетной записи Azure AD", а затем нажмите кнопку "Далее".

	![Google_Tutorial_02](./media/integration-azure-google-apps/Google_Tutorial_02.png)

7. На странице диалогового окна <strong>Настроить URL-адрес приложения</strong> в текстовом поле <strong>URL-АДРЕС КЛИЕНТА GOOGLE APPS</strong> введите URL-адрес клиента Google Apps и нажмите кнопку <strong>Далее</strong>.

	![Google_Tutorial_03](./media/integration-azure-google-apps/Google_Tutorial_03.png)

8. На странице диалогового окна <strong>Настройка единого входа в Google Apps</strong> выполните приведенные ниже действия и нажмите кнопку <strong>Завершить</strong>.
		
	+ Щелкните <strong>Загрузить сертификат</strong> и сохраните сертификат как <strong>c:\googleapps.cer</strong>.
	+ Откройте страницу входа в Google Apps и выполните вход.

	![Google_Tutorial_04](./media/integration-azure-google-apps/Google_Tutorial_04.png)

	+ На <strong>консоли администрирования</strong> щелкните "Безопасность".

	![Google_Tutorial_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

	Если значок "Безопасность" не отображается, щелкните элементы управления "Дополнительно" в нижней части страницы.

9. На странице <strong>Безопасность</strong> щелкните параметры <strong>Дополнительно</strong>.

	![Google_Tutorial_06](./media/integration-azure-google-apps/Google_Tutorial_06.png)

10. В разделе параметров <strong>Дополнительно</strong> выберите <strong>Настроить единый вход</strong>.

	![Google_Tutorial_07](./media/integration-azure-google-apps/Google_Tutorial_07.png)

11. На странице настройки единого входа выполните следующие действия:

	![Google_Tutorial_08](./media/integration-azure-google-apps/Google_Tutorial_08.png)

		+ Выберите <strong>Включить единый вход</strong>.
		+ На странице <strong>Настройка единого входа в Google Apps</strong> на портале Azure AD скопируйте <strong>URL-АДРЕС ЕДИНОГО ВХОДА</strong> и вставьте его в соответствующее текстовое поле на странице <strong>Безопасность</strong> <strong>консоли администрирования</strong> в Google Apps.
		+ На странице <strong>Настройка единого входа в Google Apps</strong> на портале Azure AD скопируйте <strong>URL-адрес службы единого выхода</strong> и вставьте его в соответствующее текстовое поле на странице <strong>Безопасность</strong> <strong>консоли администрирования</strong> в Google Apps.
		+ На странице <strong>Настройка единого входа в Google Apps</strong> на портале Azure AD скопируйте значение <strong>Изменить URL-адрес пароля</strong> и вставьте его в соответствующее текстовое поле на странице <strong>Безопасность</strong> <strong>консоли администрирования</strong> в Google Apps.
		+ Нажмите кнопку <strong>Обзор</strong>, чтобы найти <strong>Сертификат проверки</strong>, затем щелкните "Отправить".
		+ Щелкните <strong>Сохранить</strong>, чтобы сохранить изменения.


12. На странице <strong>Настройка единого входа в Google Apps</strong> на портале Azure AD нажмите кнопку "Завершить".

Теперь можно открыть [панель доступа](https://login.microsoftonline.com/login.srf?wa=wsignin1.0&rpsnv=2&ct=1384289458&rver=6.1.6206.0&wp=MCMBI&wreply=https:%2F%2Faccount.activedirectory.windowsazure.com%2Flanding.aspx%3Ftarget%3D%252fapplications%252fdefault.aspx&lc=1033&id=500633) и протестировать единый вход в Google Apps.

# Включение доступа к API Google Apps
При интеграции Azure Active Directory с Google Apps для подготовки учетных записей пользователей необходимо включить доступ к API для вашего клиента в Google Apps.

## Чтобы включить доступ к API Google Apps, выполните следующие действия:

1. Войдите в клиент Google Apps.
1. На <strong>консоли администрирования</strong> щелкните <strong>Безопасность</strong>.
<p></p>
	![Google_Tutorial_05](./media/integration-azure-google-apps/Google_Tutorial_05.png)

	Если значок "Безопасность" не отображается, щелкните элементы управления "Дополнительно" в нижней части консоли администрирования.
1. На странице "Безопасность" щелкните <strong>Справочник по API</strong>, чтобы открыть соответствующую страницу диалогового окна настройки.
1. Выберите <strong>Включить доступ к API</strong>.
<p></p>
	![Google_Tutorial_09](./media/integration-azure-google-apps/Google_Tutorial_09.png)


# Добавление пользовательских доменов
Чтобы настроить подготовку учетных записей пользователей в Google Apps, домен Azure AD и домен Google Apps должны иметь одинаковое полное доменное имя (FQDN). Однако, например, при использовании пробных клиентов для тестирования сценария в этом учебнике, полные доменные имена клиентов обычно не совпадают. Чтобы устранить эту проблему, можно настроить пользовательские домены в Azure AD и Google Apps. 
Для настройки пользовательского домена требуется доступ к файлу зоны DNS общедоступного домена. 

![Google_Tutorial_10](./media/integration-azure-google-apps/Google_Tutorial_10.png)

##Чтобы добавить пользовательский домен в Azure AD, выполните следующие действия:

1. На портале управления Azure в левой области навигации выберите <strong>Active Directory</strong>, чтобы открыть страницу диалогового окна <strong>Active Directory</strong>.
1. В списке каталогов выберите каталог, чтобы открыть страницу настройки каталога.
1. В меню верхнего уровня выберите <strong>ДОМЕНЫ</strong>.
1. Чтобы открыть текстовое поле <strong>ДОБАВИТЬ ИМЯ ДОМЕНА</strong>, введите имя домена и щелкните <strong>Добавить</strong>.
1. Нажмите кнопку <strong>Далее</strong>, чтобы открыть страницу диалогового окна <strong>Проверить имя домена</strong>.

	![Google_Tutorial_11](./media/integration-azure-google-apps/Google_Tutorial_11.png)
1. Выберите <strong>ТИП ЗАПИСИ</strong>, а затем зарегистрируйте выбранную запись в файле зоны DNS.

	![Google_Tutorial_12](./media/integration-azure-google-apps/Google_Tutorial_12.png)
1. С помощью <strong>команды nslookup</strong> необходимо проверить, удалось ли успешно зарегистрировать DNS-запись.

	![Google_Tutorial_13](./media/integration-azure-google-apps/Google_Tutorial_13.png)

## Чтобы добавить пользовательский домен в Google Apps, выполните следующие действия:

1. Войдите в клиент Google Apps.
1. На **консоли администрирования** щелкните **Домены**.

	![Google_Tutorial_14](./media/integration-azure-google-apps/Google_Tutorial_14.png)

1. Щелкните <strong>Добавить пользовательский домен</strong>.

	![Google_Tutorial_15](./media/integration-azure-google-apps/Google_Tutorial_15.png)

1. Щелкните <strong>Использовать уже имеющийся домен</strong> и нажмите кнопку <strong>Продолжить</strong>.

	![Google_Tutorial_16](./media/integration-azure-google-apps/Google_Tutorial_16.png)

1. Введите имя для пользовательского домена и нажмите кнопку <strong>Продолжить</strong>.

	![Google_Tutorial_17](./media/integration-azure-google-apps/Google_Tutorial_17.png)

1. Выполните действия по проверке владельца домена.
	
	Если у вас уже настроен федеративный единый вход, необходимо обновить URL-адрес клиента Google Apps в конфигурации федеративного единого входа.



# Настройка подготовки учетных записей пользователей 
В этом разделе показано, как включить подготовку учетных записей пользователей Active Directory для Google Apps.

## Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия:

1. На портале управления Azure в левой области навигации выберите <strong>Active Directory</strong>, чтобы открыть страницу диалогового окна <strong>Active Directory</strong>.
1. В списке каталогов выберите каталог, чтобы открыть страницу настройки каталога.
1. В меню верхнего уровня выберите <strong>ПРИЛОЖЕНИЯ</strong>.
В списке приложений выберите <strong>Google Apps</strong>, чтобы открыть диалоговое окно настройки <strong>Google Apps</strong>.
1. Чтобы открыть диалоговое окно <strong>НАСТРОИТЬ СИНХРОНИЗАЦИЮ УЧЕТНЫХ ЗАПИСЕЙ</strong>, щелкните <strong>Настроить синхронизацию учетных записей</strong>.
1. На странице диалогового окна <strong>НАСТРОИТЬ СИНХРОНИЗАЦИЮ УЧЕТНЫХ ЗАПИСЕЙ</strong> укажите имя домена Google Apps, имя пользователя Google Apps и пароль Google Apps, а затем нажмите кнопку <strong>Далее</strong>.

	![Google_Tutorial_18](./media/integration-azure-google-apps/Google_Tutorial_18.png)

1. На странице диалогового окна <strong>Подтверждение</strong> нажмите кнопку <strong>Завершить</strong>, чтобы закрыть диалоговое окно <strong>НАСТРОИТЬ СИНХРОНИЗАЦИЮ УЧЕТНЫХ ЗАПИСЕЙ</strong>.

Теперь можно создать тестовую учетную запись, подождать 10 минут и проверить, синхронизирована ли учетная запись в Google Apps.

См. также
[Рекомендации по управлению улучшениями для доступа приложений к Azure Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/18409.best-practices-for-managing-the-application-access-enhancements-for-windows-azure-active-directory.aspx)
 

