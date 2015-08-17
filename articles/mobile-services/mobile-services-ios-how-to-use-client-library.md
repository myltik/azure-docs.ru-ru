<properties
	pageTitle="Использование клиентской библиотеки iOS для мобильных служб Azure"
	description="Использование клиентской библиотеки iOS для мобильных служб"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Использование клиентской библиотеки iOS для мобильных служб Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

В этом руководстве показано, как реализовать типичные сценарии с использованием [пакета SDK iOS] для мобильных служб Azure. Если вы еще не работали с мобильными службами, сначала мы рекомендуем ознакомиться с [кратким руководством по мобильным службам] или учебником [Добавление мобильных служб в существующее приложение], чтобы научиться настраивать учетную запись, а также создавать таблицу и мобильную службу.

> [AZURE.NOTE]В этом руководстве используется последняя версия [пакета SDK iOS для мобильных служб](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409). Если в проекте используется пакет SDK предыдущей версии, сначала обновите платформу в Xcode.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>Настройка и необходимые компоненты

В этом руководстве предполагается, что вы создали мобильную службу и таблицу. Дополнительные сведения см. в разделе [Создание таблицы]. Также можно повторно использовать таблицу `TodoItem`, созданную при работе с [кратким руководством по мобильным службам] или учебником [Добавление мобильных служб в существующее приложение]. В этом руководстве предполагается, что в таблице используется та же схему, что и в таблицах, приведенных в этих учебниках. Кроме того, в этом руководстве предполагается, что Xcode ссылается на `WindowsAzureMobileServices.framework` и импортирует `WindowsAzureMobileServices/WindowsAzureMobileServices.h`.

##<a name="create-client"></a>Практическое руководство. Создание клиента мобильных служб

Чтобы получить доступ к мобильной службе Azure в проекте, создайте клиентский объект `MSClient`. Замените `AppUrl` и `AppKey` на значения URL-адреса мобильной службы и панели мониторинга ключа приложения соответственно.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>Практическое руководство. Создание ссылки на таблицу

Для доступа или обновления данных для мобильной службы Azure создайте ссылку на таблицу. Замените `TodoItem` на имя таблицы.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>Практическое руководство. Запрос данных

Чтобы создать запрос к базе данных, отправьте запрос объекта `MSTable`. Следующий запрос возвращает все элементы в `TodoItem` и регистрирует в журнале текст каждого из них.

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>Практическое руководство. Фильтрация возвращаемых данных

Есть множество способов фильтрации результатов.

Для фильтрации данных с использованием предиката используйте `NSPredicate` и `readWithPredicate`. Следующие фильтры позволяют возвращать только незавершенные элементы Todo.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>Практическое руководство. Использование MSQuery

Чтобы выполнить сложный запрос (в том числе на сортировку и подкачку), создайте объект `MSQuery` непосредственно или с помощью предиката:

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` позволяет контролировать некоторые настройки запросов, включая следующие. Выполните запрос `MSQuery` путем вызова в нем `readWithCompletion`, как показано в следующем примере. * Задание порядка результатов. * Ограничение возвращаемых полей. * Ограничение количества возвращаемых записей. * Указание общего количества в ответе. * Задание строковых параметров настраиваемого запроса. * Применение дополнительных функций.


## <a name="sorting"></a>Практическое руководство. Сортировка данных с помощью MSQuery

На примере ниже показано, как сортировать результаты. Чтобы сначала отсортировать данные в порядке возрастания по полю `text`, а затем в порядке убывания по полю `completion`, вызовите `MSQuery`:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

## <a name="paging"></a>Практическое руководство. Возврат данных на страницах с помощью MSQuery

Мобильные службы ограничивают количество записей, возвращаемых в одном ответе. Чтобы контролировать число записей, отображаемых для пользователей, необходимо реализовать систему разбиения на страницы. Разбиение по страницам выполняется с помощью следующих трех свойств объекта **MSQuery**:

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

В следующем примере простая функция запрашивает 5 записей с сервера, а затем добавляет их в локальную коллекцию предварительно загруженных записей:

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

```

## <a name="selecting"></a><a name="parameters"></a>Практическое руководство. Ограничение возвращаемых полей и развертывание строковых параметров запроса с помощью MSQuery

Чтобы ограничить поля, возвращаемые в запросе, укажите имена полей в свойстве **selectFields**. В следующем примере возвращается только текст и заполненные поля:

```
	query.selectFields = @[@"text", @"completed"];
```

Чтобы включить дополнительные строковые параметры в запрос сервера (например, так как они используются в настраиваемом серверном скрипте), заполните `query.parameters` следующим образом:

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>Практическое руководство. Вставка данных

Чтобы вставить новую строку таблицы, создайте `NSDictionary` и вызовите `table insert`. Мобильные службы автоматически создают столбцы на основе `NSDictionary`, если не отключена [динамическая схема].

Если не указано значение параметра `id`, на внутреннем сервере автоматически создается уникальный идентификатор. Укажите собственное значение параметра `id`, чтобы в качестве идентификатора использовать адреса электронной почты, имена пользователей или настраиваемые значения. Наличие собственного идентификатора может упростить операции соединения и бизнес-логику базы данных.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>Практическое руководство. Изменение данных

Чтобы обновить существующую строку, измените элемент и вызовите `update`:

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Можно также указать идентификатор строки и обновляемое поле:

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

При обновлении необходимо по крайней мере задать атрибут `id`.

##<a name="deleting"></a>Практическое руководство. Удаление данных

Чтобы удалить элемент, вызовите `delete` с элементом:

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

Кроме того, для удаления элемента можно указать идентификатор строки:

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

При удалении необходимо по крайней мере задать атрибут `id`.


##<a name="authentication"></a>Практическое руководство. Проверка подлинности пользователей

Мобильные службы Azure поддерживают различных поставщиков удостоверений. Чтобы узнать, как выполнять основные операции, см. [статью о проверке подлинности].

Мобильные службы Azure поддерживают два рабочих процесса проверки подлинности.

- **Управляемый сервером вход**. Мобильные службы Azure контролируют вход в систему от имени вашего приложения. Отображается страница входа конкретного поставщика, используемого для проверки подлинности.

- **Управляемый клиентом вход**. _Приложение_ запрашивает маркер у поставщика удостоверений, а затем предоставляет его мобильным службам Azure для проверки подлинности.

После успешной проверки подлинности вы получаете объект-пользователь со значением идентификатора пользователя и маркером проверки подлинности. Сведения об использовании этого идентификатора для авторизации пользователей приведены в [статье об авторизации на стороне службы]. Чтобы узнать, как предоставлять доступ к таблице только прошедшим проверку подлинности пользователям, см. статью [Разрешения].

### Управляемый сервером вход

Ниже показано, как добавить возможность управляемого сервером входа в проект [быстрого запуска мобильных служб]. Вы можете использовать этот же код для других проектов. Дополнительные сведения и комплексный пример см. в [статье о проверке подлинности].

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### Управляемый клиентом вход (единый вход)

Чтобы включить единый вход, вы можете войти в систему за пределами клиента мобильных служб. Кроме того, такой вход можно выполнить, если приложение обращается непосредственно к поставщику удостоверений. В этих случаях можно войти в мобильные службы, предоставив маркер, полученный независимо от поддерживаемого поставщика удостоверений.

В следующем примере для реализации единого входа для приложений iOS используется пакет [Live Connect SDK]. Здесь предполагается, что вы ранее создали экземпляр **LiveConnectClient** с именем `liveClient` в контроллере, а пользователь вошел в систему.

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>Практическое руководство. Кэширование маркеров проверки подлинности

Посмотрим, как кэшировать маркеры в проекте [быстрого запуска мобильных служб]. Вы можете выполнить алогичные действия в рамках любого проекта.[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="errors"></a>Практическое руководство. Обработка ошибок

При вызове мобильной службы блок завершения содержит параметр `NSError *error`. Если возникает ошибка, этот параметр не является пустым. В коде необходимо проверить этот параметр и обработать ошибку по мере необходимости.

Файл [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) определяет константы `MSErrorResponseKey`, `MSErrorRequestKey` и `MSErrorServerItemKey`, что позволяет получить дополнительные данные об этой ошибке. Кроме того, этот файл определяет константы для каждого кода ошибки. Пример использования констант `MSErrorServerItemKey` и `MSErrorPreconditionFailed` см. в [статье об обработчике конфликтов].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Добавление мобильных служб в существующее приложение]: /develop/mobile/tutorials/get-started-data
[быстрого запуска мобильных служб]: /develop/mobile/tutorials/get-started-ios
[кратким руководством по мобильным службам]: /develop/mobile/tutorials/get-started-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[статье о проверке подлинности]: /develop/mobile/tutorials/get-started-with-users-ios
[статью о проверке подлинности]: /develop/mobile/tutorials/get-started-with-users-ios
[пакета SDK iOS]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Разрешения]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[статье об авторизации на стороне службы]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[динамическая схема]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Создание таблицы]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[статье об обработчике конфликтов]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=August15_HO6-->