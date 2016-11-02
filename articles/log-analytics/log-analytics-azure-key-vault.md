<properties
    pageTitle="Решение хранилища ключей Azure в Log Analytics | Microsoft Azure"
    description="Решение хранилища ключей Azure в Log Analytics позволяет просматривать журналы хранилища ключей Azure."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="richrund"/>


# <a name="azure-key-vault-(preview)-solution-in-log-analytics"></a>Решение хранилища ключей Azure (предварительная версия) в Log Analytics

>[AZURE.NOTE] Это [предварительная версия решения](log-analytics-add-solutions.md#log-analytics-preview-solutions-and-features).

Решение хранилища ключей Azure в Log Analytics позволяет просматривать журналы AuditEvent хранилища ключей Azure.

В хранилище ключей Azure можно включить ведение журнала событий аудита. Эти журналы записываются в хранилище BLOB-объектов Azure, где их может проиндексировать Log Analytics для поиска и анализа.

## <a name="install-and-configure-the-solution"></a>Установка и настройка решения

Установите и настройте решение хранилища ключей Azure, выполнив следующие указания:

1.  Включите [ведение журнала диагностики для ресурсов хранилища ключей](../key-vault/key-vault-logging.md), для которых требуется выполнять мониторинг.
2.  Настройте чтение журналов из хранилища BLOB-объектов в Log Analytics, как описано в [JSON-файлах в хранилище BLOB-объектов ](../log-analytics/log-analytics-azure-storage-json.md).
3.  Включите решение хранилища ключей Azure, как описано в статье [Добавление решений Log Analytics из каталога решений](log-analytics-add-solutions.md).  

## <a name="review-azure-key-vault-data-collection-details"></a>Просмотр сведений о сборе данных хранилища ключей Azure

Решение хранилища ключей Azure собирает журналы диагностики из хранилища BLOB-объектов для хранилища ключей Azure.
Для сбора данных агент не требуется.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для хранилища ключей Azure.

| Платформа | Direct Agent | Агент Systems Center Operations Manager (SCOM) | Хранилище Azure | Нужен ли SCOM? | Отправка данных агента SCOM через группу управления | Частота сбора |
|---|---|---|---|---|---|---|
|Таблицы Azure|![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Да](./media/log-analytics-azure-keyvault/oms-bullet-green.png)|            ![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png)|![Нет](./media/log-analytics-azure-keyvault/oms-bullet-red.png)| 10 минут|

## <a name="use-azure-key-vault"></a>Использование хранилища ключей Azure

После установки решения можно просмотреть сводку состояний запросов за определенный период для отслеживаемых хранилищ ключей на плитке **Хранилище ключей Azure** на странице **Обзор** в службе Log Analytics.

![изображение плитки "Хранилище ключей Azure"](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Выбрав плитку **Обзор**, можно просмотреть сводные данные журналов и подробные сведения по следующим категориям:

- количество всех операций хранилища ключей за определенный период;
- количество неудавшихся операций за определенный период;
- среднее время задержки для каждой операции;
- качество обслуживания для операций с количеством операций, выполнявшихся более 1000 мс, и списком этих операций.

![изображение панели мониторинга хранилища ключей Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![изображение панели мониторинга хранилища ключей Azure](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Просмотр сведений об операциях

1. На странице **Обзор** щелкните плитку **Хранилище ключей Azure**.
2. На панели мониторинга **Хранилище ключей Azure** просмотрите сводные данные в одной из колонок, а затем щелкните одну из них, чтобы просмотреть подробные сведения на странице поиска журналов.

    На любой из страниц поиска журналов можно просмотреть результаты по времени, подробные результаты и историю поиска журналов. Для сужения области результатов выполните фильтрацию по аспектам.

## <a name="log-analytics-records"></a>Записи Log Analytics

Решение хранилища ключей Azure анализирует записи типа **KeyVaults**, полученные из [журналов AuditEvent](..\key-vault\key-vault-logging.md) системы диагностики Azure.  Свойства этих записей приведены в таблице ниже.  

| Свойство | Описание |
|:--|:--|
| Тип | *KeyVaults* |
| SourceSystem | *AzureStorage* |
| CallerIpAddress | IP-адрес клиента, отправившего запрос. |
| Категория      | Для журналов хранилища ключей единственным доступным значением является AuditEvent.|
| CorrelationId | Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| DurationMs | Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| HttpStatusCode_d | Код состояния HTTP, возвращаемый запросом. |
| Id_s       | Уникальный идентификатор запроса. |
| Identity_o | Удостоверение из маркера, предоставляемое при выполнении запроса REST API. Обычно это "пользователь", "субъект-служба" или комбинация "пользователь + идентификатор приложения" при запросе с помощью командлета Azure PowerShell. |
| OperationName      | Имя операции, как описано в статье [Ведение журнала хранилища ключей Azure](..\key-vault\key-vault-logging.md)|
| OperationVersion      | Запрошенная клиентом версия REST API.|
| RemoteIPLatitude | Широта клиента, отправившего запрос. |
| RemoteIPLongitude | Долгота клиента, отправившего запрос. |
| RemoteIPCountry | Страна клиента, отправившего запрос.  |
| RequestUri_s | URI запроса |
| Ресурс   | Имя хранилища ключей. |
| ResourceGroup | Группа ресурсов хранилища ключей. |
| ResourceId | Идентификатор ресурса диспетчера ресурсов Azure. Для журналов хранилища ключей это всегда идентификатор ресурса хранилища ключей. |
| ResourceProvider | *MICROSOFT.KEYVAULT* |
| ResultSignature  | Состояние HTTP.|
| ResultType      | Результат запроса REST API.|
| SubscriptionId | Идентификатор подписки Azure, которая содержит хранилище ключей. |


## <a name="next-steps"></a>Дальнейшие действия

- Используйте [поиск по журналам в Log Analytics](log-analytics-log-searches.md) для просмотра подробных данных о хранилище ключей Azure.



<!--HONumber=Oct16_HO2-->


