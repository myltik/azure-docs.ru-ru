<properties 
 pageTitle="Исходящая проверка подлинности планировщика" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>
 
# Исходящая проверка подлинности планировщика

Задания планировщика могут вызывать службы, требующие проверки подлинности. В этом случае вызванная служба определяет, может ли задание планировщика получить доступ к ее ресурсам. В число таких служб входят другие службы Azure, Salesforce.com, Facebook и защищенные пользовательские веб-сайты.

## Добавление и удаление проверки подлинности

Добавить в задание планировщика проверку подлинности очень просто: при создании или обновлении задания добавьте в элемент `request` дочерний элемент JSON `authentication`. Секретные данные, передаваемые службе планировщика запросами PUT, POST или PATCH в составе объекта `authentication`, в ответах не отображаются. В ответах секретная информация обнуляется или получает открытый маркер, который представляет сущность, прошедшую проверку подлинности.

Чтобы удалить проверку подлинности, примените непосредственно к заданию запрос PUT или PATCH, установив для объекта `authentication` нулевое значение. Никакие свойства проверки подлинности в ответе показаны не будут.

В настоящее время поддерживаются только следующие типы проверки подлинности: модель `ClientCertificate` (для использования клиентских SSL/TLS-сертификатов), модель `Basic` (для обычной проверки подлинности) и модель `ActiveDirectoryOAuth` (для проверки подлинности Active Directory OAuth.)

## Текст запроса для проверки подлинности ClientCertificate

При добавлении проверки подлинности с использованием модели `ClientCertificate` укажите в тексте запроса следующие дополнительные элементы.

|Элемент|Описание|
|:---|:---|
|_authentication (родительский элемент)_|Объект проверки подлинности для использования клиентского SSL-сертификата.|
|_type_|обязательный параметр. Тип проверки подлинности. Для клиентских SSL-сертификатов используйте значение `ClientCertificate`.|
|_pfx_|обязательный параметр. Содержимое PFX-файла с кодировкой Base64.|
|_password_|обязательный параметр. Пароль для доступа к PFX-файлу.|


## Текст ответа при проверке подлинности ClientCertificate

При отправке запроса со сведениями о проверке подлинности в ответ включаются следующие элементы.

|Элемент |Описание |
|:--|:--|
|_authentication (родительский элемент)_ |Объект проверки подлинности для использования клиентского SSL-сертификата.|
|_type_ |Тип проверки подлинности. Для клиентских SSL-сертификатов это значение равно `ClientCertificate`.|
|_certificateThumbprint_ |Отпечаток сертификата.|
|_certificateSubjectName_ |Различающееся имя субъекта сертификата.|
|_certificateExpiration_ |Срок действия сертификата.|

## Пример запроса и ответа при использовании проверки подлинности ClientCertificate

В приведенном ниже примере отправляется запрос PUT, включающий проверку подлинности `ClientCertificate`. Запрос выглядит следующим образом:


	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Content-Length: 4013
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "clientcertificate",
			"password": "test",
			"pfx": "long-pfx-key”
		  }
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

После отправки запроса поступает следующий ответ:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	 

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "ClientCertificate",
			"certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
			"certificateExpiration": "2021-01-01T08:00:00Z",
			"certificateSubjectName": "CN=Scheduler Management"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}
## Текст запроса для обычной проверки подлинности

При добавлении проверки подлинности с использованием модели `Basic` укажите в тексте запроса следующие дополнительные элементы.

|Элемент|Описание|
|:--|:--|
|_authentication (родительский элемент)_ |Объект проверки подлинности для использования обычной проверки подлинности.|
|_type_ |обязательный параметр. Тип проверки подлинности. Для обычной проверки подлинности используйте значение `Basic`.|
|_username_ |обязательный параметр. Имя пользователя для проверки подлинности.|
|_password_ |обязательный параметр. Пароль для проверки подлинности.|

## Текст ответа при обычной проверке подлинности

При отправке запроса со сведениями о проверке подлинности в ответ включаются следующие элементы.

|Элемент|Описание|
|:--|:--|
|_authentication (родительский элемент)_ |Объект проверки подлинности для использования обычной проверки подлинности.|
|_type_ |Тип проверки подлинности. Для обычной проверки подлинности это значение равно `Basic`.|
|_username_ |Имя пользователя, прошедшего проверку подлинности.|

## Пример запроса и ответа при использовании обычной проверки подлинности

В приведенном ниже примере отправляется запрос PUT, включающий проверку подлинности `Basic`. Запрос выглядит следующим образом:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		"authentication":{  
		  "username":"user1",
		  "password":"password",
		  "type":"basic"
		  }           
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

После отправки запроса поступает следующий ответ:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"username":"user1",
			"type":"Basic"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Текст запроса для проверки подлинности ActiveDirectoryOAuth

При добавлении проверки подлинности с использованием модели `ActiveDirectoryOAuth` укажите в тексте запроса следующие дополнительные элементы.

|Элемент |Описание |
|:--|:--|
|_authentication (родительский элемент)_ |Объект проверки подлинности для использования проверки подлинности ActiveDirectoryOAuth.|
|_type_ |обязательный параметр. Тип проверки подлинности. Для проверки подлинности ActiveDirectoryOAuth используйте значение `ActiveDirectoryOAuth`.|
|_tenant_ |обязательный параметр. Идентификатор клиента Azure AD.|
|_audience_ |обязательный параметр. Это свойство имеет значение https://management.core.windows.net/.|.
|_clientid_ |обязательный параметр. Укажите идентификатор клиента для приложения Azure AD.|
|_secret_ |обязательный параметр. Секретные данные клиента, запрашивающего маркер.|

### Определение идентификатора клиента

Идентификатор клиента Azure AD можно узнать, выполнив команду `Get-AzureAccount` в Azure PowerShell.

## Текст ответа при проверке подлинности ActiveDirectoryOAuth

При отправке запроса со сведениями о проверке подлинности в ответ включаются следующие элементы.

|Элемент |Описание |
|:--|:--|
|_authentication (родительский элемент)_ |Объект проверки подлинности для использования проверки подлинности ActiveDirectoryOAuth.|
|_type_ |Тип проверки подлинности. Для аутентификации ActiveDirectoryOAuth это значение равно `ActiveDirectoryOAuth`.|
|_tenant_ |Идентификатор клиента Azure AD. |
|_audience_ |Имеет значение https://management.core.windows.net/.|.
|_clientid_ |Идентификатор клиента для приложения Azure AD.|

## Пример запроса и ответа при использовании проверки подлинности ActiveDirectoryOAuth

В приведенном ниже примере отправляется запрос PUT, включающий аутентификацию `ActiveDirectoryOAuth`. Запрос выглядит следующим образом:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"01234567-89ab-cdef-0123-456789abcdef",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"secret": "&lt;secret-key&gt;",
			"type":"ActiveDirectoryOAuth"
		  }                      
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

После отправки запроса поступает следующий ответ:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET


	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"01234567-89ab-cdef-0123-456789abcdef",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"type":"ActiveDirectoryOAuth"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## См. также
 

 [Что такое планировщик?](scheduler-intro.md)
 
 [Основные понятия, терминология и иерархия сущностей планировщика Azure](scheduler-concepts-terms.md)

 [Приступая к работе с планировщиком Azure на портале Azure](scheduler-get-started-portal.md)

 [Планы и выставление счетов в планировщике Azure](scheduler-plans-billing.md)

 [Справочник по API REST планировщика Azure](https://msdn.microsoft.com/library/dn528946)

 [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)

 [Высокая доступность и надежность планировщика Azure](scheduler-high-availability-reliability.md)

 [Ограничения, значения по умолчанию и коды ошибок планировщика Azure](scheduler-limits-defaults-errors.md)


  

 
  

<!---HONumber=AcomDC_1217_2015-->