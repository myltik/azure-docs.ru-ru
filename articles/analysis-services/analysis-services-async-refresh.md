---
title: Асинхронное обновление для моделей Azure Analysis Services | Документация Майкрософт
description: Узнайте, как написать код, выполняющий асинхронное обновление с использованием REST API.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a20e8460e0243e2f6f2f258e26374a2cd716206c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601621"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Асинхронное обновление с помощью REST API
Вы можете задать асинхронное обновление данных в табличных моделях Azure Analysis Services, используя любой язык программирования, с помощью которого можно вызвать REST. Обновление предусматривает также синхронизацию реплик только для чтения для развертывания запросов. 

Обновление данных может занимать некоторое время в зависимости от ряда факторов, в том числе объема данных и уровня оптимизации с использованием секций и т. п. Эти операции обычно вызывают имеющимися методами, например, используя [табличную модель объектов](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo), командлеты [PowerShell](https://docs.microsoft.com/sql/analysis-services/powershell/analysis-services-powershell-reference) или язык [TMSL](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) (Tabular Model Scripting Language). Но для использования этих методов может понадобиться применить ненадежные длительные HTTP-подключения.

REST API для Azure Analysis Services позволяет выполнять обновления данных асинхронно. REST API устраняет необходимость в длительных подключениях через клиентские приложения. Кроме того, здесь предусмотрены другие возможности, обеспечивающие надежность работы, такие как выполнение повторных попыток и пакетных фиксаций.

## <a name="base-url"></a>Базовый URL-адрес

У базового URL-адреса должен быть такой формат:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Например, рассмотрим модель с именем AdventureWorks на сервере myserver, расположенном в регионе Azure "Западная часть США". Тогда у сервера будет такое имя:

```
asazure://westus.asazure.windows.net/myserver 
```

Базовый URL-адрес для сервера с этим именем будет таким:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Базовый URL-адрес можно использовать, чтобы добавлять ресурсы и операции на основе следующих параметров: 

![Асинхронное обновление](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Все, что заканчивается на **s** — коллекции.
- Все, что заканчивается на **()** — функции.
- Все остальное — это ресурсы или объекты.

Например, вы можете выполнить обновление, применив команду POST к коллекции refreshes:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Все вызовы должны проходить проверку подлинности, для чего нужен допустимый токен Azure Active Directory (OAuth 2) в заголовке авторизации, и соответствовать следующим требованиям:

- Токен должен быть пользовательским маркером или субъектом-службой приложения.
- У токена должна быть правильная аудитория, а именно `https://*.asazure.windows.net`.
- У пользователя или приложения должно быть достаточно разрешений для сервера или модели, чтобы выполнять запрашиваемые вызовы. Уровень разрешений определяется по ролям в модели или группе администраторов на сервере.

    > [!IMPORTANT]
    > Сейчас требуются разрешения роли **администратора сервера**.

## <a name="post-refreshes"></a>POST для /refreshes

Выполните обновление, применив команду POST к коллекции /refreshes, чтобы добавить в нее новый элемент. Под заголовком Location в ответе будет указан идентификатор обновления. Клиентское приложение может отключиться и, если требуется, проверить состояние позже, так как обновление происходит асинхронно.

Для одной модели можно выполнить только одну операцию обновления. Если во время одной операции обновления запустить вторую, возвращается конфликт с кодом состояния HTTP 409.

Текст должен выглядеть примерно так:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Параметры
Указывать параметры не обязательно. Применяются значения по умолчанию.

|ИМЯ  |type  |ОПИСАНИЕ  |значение по умолчанию  |
|---------|---------|---------|---------|
|type     |  Перечисление.       |  Тип выполняемой обработки. Тип выполняемой обработки зависит от типа [команды refresh](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/refresh-command-tmsl) TMSL: full, clearValues, calculate, dataOnly, automatic или defragment. Тип add не поддерживается.      |   automatic      |
|CommitMode     |  Перечисление.       |  Определяет, будут объекты зафиксированы в пакетах или только после завершения. Режимы: default, transactional, partialBatch.  |  transactional       |
|MaxParallelism     |   int      |  Это значение определяет максимальное количество потоков, над которыми можно параллельно выполнять команды обработки. Это значение согласуется со свойством MaxParallelism, которое можно задать, используя [команду sequence](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/sequence-command-tmsl) или другими способами.       | 10        |
|RetryCount    |    int     |   Указывает число попыток повторить операцию, по исчерпании которого будет определен сбой.      |     0    |
|Объекты     |   Массив,      |   Массив объектов для обработки. Для каждого объекта указываются параметр table, если нужно обработать целую таблицу, или параметры table и partition для обработки секции. Если нет указанных объектов, обновляется вся модель. |   Обработка целой модели      |

Значение CommitMode — partialBatch. Оно используется при начальной загрузке для больших наборов данных, что может занять несколько часов. Если обновление завершится сбоем после успешной фиксации одного или нескольких пакетов, эти пакеты останутся зафиксированными (т. е. для них не будет выполнен откат).

> [!NOTE]
> На момент написания статьи требуется, чтобы размер пакета был равен значению MaxParallelism, но это могло измениться.

## <a name="get-refreshesrefreshid"></a>GET /refreshes/\<refreshId>

Чтобы проверить состояние обновления, используйте команду GET с идентификатором обновления. Ниже приведен пример текста ответа. Если операция еще выполняется, возвращается состояние **inProgress**.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>GET для /refreshes

Чтобы получить список последних операций обновления для модели, примените команду GET к коллекции /refreshes. Ниже приведен пример текста ответа. 

> [!NOTE]
> На момент написания этой статьи предусматривается, что сохраняются и возвращаются операции обновления за последние 30 дней, но это могло измениться.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>DELETE /refreshes/\<refreshId>

Чтобы отменить выполняющееся обновление, примените команду DELETE к идентификатору обновления.

## <a name="post-sync"></a>POST для /sync

После операций обновления может понадобиться синхронизировать новые данные с репликами для развертывания запросов. Чтобы синхронизировать модель, примените команду POST к функции /sync. Под заголовком Location в ответе будет указан идентификатор операции.

## <a name="get-sync-status"></a>GET для состояния /sync

Чтобы проверить состояние синхронизации, используйте команду GET, передав в качестве параметра идентификатор операции. Вот пример текста ответа:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Значения для `syncstate`:

- 0 — репликация. Файлы базы данных реплицируются в целевую папку.
- 1 — восстановление. База данных восстанавливается на серверных экземплярах только для чтения.
- 2 — завершено. Синхронизация успешно выполнена.
- 3 — сбой операции.
- 4 — финализация. Синхронизация завершена, но еще выполняется очистка.

## <a name="code-sample"></a>Пример кода

Здесь приведен пример кода C# для начала работы: [RestApiSample на GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Как пользоваться примером кода

1.  Клонируйте или скачайте репозиторий. Откройте решение RestApiSample.
2.  Найдите строку **client.BaseAddress = …** и укажите [базовый URL-адрес](#base-url).

В образце кода используются интерактивное имя для входа, имя пользователя и пароль или [субъект-служба](#service-principal).

#### <a name="interactive-login-or-usernamepassword"></a>Интерактивное имя для входа или пользователя

Чтобы использовать проверку подлинности с такими данными, необходимо создать приложение Azure с необходимыми разрешениями API. 

1.  На портале Azure выберите **Создать ресурс** > **Azure Active Directory** > **Регистрация приложений** > **Регистрация нового приложения**.

    ![Регистрация нового приложения](./media/analysis-services-async-refresh/aas-async-app-reg.png)


2.  В разделе **Создать** введите имя и выберите тип приложения **Собственный**. Для параметра **URI перенаправления** введите **urn:ietf:wg:oauth:2.0:oob**, а затем нажмите кнопку **Создать**.

    ![Параметры](./media/analysis-services-async-refresh/aas-async-app-reg-name.png)

3.  Выберите свое приложение, затем скопируйте и сохраните **Код приложения**.

    ![Копирование идентификатора приложения](./media/analysis-services-async-refresh/aas-async-app-id.png)

4.  В разделе **Параметры** выберите **Необходимые разрешения** > **Добавить**.

    ![Добавление параметров доступа через API](./media/analysis-services-async-refresh/aas-async-add.png)

5.  В разделе **Select an API** (Выбор API) введите в поле поиска **Azure Analysis Services**, а затем выберите эту службу.

    ![Выбор API](./media/analysis-services-async-refresh/aas-async-select-api.png)

6.  Установите флажок **Read and Write all Models** (Чтение и запись всех моделей) и нажмите кнопку **Выбрать**. Если установлены оба флажка, нажмите кнопку **Готово**, чтобы добавить разрешения. Распространение параметров может длиться несколько минут.

    ![Выбор разрешения на чтение и запись всех моделей](./media/analysis-services-async-refresh/aas-async-select-read.png)

7.  В примере кода найдите метод **UpdateToken()**. Просмотрите содержимое этого метода.
8.  Найдите фрагмент **string clientID =** , а затем введите значение параметра **Код приложения**, скопированное на шаге 3.
9.  Запустите пример.

#### <a name="service-principal"></a>Субъект-служба

Дополнительные сведения о том, как настроить субъект-службу и назначить необходимые разрешения в Azure AS, см. в статьях [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md) и [Добавление субъекта-службы к роли администратора сервера](analysis-services-addservprinc-admins.md). Выполнив эти шаги, сделайте дополнительно следующее:

1.  В примере кода найдите фрагмент **string authority = …** и замените **стандартный** идентификатор на код клиента своей организации.
2.  Закомментируйте или раскомментируйте код, так чтобы класс ClientCredential использовался для создания экземпляра объекта cred. Убедитесь, что доступ к значениям \<App ID> и \<App Key> осуществляется безопасно, или настройте для субъектов-служб проверку подлинности.
3.  Запустите пример.


## <a name="see-also"></a>См. также

[Примеры](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


