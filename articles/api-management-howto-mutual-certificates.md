<properties 
	pageTitle="Защита фоновых служб посредством взаимной проверки подлинности с помощью сертификата в службе Azure API Management" 
	description="Защита фоновых служб посредством взаимной проверки подлинности на основе сертификата в службе управления API Azure." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Защита фоновых служб посредством взаимной проверки подлинности с помощью сертификата в службе Azure API Management

Служба API Management дает возможность защищать доступ к фоновым службам API путем взаимной проверки подлинности с помощью сертификата. В этом руководстве описано, как управлять сертификатами в консоли API Management и как настроить использование сертификата для доступа из API к фоновой службе.

> Дополнительные сведения об управлении сертификатами с помощью API REST API Management см. в разделе [Объект сертификата API REST Azure API Management][Объект сертификата API REST Azure API Management].

## Содержание раздела

- [Предварительные требования][Предварительные требования]
- [Добавление сертификата клиента][Добавление сертификата клиента]
- [Удаление сертификата клиента][Удаление сертификата клиента]
- [Настройка API для проверки подлинности прокси с помощью взаимного сертификата][Настройка API для проверки подлинности прокси с помощью взаимного сертификата]

## <a name="prerequisites"></a> Предварительные требования

В этом руководстве описано, как настроить для экземпляра службы API Management взаимную проверку подлинности с помощью сертификата при доступе из API к фоновой службе. Перед выполнением инструкций, приведенных в этом разделе, вам необходимо настроить фоновую службу для взаимной проверки подлинности с помощью сертификата. Кроме того, нужно иметь доступ к сертификату, который следует добавить в консоль API Management, и его паролю.

## <a name="step1"></a> Добавление сертификата клиента

Для начала щелкните **Консоль управления** на портале Azure службы API Management. Открывается административный портал API Management.

![Консоль API Management][api-management-management-console]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][Создание экземпляра службы API Management] в руководстве [Начинаем работу с API Management][Начинаем работу с API Management].

В меню **API Management** (Управление API) слева выберите пункт **Security** (Безопасность), а затем перейдите на вкладку **Client certificates** (Сертификаты клиента).

![Сертификаты клиента][api-management-security-client-certificates]

Чтобы добавить новый сертификат, щелкните ссылку **Upload certificate** (Загрузить сертификат).

![Добавление сертификата][api-management-upload-certificate]

Выберите сертификат и введите пароль к нему.

>Сертификат должен быть в формате **PFX**. Разрешено использовать самозаверяющие сертификаты.

![Добавление сертификата][api-management-upload-certificate-form]

Нажмите кнопку **Upload** (Загрузить).

>После этого будет проверен пароль к сертификату. Если он неправильный, появится сообщение об ошибке.

![Сертификат добавлен][api-management-certificate-uploaded]

После добавления сертификат появляется на вкладке **Client certificates** (Сертификаты клиента). Если у вас несколько сертификатов, запишите субъект или последние четыре символа отпечатка. Эти данные используются для выбора сертификата при настройке API для использования сертификатов, как описано в следующем разделе [Настройка API для проверки подлинности прокси с помощью взаимного сертификата][Настройка API для проверки подлинности прокси с помощью взаимного сертификата].

## <a name="step1a"></a> Удаление сертификата клиента

Чтобы удалить сертификат, щелкните рядом с ним ссылку **Delete** (Удалить).

![Удаление сертификата][api-management-certificate-delete]

Чтобы подтвердить действие, нажмите кнопку **Yes, delete it** (Да, удалить).

![Подтверждение удаления][api-management-confirm-delete]

Если сертификат используется интерфейсом API, появляется предупреждение. Перед удалением сертификата нужно сначала удалить его из всех API, которые настроены на его использование.

![Подтверждение удаления][api-management-confirm-delete-policy]

## <a name="step2"></a> Настройка API для проверки подлинности прокси с помощью взаимного сертификата

В меню **API Management** (Управление API) слева выберите пункт **APIs** (Интерфейсы API), щелкните имя нужного API и перейдите на вкладку **Security** (Безопасность).

![Безопасность API][api-management-api-security]

В раскрывающемся списке **With credentials** (С учетными данными) выберите пункт **Mutual certificates** (Взаимные сертификаты).

![Взаимные сертификаты][api-management-mutual-certificates]

В раскрывающемся списке **Client certificate** (Сертификат клиента) выберите нужный сертификат. Если сертификатов несколько, то выбрать нужный можно по субъекту или последним четырем символам отпечатка, которые вы записали ранее.

![Выбор сертификата][api-management-select-certificate]

Чтобы сохранить изменение конфигурации в API, нажмите кнопку **Save** (Сохранить).

> Изменение вступает в силу немедленно, и для проверки подлинности на фоновом сервере при вызове операций этого API теперь будет использоваться сертификат.

![Сохранение изменений в API][api-management-save-api]

> Если сертификат настроен для проверки подлинности прокси при доступе из API к фоновой службе, он становится частью политики для этого API и его можно увидеть в редакторе политики.

![Политика сертификата][api-management-certificate-policy]


[Объект сертификата API REST Azure API Management]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[Предварительные требования]: #prerequisites
[Добавление сертификата клиента]: #step1
[Удаление сертификата клиента]: #step1a
[Настройка API для проверки подлинности прокси с помощью взаимного сертификата]: #step2
[Создание экземпляра службы API Management]: ../api-management-get-started/#create-service-instance
[Начинаем работу с API Management]: ../api-management-get-started
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

<!--HONumber=46--> 
