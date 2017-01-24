---
title: "Как реализовать аварийное восстановление с помощью функций резервного копирования и восстановления служб в управлении API Azure | Документация Майкрософт"
description: "Использование архивации и восстановления для выполнения аварийного восстановления в службе управления API Azure."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f10be3c-f796-4a6c-bacd-7931b6aa82af
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: a7ff82a47b4e972db96929acb47fcce760b244b3
ms.openlocfilehash: 73bb12643a5c94e364ac4040f6e1678cb1495fb2


---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Реализация аварийного восстановления с помощью функций резервного копирования и восстановления службы в Azure API Management
Выбрав службу Azure API Management для публикации интерфейсов API и управления ими, вы получаете множество возможностей по обеспечению отказоустойчивости и организации инфраструктуры, которые в противном случае вам пришлось бы проектировать и внедрять самостоятельно. Платформа Azure устраняет большую часть потенциальных сбоев при относительно небольших затратах.

Чтобы обеспечить восстановление в случае проблем с доступностью в регионе, в котором размещена ваша служба API Management, необходимо быть готовым к ее воссозданию в другом регионе в любой момент. В зависимости от целевых показателей доступности и времени восстановления вам, возможно, потребуется создать резервную службу в одном или нескольких регионах, а затем наладить постоянную синхронизацию ее конфигурации и содержимого с активной службой. Функция резервного копирования и восстановления службы служит необходимым фундаментом для реализации стратегии аварийного восстановления.

В этом руководстве показано, как проверять подлинность запросов к диспетчеру ресурсов Azure, а также создавать резервные копии экземпляров служб управления API и восстанавливать их.

> [!NOTE]
> Процесс резервного копирования и аварийного восстановления экземпляра службы управления API может использоваться для репликации экземпляров службы управления API, например для реализации промежуточного хранения.
>
> Обратите внимание, что срок действия каждой резервной копии истекает через 7 дней. При попытке восстановления резервной копии по истечении 7 дней восстановление завершится сбоем и будет отображено сообщение `Cannot restore: backup expired` .
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Проверка подлинности запросов к диспетчеру ресурсов Azure
> [!IMPORTANT]
> Интерфейс REST API для резервного копирования и восстановления использует диспетчер ресурсов Azure и применяет механизм проверки подлинности, отличный от интерфейсов REST API, используемых для управления объектами службы управления API. В этом разделе описываются действия, необходимые для проверки подлинности запросов к диспетчеру ресурсов Azure. Дополнительные сведения см. в [справочнике REST API Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Все задачи, выполняемые с ресурсами с помощью диспетчера ресурсов Azure, должны пройти проверку подлинности Azure Active Directory. Для этого:

* добавьте приложение в клиент Azure Active Directory;
* настройте разрешения для добавленного приложения;
* получите маркер для проверки подлинности запросов к диспетчеру ресурсов Azure.

В первую очередь необходимо создать приложение Azure Active Directory. Войдите на [классический портал Azure](http://manage.windowsazure.com/) , используя подписку, включающую экземпляр службы управления API, и перейдите на вкладку **Приложения** , чтобы открыть используемый по умолчанию каталог Azure Active Directory.

> [!NOTE]
> Если этот каталог не отображается в вашей учетной записи, обратитесь к администратору подписки Azure, чтобы получить необходимые разрешения для учетной записи. Сведения о расположении каталога по умолчанию см. в разделе "Найдите каталог по умолчанию на классическом портале Azure." статьи [Создание рабочего или учебного удостоверения в Azure Active Directory для использования с виртуальными машинами Windows](../virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

![Создание приложения Azure Active Directory][api-management-add-aad-application]

Щелкните **Добавить**, выберите **Добавить приложение, разрабатываемое моей организацией**, а затем — **Собственное клиентское приложение**. Введите описательное имя и нажмите кнопку «Далее». Введите в поле **URI перенаправления** любой URL-адрес, например `http://resources`. Это поле является обязательным, но введенное значение впоследствии не используется. Установите флажок, чтобы сохранить приложение.

После того как приложение будет сохранено, щелкните **Настройка**, прокрутите страницу вниз до раздела **Разрешения для других приложений** и нажмите кнопку **Добавить приложение**.

![Добавление разрешений][api-management-aad-permissions-add]

Выберите **API управления службами **Microsoft** Azure** и установите флажок, чтобы добавить приложение.

![Добавление разрешений][api-management-aad-permissions]

Щелкните **Делегированные разрешения** рядом с только что добавленным приложением **API управления службами **Microsoft** Azure**, установите флажок **Доступ к управлению службами Azure (предварительная версия)** и нажмите кнопку **Сохранить**.

![Добавление разрешений][api-management-aad-delegated-permissions]

Прежде чем вызывать API, выполняющие резервное копирование и восстановление, необходимо получить маркер. В следующем примере для получения маркера используется пакет NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) .

```c#
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Замените `{tentand id}`, `{application id}` и `{redirect uri}`, следуя приведенным инструкциям.

Замените `{tenant id}` на идентификатор клиента для приложения Azure Active Directory, которое вы только что создали. Чтобы получить доступ к идентификатору, щелкните **Просмотр конечных точек**.

![Endpoints][api-management-aad-default-directory]

![Endpoints][api-management-endpoint]

Замените `{application id}` и `{redirect uri}`, используя **идентификатор клиента** и URL-адрес из раздела **URI перенаправления** на вкладке **Настройка** приложения Azure Active Directory.

![Ресурсы][api-management-aad-resources]

Когда все значения будут указаны, код должен вернуть примерно такой маркер:

![Маркер][api-management-arm-token]

Прежде чем выполнять операции резервного копирования и восстановления, описанные в следующих разделах, задайте заголовок запроса проверки подлинности для вызова REST.

```c#
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

## <a name="step1"> </a>Архивация службы управления API
Чтобы выполнить архивацию службы управления API, отправьте следующий HTTP-запрос:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Описание:

* `subscriptionId` — идентификатор подписки, включающей в себя службу управления API, резервное копирование которой вы пытаетесь выполнить;
* `resourceGroupName` — строка в формате "Api-Default-{service-region}", где `service-region` — это регион Azure, в котором размещена служба управления API для резервного копирования (например, `North-Central-US`);
* `serviceName` — имя службы управления API, резервное копирование которой вы выполняете, на момент ее создания;
* `api-version` замените `2014-02-14`.

В тексте запроса укажите целевую учетную запись хранения Azure, ключ доступа, имя контейнера BLOB-объектов и имя резервной копии:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Для заголовка запроса `Content-Type` установите значение `application/json`.

Резервное копирование — это длительная операция, которая может занять много минут.  Если запрос выполнен успешно и процесс резервного копирования инициирован, вы получите код состояния ответа `202 Accepted` с заголовком `Location`.  Чтобы отслеживать состояние операции, отправляйте запросы GET на URL-адрес, указанный в заголовке `Location` . Пока резервное копирование выполняется, вы будете получать код состояния "202 Accepted". Код ответа `200 OK` указывает на то, что резервное копирование успешно завершено.

Обратите внимание на то, что к запросу на архивацию применяются приведенные ниже ограничения.

* **Контейнер**, указанный в теле запроса, **должен существовать**.
* Пока выполняется резервное копирование, **не предпринимайте никаких действий по управлению службами**, таких как повышение или понижение уровня SKU, смена доменного имени и т. д.
* Восстановление **резервной копии гарантируется только в течение 7 дней** с момента ее создания.
* **Данные об использовании**, применяемые при создании аналитических отчетов, **не включаются** в резервную копию. Для периодического извлечения аналитических отчетов с целью их дальнейшего помещения на хранение используйте интерфейс [REST API управления API Azure][Azure API Management REST API].
* Частота резервного копирования службы влияет на цель точки восстановления. Чтобы максимально снизить этот показатель, мы советуем настроить регулярное резервное копирование, а также выполнять резервное копирование по требованию после внесения важных изменений в службу API Management.
* **Изменения**, внесенные в конфигурацию службы (например, интерфейсы API, политики, внешний вид портала разработчика) во время резервного копирования, **возможно, не включены в резервную копию. В этом случае они будут утеряны**.

## <a name="step2"> </a>Восстановление службы управления API
Чтобы восстановить службу API Management из ранее созданной резервной копии, отправьте следующий HTTP-запрос:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

где:

* `subscriptionId` — идентификатор подписки, включающей в себя службу управления API, которую нужно восстановить из резервной копии;
* `resourceGroupName` — строка в формате "Api-Default-{service-region}", где `service-region` — это регион Azure, в котором размещена восстанавливаемая из резервной копии служба управления API (например, `North-Central-US`);
* `serviceName` — имя восстанавливаемой службы управления API на момент ее создания;
* `api-version` замените `2014-02-14`.

В тексте запроса укажите расположение файла резервной копии, т. е. учетную запись хранения Azure, ключ доступа, имя контейнера BLOB-объектов и имя резервной копии:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Для заголовка запроса `Content-Type` установите значение `application/json`.

Восстановление — это длительная операция, которая может занять до 30 минут и более.  Если запрос выполнен успешно и процесс восстановления инициирован, вы получите код состояния ответа `202 Accepted` с заголовком `Location`.  Чтобы отслеживать состояние операции, отправляйте запросы GET на URL-адрес, указанный в заголовке `Location` . Пока восстановление выполняется, вы будете получать код состояния "202 Accepted". Код ответа `200 OK` указывает на то, что восстановление успешно завершено.

> [!IMPORTANT]
> **SKU** восстанавливаемой службы **должен соответствовать** SKU службы в резервной копии.
>
> **Изменения**, внесенные в конфигурацию службы (например, интерфейсы API, политики, внешний вид портала разработчика) во время восстановления, **могут быть перезаписаны**.
>
>

## <a name="next-steps"></a>Дальнейшие действия
Чтобы ознакомиться с двумя другими способами резервного копирования и восстановления, прочитайте следующие записи в блогах по решениям Майкрософт.

* [Репликация учетных записей управления API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
  * Благодарим Джизелу за материалы для этой статьи.
* [Управление API Azure: резервное копирование и восстановление конфигурации](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Стюарт подробно описывает альтернативный подход, который не соответствует официальным рекомендациям, но тоже очень интересен.

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png



<!--HONumber=Dec16_HO3-->


