<properties
   pageTitle="Краткое руководство по API Graph Azure AD | Microsoft Aure"
   description="API Graph Azure Active Directory обеспечивает программный доступ к Azure AD через конечные точки REST API OData. Приложения могут использовать Graph API для выполнений операций создания, чтения, обновления и удаления (CRUD) с данными и объектами каталогов."
   services="active-directory"
   documentationCenter="n/a"
   authors="JimacoMS"
   manager="msmbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="v-jibran@microsoft.com"/>

# Краткое руководство по API Graph Azure AD

API Graph Azure Active Directory обеспечивает программный доступ к Azure AD через конечные точки REST API OData. Приложения могут использовать Graph API для выполнений операций создания, чтения, обновления и удаления (CRUD) с данными и объектами каталогов. Например, можно использовать API Graph для создания нового пользователя, просмотра или обновления свойств пользователя, изменения пароля пользователя, проверки членства в группе для доступа на основе ролей, а также отключения или удаления пользователя. Дополнительные сведения о функциях API Graph и сценариях приложений см. в разделах [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) и [Необходимые условия для использования API Graph Azure AD](https://msdn.microsoft.com/library/hh974476.aspx).

> [AZURE.IMPORTANT] Функциональные возможности API Azure AD Graph доступны также в [Microsoft Graph](https://graph.microsoft.io/) — едином API, который включает API из других служб Майкрософт, таких как Outlook, OneDrive, OneNote, Planner и Office Graph, для которых применяются единая конечная точка и единый маркер доступа.

## Как составить URL-адрес Graph API

В API Graph для доступа к данным и объектам каталога (другими словами, к ресурсам и сущностям), с которыми требуется выполнять операции CRUD, можно использовать URL-адреса на основе протокола OData. URL-адреса, используемые в API Graph, состоят из четырех основных частей — корня службы, идентификатора клиента, пути к ресурсу и строки параметров запроса: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Возьмем, к примеру, следующий URL-адрес: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Корень службы**. В API Graph Azure AD корень службы всегда https://graph.windows.net.
- **Идентификатор клиента**. Это может быть имя проверенного (зарегистрированного) домена, в примере выше — contoso.com. Это также может быть идентификатор объекта клиента или псевдоним myorganization или me. Дополнительные сведения см. в статье [Адресация сущностей и операций в Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
- **Путь к ресурсу**. В этом разделе URL-адрес идентифицирует ресурс для взаимодействия (с пользователями, группами, конкретным пользователем или группой и т. д.). В приведенном выше примере это groups верхнего уровня для адресации данного набора ресурсов. Можно также обращаться к конкретной сущности, например users/{objectId} или users/userPrincipalName.
- **Параметры запроса**. Знак вопроса (?) отделяет раздел пути ресурса от раздела параметров запроса. Параметр запроса api-version необходим для всех запросов в API Graph. API Graph также поддерживает следующие параметры запроса OData: **$filter**, **$orderby**, **$expand**, **$top** и **$format**. В настоящее время не поддерживаются следующие параметры запроса: **$count**, **$inlinecount** и **$skip**. Дополнительные сведения см. в статье [Поддерживаемые запросы, фильтры и операции разбиения на страницы в Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## Версии API Graph

Версия для запроса API Graph указывается в параметре запроса api-version. Для версии 1.5 и более поздних используется числовое значение версии: api-version=1.6. Для предыдущих версий используйте строку даты, соответствующую формату ГГГГ-ММ-ДД. Пример: api-version=2013-11-08. Для компонентов предварительной версии используйте строку beta. Пример: api-version=beta. Дополнительные сведения о различиях между версиями API Graph см. в разделе [Управление версиями API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## Метаданные API Graph

Для получения файла метаданных API Graph добавьте сегмент $metadata после идентификатора клиента в URL-адресе. Например, следующий URL-адрес возвращает метаданные для демонстрационной компании, используемой Graph Explorer: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Можно ввести этот URL-адрес в адресной строке веб-браузера для просмотра метаданных. Возвращаемый документ метаданных с расширением CSDL описывает сущности и сложные типы, их свойства, а также функции и действия, представляемые запрошенной версией API Graph. Если параметр api-version не задан, то возвращаются метаданные для самой последней версии.

## Стандартные запросы

В разделе [Стандартные запросы Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) представлены стандартные запросы, которые могут использоваться с Graph Azure AD, включая запросы для доступа к ресурсам верхнего уровня в каталоге и запросы для выполнения операций в каталоге.

Например, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` возвращает сведения об организации для каталога contoso.com.

Или `https://graph.windows.net/contoso.com/users?api-version=1.6` перечисляет все объекты пользователя в каталоге contoso.com.

## Использование Graph Explorer

Можно использовать Graph Explorer для API Graph Azure AD, чтобы запрашивать данные каталога по мере создания приложения.

> [AZURE.IMPORTANT] Graph Explorer не поддерживает запись или удаление данных из каталога. С помощью Graph Explorer можно выполнять только операции чтения в каталоге Azure AD.

Ниже показан результат, который отобразится, если перейти в Graph Explorer, выбрать параметр "Использовать демонстрационную компанию" и ввести `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6`, чтобы отобразить всех пользователей в демонстрационном каталоге:

![Обозреватель API Graph Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Загрузка Graph Explorer**. Чтобы загрузить этот инструмент, перейдите по адресу [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Выберите **Использовать демонстрационную компанию**, чтобы запустить Graph Explorer для обработки данных из примера клиента. Учетные данные для использования демонстрационной компании не требуются. Вы также можете щелкнуть **Войти** и выполнить вход с использованием учетных данных Azure AD, чтобы запустить Graph Explorer для работы с вашим клиентом. При запуске Graph Explorer для своего собственного клиента вам или вашему администратору потребуется предоставить согласие в процессе входа. Если у вас есть подписка Office 365, у вас также есть клиент Azure AD. Учетные данные, используемые для входа в Office 365, в действительности являются учетными записями Azure AD, и эти учетные данные можно использовать в Graph Explorer.

**Выполнение запроса**. Для выполнения запроса введите запрос в текстовом поле запроса и нажмите кнопку **ПОЛУЧИТЬ** или клавишу **ВВОД**. Результаты появятся в поле ответа. Например, с помощью `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` можно получить список всех объектов групп в демонстрационном каталоге.

Обратите внимание на следующие возможности и ограничения Graph Explorer.
- Возможность автозаполнения в наборах ресурсов. Чтобы опробовать эту возможность, нажмите кнопку **Использовать демонстрационную компанию** и щелкните в текстовом поле запроса (где отображается URL-адрес компании). В раскрывающемся списке можно выбрать набор ресурсов.

- Поддерживает псевдонимы адресации me и myorganization. Например, можно использовать `https://graph.windows.net/me?api-version=1.6` для возврата объекта пользователя, выполнившего вход, или `https://graph.windows.net/myorganization/users?api-version=1.6` для возврата всех пользователей в текущем каталоге. Обратите внимание, что при использовании псевдонима me для демонстрационной компании возвращается ошибка, так как ни один пользователь не выполнил вход и не выдал запрос.

- Раздел заголовков ответа. Его можно использовать для устранения проблем, возникающих при выполнении запросов.

- Средство просмотра JSON для ответа с возможностью развертывания и свертывания.

- Отображение эскизов фотографий не поддерживается.

## Использование Fiddler для записи в каталог

В целях этого краткого руководства можно использовать веб-отладчик Fiddler, чтобы попрактиковаться в выполнении операций записи в каталоге Azure AD. Дополнительные сведения о Fiddler и его установке см. по адресу [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

В следующем примере веб-отладчик Fiddler используется для создания новой группы безопасности MyTestGroup в каталоге Azure AD.

**Получение маркера доступа**. Чтобы получить доступ к Graph Azure AD, клиенты сначала должны успешно пройти проверку подлинности в Azure AD. Дополнительные сведения см. в разделе [Сценарии аутентификации в Azure Active Directory](active-directory-authentication-scenarios.md).

**Составление и выполнение запроса**. Выполните следующие шаги.

1. Откройте веб-отладчик Fiddler и перейдите на вкладку **Composer** (Средство создания).
2. Так как требуется создать новую группу безопасности, выберите в раскрывающемся меню **Post** в качестве метода HTTP. Дополнительные сведения об операциях и разрешениях в объекте группы см. в описании [Group](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) в разделе [Справочник по Azure AD Graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. В поле рядом с методом **Post** в качестве URL-адреса запроса введите следующее: `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Необходимо заменить mytenantdomain доменным именем собственного каталога Azure AD.

4. В поле непосредственно под методом Post введите следующее:

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Замените &lt;your access token&gt; маркером доступа для своего каталога Azure AD.

5. В поле **Request body** (Текст запроса) введите следующее:

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Дополнительные сведения о создании групп см. в разделе [Создать группу](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Дополнительные сведения о сущностях и типах Azure AD, доступ к которым можно получить с помощью Graph, а также сведения об операциях, которые можно с ними выполнять с помощью Graph, см. в разделе [Справочник по Azure AD Graph REST API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## Дальнейшие действия

- Узнайте больше о [API Graph Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Дополнительная информация: [Azure AD Graph API Permission Scopes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).

<!---HONumber=AcomDC_0921_2016-->