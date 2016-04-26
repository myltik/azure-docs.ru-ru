<properties 
   pageTitle="Начало работы с хранилищем озера данных Azure с помощью интерфейсов REST API| Microsoft Azure" 
   description="Использование интерфейсов REST API WebHDFS для выполнения операций в хранилище озера данных" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# Начало работы с хранилищем озера данных Azure с помощью интерфейсов REST API

> [AZURE.SELECTOR]
- [Портал](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [Пакет SDK для .NET](data-lake-store-get-started-net-sdk.md)
- [Пакет SDK для Java](data-lake-store-get-started-java-sdk.md)
- [ИНТЕРФЕЙС REST API](data-lake-store-get-started-rest-api.md)
- [Интерфейс командной строки Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

В этой статье содержатся сведения об использовании интерфейсов REST API WebHDFS и REST API хранилища озера данных для управления учетными записями, а также для выполнения операций файловой системы в хранилище озера данных Azure. Хранилище озера данных располагает собственными интерфейсами REST API для операций управления учетными записями. Однако поскольку хранилище озера данных совместимо с экосистемой HDFS и Hadoop, оно поддерживает использование интерфейсов REST AP WebHDFS для выполнения операций файловой системы.

>[AZURE.NOTE] Подробные сведения о поддержке REST API для хранилища озера данных см. в [справочнике по REST API хранилища озера данных Azure](https://msdn.microsoft.com/library/mt693424.aspx).

## Предварительные требования

- **Подписка Azure.**. См. [Бесплатная пробная версия Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Включите свою подписку Azure** для общедоступной предварительной версии хранилища озера данных. См. [инструкции](data-lake-store-get-started-portal.md#signup).
- **Создание приложения Azure Active Directory**. См. статью [Создание приложения Active Directory и субъекта-службы с помощью портала](../resource-group-create-service-principal-portal.md). После создания приложения необходимо получить следующие связанные с ним значения.
	- Получение идентификатора клиента для приложения.
	- Создание ключа проверки подлинности
	- Настройка делегированных разрешений

	Инструкции о получении этих значений доступны по указанной выше ссылке.
- **Назначение роли для приложения Azure Active Directory**. Роль может быть задана на уровне области действия, согласно которой необходимо предоставить разрешение приложению Azure Active Directory. Например, можно назначить приложение на уровне подписки или на уровне группы ресурсов. Инструкции см. в статье [Assign application to a role \(Назначение приложения роли\)](../resource-group-create-service-principal-portal.md#assign-application-to-role).
- [cURL](http://curl.haxx.se/). В этой статье для демонстрации вызовов REST API к учетной записи хранения озера данных используется cURL.

## Как выполнить аутентификацию с помощью Azure Active Directory?

Существует два способа проверки подлинности с помощью Azure Active Directory.

* **Интерактивный**, когда приложение предлагает пользователю войти в систему. Дополнительные сведения см. в статье [Authorization code grant flow \(Поток предоставления кода авторизации\)](https://msdn.microsoft.com/library/azure/dn645542.aspx).

* **Неинтерактивный**, когда приложение предоставляет собственные учетные данные. Дополнительные сведения см. в статье [Service to service calls using credentials \(Вызовы между службами с помощью учетных данных\)](https://msdn.microsoft.com/library/azure/dn645543.aspx).

В этой статье используется **неинтерактивный** подход. Для этого необходимо отправить запрос POST, аналогичный показанному ниже.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Выходные данные этого запроса будут содержать маркер авторизации \(обозначен `access-token` в приведенных ниже выходных данных\) для дальнейшей передачи в вызовах REST API. Сохраните этот маркер в текстовый файл. Он потребуется в данной статье позднее.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Создание учетной записи хранения озера данных

Эта операция основана на вызове REST API, определенном [здесь](https://msdn.microsoft.com/library/mt694078.aspx).

Используйте следующую команду cURL: Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

В приведенной выше команде замените \<`REDACTED`\> маркером авторизации, полученным ранее. Полезные данные запроса для этой команды находятся в файле **input.json**, предоставленном для параметра `-d` выше. Содержимое файла input.json выглядит следующим образом:

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Создание папок в учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Используйте следующую команду cURL: Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

В приведенной выше команде замените \<`REDACTED`\> маркером авторизации, полученным ранее. Эта команда создает каталог с именем **mytempdir** в корневой папке учетной записи хранения озера данных.

В случае успешного завершения операции будет отображен примерно следующий ответ:

	{"boolean":true}

## Вывод списка папок в учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Используйте следующую команду cURL: Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

В приведенной выше команде замените \<`REDACTED`\> маркером авторизации, полученным ранее.

В случае успешного завершения операции будет отображен примерно следующий ответ:

	{
	"FileStatuses": {
		"FileStatus": [{
			"length": 0,
			"pathSuffix": "mytempdir",
			"type": "DIRECTORY",
			"blockSize": 268435456,
			"accessTime": 1458324719512,
			"modificationTime": 1458324719512,
			"replication": 0,
			"permission": "777",
			"owner": "NotSupportYet",
			"group": "NotSupportYet"
		}]
	}
	}

## Отправка данных в учетную запись хранения озера данных Azure

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Процесс отправки данных с помощью REST API WebHDFS состоит из двух этапов, как описано ниже.

1. Отправьте запрос HTTP PUT, не высылая нужные данные файла. В следующей команде замените **\<yourstorename\>** именем вашего хранилища озера данных.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	Выходные данные этой команды будут содержать временный URL-адрес перенаправления, аналогичный показанному ниже.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. Теперь необходимо отправить другой запрос HTTP PUT по URL-адресу, указанному для свойства **Расположение** в ответе. Замените **\<yourstorename\>** именем вашего хранилища озера данных.

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	Результат будет выглядеть примерно так:

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Чтение данных из учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Процесс чтения данных из учетной записи хранения озера данных состоит из двух этапов.

* Сначала следует отправить запрос GET к конечной точке `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Будет возвращено расположение для отправки следующего запроса GET.
* Затем нужно отправить запрос GET к конечной точке `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Будет отображено содержимое файла.

Однако поскольку на первом и втором этапе применяются одинаковые входные параметры, для отправки первого запроса можно использовать параметр `-L`. Параметр `-L` фактически объединяет два запроса в один, а также позволяет cURL повторно отправить запрос в новое расположение. И, наконец, отображаются выходные данные всех вызовов запросов, как показано ниже. Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Должен отобразиться результат, аналогичный приведенному ниже:

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## Переименование файла в учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Чтобы переименовать файл, используйте следующую команду cURL: Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Должен отобразиться результат, аналогичный приведенному ниже:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Удаление файла из учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Чтобы удалить файл, используйте следующую команду cURL: Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Вы должны увидеть подобные выходные данные:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Удаление учетной записи хранения озера данных

Эта операция основана на вызове REST API, определенном [здесь](https://msdn.microsoft.com/library/mt694075.aspx).

Чтобы удалить учетную запись хранилища озера данных, используйте следующую команду cURL: Замените **\<yourstorename\>** именем вашего хранилища озера данных.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Вы должны увидеть подобные выходные данные:

	HTTP/1.1 200 OK
	...
	...

## См. также

- [Приложения больших данных с открытым исходным кодом, которые работают с хранилищем озера данных Azure](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->