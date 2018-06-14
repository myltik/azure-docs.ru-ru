---
title: Внедрение аварийного восстановления с помощью функций архивации и восстановления в службе управления API Azure | Документация Майкрософт
description: Использование архивации и восстановления для выполнения аварийного восстановления в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: 3fcd2fc4162cfbf549be979e15745934c2e4c6ff
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019285"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Реализация аварийного восстановления с помощью функций резервного копирования и восстановления службы в Azure API Management

Выбрав службу Azure API Management для публикации интерфейсов API и управления ими, вы получаете множество возможностей по обеспечению отказоустойчивости и организации инфраструктуры, которые в противном случае вам пришлось бы проектировать и внедрять самостоятельно. Платформа Azure устраняет большую часть потенциальных сбоев при относительно небольших затратах.

Чтобы обеспечить восстановление в случае проблем с доступностью в регионе, в котором размещена ваша служба управления API, вы должны быть готовы в любой момент создать ее в другом регионе. В зависимости от целевых показателей доступности и времени восстановления вам, возможно, потребуется создать резервную службу в одном или нескольких регионах, а затем наладить постоянную синхронизацию ее конфигурации и содержимого с активной службой. Функция резервного копирования и восстановления службы служит необходимым фундаментом для реализации стратегии аварийного восстановления.

В этом руководстве показано, как проверять подлинность запросов к Azure Resource Manager, и как создавать резервные копии экземпляров служб управления API и восстанавливать их.

> [!NOTE]
> Процесс резервного копирования и аварийного восстановления экземпляра службы управления API может использоваться для репликации экземпляров службы управления API, например для реализации промежуточного хранения.
>
> Срок действия каждой резервной копии истекает через 30 дней. Если вы попытаетесь восстановить резервную копию по истечении 30 дней, восстановление завершится сбоем и будет отображено сообщение `Cannot restore: backup expired`.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Проверка подлинности запросов к диспетчеру ресурсов Azure

> [!IMPORTANT]
> Интерфейс REST API для резервного копирования и восстановления использует диспетчер ресурсов Azure и применяет механизм проверки подлинности, отличный от интерфейсов REST API, используемых для управления объектами службы управления API. В этом разделе описываются действия, необходимые для проверки подлинности запросов к диспетчеру ресурсов Azure. Дополнительные сведения см. в [справочнике REST API Azure](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Все задачи, которые выполняются в ресурсах с помощью Azure Resource Manager, необходимо аутентифицировать в Azure Active Directory. Для этого:

* добавьте приложение в клиент Azure Active Directory;
* настройте разрешения для добавленного приложения;
* получите маркер для проверки подлинности запросов к диспетчеру ресурсов Azure.

### <a name="create-an-azure-active-directory-application"></a>Создание приложения Azure Active Directory

1. Войдите на [портале Azure](https://portal.azure.com). 
2. Войдите в подписку, которая содержит ваш экземпляр службы управления API, и перейдите на вкладку **Регистрация приложений**.

    > [!NOTE]
    > Если этот каталог не отображается в вашей учетной записи, обратитесь к администратору подписки Azure, чтобы получить необходимые разрешения для учетной записи.
3. Щелкните **Регистрация нового приложения**.

    Справа появится окно **Создание**. Здесь вам нужно ввести информацию о приложении AAD.
4. Введите имя приложения.
5. Выберите тип приложения **Машинный код**.
6. Введите в поле **URI перенаправления** любой URL-адрес, например `http://resources`. Это поле является обязательным, но введенное значение впоследствии не используется. Установите флажок, чтобы сохранить приложение.
7. Нажмите кнопку **Создать**.

### <a name="add-an-application"></a>Добавление приложения

1. После создания приложения щелкните **Параметры**.
2. Теперь щелкните **Требуемые разрешения**.
3. Щелкните **+Добавить**.
4. Нажмите кнопку **Выбор API**.
5. Выберите **Windows** **Azure Service Management API** (API управления службами Azure).
6. Нажмите кнопку **Выбрать**. 

    ![Добавление разрешений](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Щелкните **Делегированные разрешения** рядом с только что добавленным приложением и установите флажок **Access Azure Service Management (preview)** (Доступ к управлению службами Azure (предварительная версия)).
8. Нажмите кнопку **Выбрать**.

### <a name="configuring-your-app"></a>Настройка приложения

Прежде чем вызывать API, выполняющие резервное копирование и восстановление, необходимо получить маркер. В следующем примере для получения маркера используется пакет NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory).

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
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

1. Замените `{tenant id}` идентификатором клиента для созданного приложения Azure Active Directory. Чтобы узнать этот идентификатор, выберите **Регистрации приложений** -> **Конечные точки**.

    ![Конечные точки][api-management-endpoint]
2. Замените `{application id}` значением, которое представлено на странице **Параметры**.
3. Замените URL-адрес на вкладке **URI перенаправления** URL-адресом вашего приложения Azure Active Directory.

    Когда все значения будут указаны, этот пример кода должен вернуть примерно такой маркер:

    ![по маркеру][api-management-arm-token]

## <a name="calling-the-backup-and-restore-operations"></a>Вызов операций резервного копирования и восстановления

Прежде чем выполнять операции резервного копирования и восстановления, описанные в следующих разделах, задайте заголовок запроса авторизации для вызова REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Архивация службы управления API
Чтобы выполнить архивацию службы управления API, отправьте следующий HTTP-запрос:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

где:

* `subscriptionId` — идентификатор подписки, содержащей службу управления API, для которой вы собираетесь выполнить резервное копирование;
* `resourceGroupName` — строка в формате "Api-Default-{service-region}", где `service-region` обозначает регион Azure, в котором размещена копируемая служба управления API (например, `North-Central-US`);
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

Резервное копирование — это длительная операция, которая может занять много минут.  Если запрос выполнен успешно и запущен процесс резервного копирования, вы получите код состояния ответа `202 Accepted` с заголовком `Location`.  Чтобы отслеживать состояние операции, отправляйте запросы GET на URL-адрес, указанный в заголовке `Location` . Пока резервное копирование выполняется, вы получаете код состояния "202 Accepted". Код ответа `200 OK` указывает на то, что резервное копирование успешно завершено.

Отправляя запрос на резервное копирование, учитывайте следующие ограничения.

* **Контейнер**, указанный в теле запроса, **должен существовать**.
* Пока выполняется резервное копирование, **не предпринимайте никаких действий по управлению службами**, таких как повышение или понижение уровня SKU, смена доменного имени и т. д.
* Восстановление **резервной копии гарантируется только в течение 30 дней** с момента ее создания.
* **Данные об использовании**, применяемые при создании аналитических отчетов, **не включаются** в резервную копию. Для периодического извлечения аналитических отчетов с целью их дальнейшего помещения на хранение используйте интерфейс [REST API управления API Azure][Azure API Management REST API].
* Частота резервного копирования службы влияет на целевую точку восстановления. Чтобы снизить этот показатель, мы советуем настроить регулярное резервное копирование, а также выполнять резервное копирование по требованию после внесения важных изменений в службу управления API.
* **Изменения**, внесенные в конфигурацию службы (интерфейсы API, политики, внешний вид портала разработчика и т. п.) во время резервного копирования, **могут не попасть в резервную копию, то есть могут быть утеряны**.

### <a name="step2"> </a>Восстановление службы управления API
Чтобы восстановить службу API Management из ранее созданной резервной копии, отправьте следующий HTTP-запрос:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

где:

* `subscriptionId` — идентификатор подписки, включающей в себя службу управления API, которую нужно восстановить из резервной копии;
* `resourceGroupName` — строка в формате "Api-Default-{service-region}", где `service-region` — это регион Azure, в котором размещена восстанавливаемая из резервной копии служба управления API (например, `North-Central-US`);
* `serviceName` — имя восстанавливаемой службы управления API на момент ее создания;
* `api-version` замените `2014-02-14`.

В тексте запроса укажите расположение файла резервной копии, т. е. учетную запись хранения Azure, ключ доступа, имя контейнера BLOB-объектов и имя резервной копии:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Для заголовка запроса `Content-Type` установите значение `application/json`.

Восстановление — это длительная операция, которая может занять до 30 минут и более. Если запрос выполнен успешно и процесс восстановления инициирован, вы получите код состояния ответа `202 Accepted` с заголовком `Location`. Чтобы отслеживать состояние операции, отправляйте запросы GET на URL-адрес, указанный в заголовке `Location` . Пока восстановление выполняется, вы получаете код состояния "202 Accepted". Код ответа `200 OK` указывает на то, что восстановление успешно завершено.

> [!IMPORTANT]
> **Номер SKU** восстанавливаемой службы **должен совпадать** с номером SKU, сохраненным в резервной копии.
>
> **Изменения**, внесенные в конфигурацию службы (например, интерфейсы API, политики, внешний вид портала разработчика) во время восстановления, **могут быть перезаписаны**.
>
>

## <a name="next-steps"></a>Дополнительная информация
Чтобы ознакомиться с двумя другими способами резервного копирования и восстановления, прочитайте следующие записи в блогах по решениям Майкрософт.

* [Репликация учетных записей управления API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Управление API Azure: резервное копирование и восстановление конфигурации](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Стюарт подробно описывает альтернативный подход, который отличается от официальных рекомендаций, но тоже заслуживает внимания.

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
