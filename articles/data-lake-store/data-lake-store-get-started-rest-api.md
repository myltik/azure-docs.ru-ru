<properties 
   pageTitle="Начало работы с хранилищем озера данных Azure с помощью интерфейсов REST API| Microsoft Azure" 
   description="Использование интерфейсов REST API WebHDFS для выполнения операций в хранилище озера данных" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
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

- **Создание приложения Azure Active Directory**. Это приложение будет использоваться для проверки подлинности приложения Data Lake Store в Azure AD. Существуют разные способы проверки подлинности приложения с помощью Azure AD: **проверка подлинности пользователя** или **проверка подлинности со взаимодействием между службами**. Инструкции и дополнительные сведения о проверке подлинности см. в статье [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md) (Проверка подлинности приложений Data Lake Store с помощью Azure Active Directory).

- [cURL](http://curl.haxx.se/). В этой статье для демонстрации вызовов REST API к учетной записи хранения озера данных используется cURL.

## Как выполнить аутентификацию с помощью Azure Active Directory?

Существует два способа проверки подлинности с помощью Azure Active Directory.

### Проверка подлинности пользователя (интерактивная)

В этом сценарии приложение предлагает пользователю войти в систему, и все операции выполняются в контексте пользователя. Выполните следующие действия для использования интерактивной аутентификации.

1. В приложении перенаправьте пользователя на следующий URL-адрес.

		https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

	>[AZURE.NOTE] <REDIRECT-URI> должен быть закодирован для использования в URL-адресе. То есть для https://localhost используйте `https%3A%2F%2Flocalhost`.

	В целях обучения можно заменить значения заполнителей в URL-адресе выше и вставить его в адресную строку веб-браузера. Вы перейдете на страницу аутентификации с помощью учетной записи Azure. После входа в систему вы увидите ответ в адресной строке браузера. Ответ имеет следующий формат.
		
		http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Запишите код авторизации из ответа. В этом учебнике вы можете скопировать код авторизации из адресной строки веб-браузера и передать его в запрос POST к конечной точке маркера, как показано ниже.

		curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

	>[AZURE.NOTE] В этом случае кодировать <REDIRECT-URI> не нужно.

3. Ответ является объектом JSON, содержащим маркер доступа (например, `"access_token": "<ACCESS_TOKEN>"`) и маркер обновления (например, `"refresh_token": "<REFRESH_TOKEN>"`). Приложение использует маркер доступа для обращения к хранилищу озера данных Azure, а маркер обновления — для получения другого маркера доступа, когда срок действия текущего маркера доступа истечет.

		{"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":	"1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  По истечении срока действия маркера доступа можно запросить новый маркер доступа, используя маркер обновления, как показано ниже.

		 curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      		-F grant_type=refresh_token \
      		-F resource=https://management.core.windows.net/ \
      		-F client_id=<CLIENT-ID> \
      		-F refresh_token=<REFRESH-TOKEN>
 
Дополнительные сведения об интерактивной проверке подлинности пользователей см. в статье [Авторизация доступа к веб-приложениям с помощью OAuth 2.0 и Azure Active Directory](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### Проверка подлинности с взаимодействием между службами (неинтерактивная)

В этом сценарии приложение предоставляет свои собственные учетные данные для выполнения операций. Для этого необходимо отправить запрос POST, аналогичный показанному ниже.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Выходные данные этого запроса будут содержать маркер авторизации (обозначен `access-token` в приведенных ниже выходных данных) для дальнейшей передачи в вызовах REST API. Сохраните этот маркер в текстовый файл. Он потребуется в данной статье позднее.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

В этой статье используется **неинтерактивный** подход. Дополнительные сведения о неинтерактивном подходе (вызовы между службами) см. в [этой статье](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## Создание учетной записи хранения озера данных

Эта операция основана на вызове REST API, определенном [здесь](https://msdn.microsoft.com/library/mt694078.aspx).

Используйте следующую команду cURL: Замените **<yourstorename>** именем своего хранилища Data Lake Store.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

В приведенной выше команде замените <`REDACTED`> полученным ранее маркером авторизации. Полезные данные запроса для этой команды находятся в файле **input.json**, предоставленном для параметра `-d` выше. Содержимое файла input.json выглядит следующим образом:

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Создание папок в учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Используйте следующую команду cURL: Замените **<yourstorename>** именем своего хранилища Data Lake Store.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

В приведенной выше команде замените <`REDACTED`> полученным ранее маркером авторизации. Эта команда создает каталог с именем **mytempdir** в корневой папке учетной записи Data Lake Store.

В случае успешного завершения операции будет отображен примерно следующий ответ:

	{"boolean":true}

## Вывод списка папок в учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Используйте следующую команду cURL: Замените **<yourstorename>** именем своего хранилища Data Lake Store.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

В приведенной выше команде замените <`REDACTED`> полученным ранее маркером авторизации.

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

1. Отправьте запрос HTTP PUT, не высылая нужные данные файла. В следующей команде замените **<yourstorename>** именем своего хранилища Data Lake Store.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	Выходные данные этой команды будут содержать временный URL-адрес перенаправления, аналогичный показанному ниже.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. Теперь необходимо отправить другой HTTP-запрос PUT по URL-адресу, указанному для свойства **Расположение** в ответе. Замените **<yourstorename>** именем своего хранилища Data Lake Store.

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

Однако поскольку на первом и втором этапе применяются одинаковые входные параметры, для отправки первого запроса можно использовать параметр `-L`. Параметр `-L` фактически объединяет два запроса в один, а также позволяет cURL повторно отправить запрос к новому расположению. И, наконец, отображаются выходные данные всех вызовов запросов, как показано ниже. Замените **<yourstorename>** именем своего хранилища Data Lake Store.

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

Чтобы переименовать файл, используйте следующую команду cURL: Замените **<yourstorename>** именем своего хранилища Data Lake Store.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Должен отобразиться результат, аналогичный приведенному ниже:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Удаление файла из учетной записи хранения озера данных

Эта операция основана на вызове REST API WebHDFS, определенном [здесь](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Чтобы удалить файл, используйте следующую команду cURL: Замените **<yourstorename>** именем своего хранилища Data Lake Store.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Вы должны увидеть подобные выходные данные:

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Удаление учетной записи хранения озера данных

Эта операция основана на вызове REST API, определенном [здесь](https://msdn.microsoft.com/library/mt694075.aspx).

Чтобы удалить учетную запись хранилища озера данных, используйте следующую команду cURL: Замените **<yourstorename>** именем своего хранилища Data Lake Store.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Вы должны увидеть подобные выходные данные:

	HTTP/1.1 200 OK
	...
	...

## Дополнительные материалы

- [Приложения больших данных с открытым исходным кодом, которые работают с хранилищем озера данных Azure](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_1005_2016-->