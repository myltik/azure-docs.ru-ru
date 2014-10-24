<properties pageTitle="How to secure back-end services using mutual certificate authentication in Azure API Management" metaKeywords="" description="Learn how to secure back-end services using mutual certificate authentication in Azure API Management." metaCanonical="" services="api-management" documentationCenter="API Management" title="How to secure back-end services using mutual certificate authentication in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

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

![Консоль API Management][Консоль API Management]

>Если экземпляр службы API Management еще не создан, см. раздел [Создание экземпляра службы API Management][Создание экземпляра службы API Management] в руководстве [Начинаем работу с API Management][Начинаем работу с API Management].

В меню **API Management** (Управление API) слева выберите пункт **Security** (Безопасность), а затем перейдите на вкладку **Client certificates** (Сертификаты клиента).

![Сертификаты клиента][Сертификаты клиента]

Чтобы добавить новый сертификат, щелкните ссылку **Upload certificate** (Загрузить сертификат).

![Добавление сертификата][Добавление сертификата]

Выберите сертификат и введите пароль к нему.

>Сертификат должен быть в формате **PFX**. Разрешено использовать самозаверяющие сертификаты.

![Добавление сертификата][1]

Нажмите кнопку **Upload** (Загрузить).

>После этого будет проверен пароль к сертификату. Если он неправильный, появится сообщение об ошибке.

![Сертификат добавлен][Сертификат добавлен]

После добавления сертификат появляется на вкладке **Client certificates** (Сертификаты клиента). Если у вас несколько сертификатов, запишите субъект или последние четыре символа отпечатка. Эти данные используются для выбора сертификата при настройке API для использования сертификатов, как описано в следующем разделе [Настройка API для проверки подлинности прокси с помощью взаимного сертификата][Настройка API для проверки подлинности прокси с помощью взаимного сертификата].

## <a name="step1a"></a> Удаление сертификата клиента

Чтобы удалить сертификат, щелкните рядом с ним ссылку **Delete** (Удалить).

![Удаление сертификата][Удаление сертификата]

Чтобы подтвердить действие, нажмите кнопку **Yes, delete it** (Да, удалить).

![Подтверждение удаления][Подтверждение удаления]

Если сертификат используется интерфейсом API, появляется предупреждение. Перед удалением сертификата нужно сначала удалить его из всех API, которые настроены на его использование.

![Подтверждение удаления][2]

## <a name="step2"></a> Настройка API для проверки подлинности прокси с помощью взаимного сертификата

В меню **API Management** (Управление API) слева выберите пункт **APIs** (Интерфейсы API), щелкните имя нужного API и перейдите на вкладку **Security** (Безопасность).

![Безопасность API][Безопасность API]

В раскрывающемся списке **With credentials** (С учетными данными) выберите пункт **Mutual certificates** (Взаимные сертификаты).

![Взаимные сертификаты][Взаимные сертификаты]

В раскрывающемся списке **Client certificate** (Сертификат клиента) выберите нужный сертификат. Если сертификатов несколько, то выбрать нужный можно по субъекту или последним четырем символам отпечатка, которые вы записали ранее.

![Выбор сертификата][Выбор сертификата]

Чтобы сохранить изменение конфигурации в API, нажмите кнопку **Save** (Сохранить).

> Изменение вступает в силу немедленно, и для проверки подлинности на фоновом сервере при вызове операций этого API теперь будет использоваться сертификат.

![Сохранение изменений в API][Сохранение изменений в API]

> Если сертификат настроен для проверки подлинности прокси при доступе из API к фоновой службе, он становится частью политики для этого API и его можно увидеть в редакторе политики.

![Политика сертификата][Политика сертификата]


[Объект сертификата API REST Azure API Management]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[Предварительные требования]: #prerequisites
[Добавление сертификата клиента]: #step1
[Удаление сертификата клиента]: #step1a
[Настройка API для проверки подлинности прокси с помощью взаимного сертификата]: #step2
[Консоль API Management]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[Создание экземпляра службы API Management]: ../api-management-get-started/#create-service-instance
[Начинаем работу с API Management]: ../api-management-get-started
[Сертификаты клиента]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[Добавление сертификата]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[1]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[Сертификат добавлен]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[Удаление сертификата]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[Подтверждение удаления]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[2]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png
[Безопасность API]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[Взаимные сертификаты]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[Выбор сертификата]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[Сохранение изменений в API]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[Политика сертификата]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
