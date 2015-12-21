<properties 
	pageTitle="Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management" 
	description="Узнайте, как защитить фоновые службы посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Защита фоновых служб посредством проверки подлинности с помощью сертификата клиента в службе Azure API Management

Служба API Management дает возможность защищать доступ к фоновым службам API с помощью сертификатов клиента. В этом руководстве описывается, как управлять сертификатами на портале издателя API и как настроить использование сертификата в API для доступа к фоновой службе.

Сведения об управлении сертификатами с помощью REST API службы управления API см. в разделе [Объект сертификата REST API службы управления API Azure ][].

## <a name="prerequisites"> </a>Предварительные требования

В этом руководстве описано, как настроить для экземпляра службы API Management проверку подлинности с помощью сертификата клиента при доступе из API к фоновой службе. Перед выполнением инструкций, приведенных в этом разделе, в фоновой службе необходимо настроить проверку подлинности с помощью сертификата клиента. Кроме того, нужно иметь доступ к сертификату, который следует отправить на портал издателя API Management, и его паролю.

## <a name="step1"> </a>Отправка сертификата клиента

Для начала на классическом портале Azure для службы управления API щелкните **Управление**. Будет открыт портал издателя службы управления API.

![Портал издателя API][api-management-management-console]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][] в руководстве [Начинаем работу с API Management][].

В меню **API Management** (Управление API) слева выберите пункт **Security** (Безопасность), а затем перейдите на вкладку **Client certificates** (Сертификаты клиента).

![Сертификаты клиента][api-management-security-client-certificates]

Чтобы добавить новый сертификат, щелкните ссылку **Upload certificate** (Загрузить сертификат).

![Передача сертификата][api-management-upload-certificate]

Выберите сертификат и введите пароль к нему.

>Сертификат должен быть в формате **PFX**. Разрешено использовать самозаверяющие сертификаты.

![Передача сертификата][api-management-upload-certificate-form]

Нажмите кнопку **Upload** (Загрузить).

>После этого будет проверен пароль к сертификату. Если он неправильный, появится сообщение об ошибке.

![Сертификат добавлен][api-management-certificate-uploaded]

После добавления сертификат появляется на вкладке **Client certificates** (Сертификаты клиента). Если у вас несколько сертификатов, запишите субъект или последние четыре символа отпечатка. Эти данные используются для выбора сертификата при настройке API для использования сертификатов, как описано в разделе [Настройка API для проверки подлинности шлюза с помощью сертификата клиента][] ниже.

## <a name="step1a"> </a>Удаление сертификата клиента

Чтобы удалить сертификат, щелкните рядом с ним ссылку **Delete** (Удалить).

![Удаление сертификата][api-management-certificate-delete]

Чтобы подтвердить действие, нажмите кнопку **Yes, delete it** (Да, удалить).

![Подтверждение удаления][api-management-confirm-delete]

Если сертификат используется интерфейсом API, появляется предупреждение. Перед удалением сертификата нужно сначала удалить его из всех API, которые настроены на его использование.

![Подтверждение удаления][api-management-confirm-delete-policy]

## <a name="step2"> </a>Настройка API для проверки подлинности шлюза с помощью сертификата клиента

В меню **API Management** (Управление API) слева выберите пункт **APIs** (Интерфейсы API), щелкните имя нужного API и перейдите на вкладку **Security** (Безопасность).

![Безопасность API][api-management-api-security]

В раскрывающемся списке **With credentials** (С учетными данными) выберите пункт **Mutual certificates** (Взаимные сертификаты).

![Сертификаты клиента][api-management-mutual-certificates]

В раскрывающемся списке **Client certificate** (Сертификат клиента) выберите нужный сертификат. Если сертификатов несколько, то выбрать нужный можно по субъекту или последним четырем символам отпечатка, которые вы записали ранее.

![Выбор сертификата][api-management-select-certificate]

Чтобы сохранить изменение конфигурации в API, нажмите кнопку **Save** (Сохранить).

>Изменение вступает в силу немедленно, и для проверки подлинности на фоновом сервере при вызове операций этого API теперь будет использоваться сертификат.

![Сохранение изменений в API][api-management-save-api]

>Если сертификат настроен для проверки подлинности шлюза при доступе к серверной службе API, он становится частью политики для этого API, и его можно увидеть в редакторе политики.

![Политика сертификата][api-management-certificate-policy]

## Дальнейшие действия

Дополнительные сведения о других способах защиты фоновой службы, таких как базовая проверка подлинности HTTP или проверка подлинности с общим секретом, см. в следующем видео.

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Начинаем работу с API Management]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Создание экземпляра службы API Management]: api-management-get-started.md#create-service-instance

[Объект сертификата REST API службы управления API Azure ]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Настройка API для проверки подлинности шлюза с помощью сертификата клиента]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



 

<!---HONumber=AcomDC_1210_2015-->