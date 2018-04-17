---
title: Журналы диагностики Azure | Документация Майкрософт
description: Клиент может включить анализ журналов для Azure CDN.
services: cdn
documentationcenter: ''
author: ''
manager: ''
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: v-deasim
ms.openlocfilehash: f9711f9cfaab1ef22da220a773689c95b1103970
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
---
# <a name="azure-diagnostic-logs"></a>Журналы диагностики Azure

Журналы диагностики Azure позволяют просматривать данные основной аналитики и сохранять их в одно или несколько из следующих целевых расположений:

 - Учетная запись хранения Azure
 - Концентраторы событий Azure
 - [репозиторий OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).
 
Эта функция доступна для всех конечных точек CDN, относящихся к профилям CDN Verizon (уровень "Стандартный" или "Премиум") и Akamai (уровень "Стандартный"). 

Журналы диагностики Azure позволяют экспортировать основные метрики использования из конечной точки CDN в различные источники и настраивать их для дальнейшего использования. Например, вам доступны следующие типы экспорта данных:

- экспорт данных в хранилище BLOB-объектов, экспорт в CSV-файл и создание диаграмм в Excel;
- экспорт данных в концентраторы событий и их сопоставление с данными из других служб Azure;
- экспорт данных в Log Analytics и их просмотр в собственной рабочей области OMS.

На изображении ниже показано типичное представление данных основной аналитики CDN.

![Портал — журналы диагностики](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Рисунок 1. Представление основной аналитики CDN*

Дополнительные сведения см. в статье [о журналах диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="enable-logging-with-azure-portal"></a>Включение ведения журнала с помощью портала Azure

Выполните инструкции ниже, чтобы включить ведение журнала для основной аналитики CDN:

Войдите на [портале Azure](http://portal.azure.com). Если в вашем рабочем процессе еще не включена служба CDN, [включите Azure CDN](cdn-create-new-endpoint.md), прежде чем продолжить.

1. На портале Azure перейдите к **профилю CDN**.
2. Выберите профиль CDN, а затем конечную точку CDN, для которой нужно включить **журналы диагностики**.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. В разделе **Мониторинг** выберите **Журналы диагностики**.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Включение ведения журнала с помощью службы хранилища Azure
    
1. Чтобы использовать службу хранилища Azure для хранения журналов, установите флажок **Архивировать в учетной записи хранения**, выберите **CoreAnalytics** и установите количество дней хранения в параметре **Хранение (дни)**. Нулевое значение означает, что журналы будут храниться неограниченно долго. 
2. Введите имя параметра и щелкните **Учетная запись хранения**. Выберите нужную учетную запись хранения и щелкните **Сохранить**.

![Портал — журналы диагностики](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Рисунок 2. Ведение журнала с помощью службы хранилища Azure*

### <a name="logging-with-oms-log-analytics"></a>Ведение журнала с помощью OMS Log Analytics

Чтобы использовать OMS Log Analytics для хранения журналов, сделайте следующее:

1. В колонке **Журналы диагностики** установите флажок **Отправить в Log Analytics**. 

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Щелкните **Настройка**, чтобы настроить ведение журналов Log Analytics. Откроется диалоговое окно "Рабочие области OMS", где можно выбрать существующую рабочую область или создать новую.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Щелкните **Create New Workspace** (Создание рабочей области).

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/07_Create-new.png)

4. Введите имя рабочей области OMS. Имя рабочей области OMS должно быть уникальным и может содержать только буквы, цифры и дефисы; не допускаются пробелы и знаки подчеркивания. 
5. Далее выберите существующую подписку, группу ресурсов (новую или существующую), расположение и ценовую категорию. При желании вы можете закрепить эту конфигурацию на панели мониторинга. Нажмите кнопку **ОК**, чтобы завершить настройку.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5.  После создания рабочей области вы вернетесь к окнам журналов диагностики. Проверьте и подтвердите имя рабочей области Log Analytics.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    После настройки конфигурации Log Analytics убедитесь, что выбран элемент **CoreAnalytics**.

6. Выберите команду **Сохранить**.

7. Чтобы просмотреть новую рабочую область OMS, перейдите к панели мониторинга на портале Azure и щелкните имя рабочей области Log Analytics. Щелкните плитку портала OMS, чтобы открыть рабочую область в репозитории OMS. 

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Репозиторий OMS готов для регистрации данных в журнале. Чтобы воспользоваться этими данными, необходимо использовать [решение OMS](#consuming-oms-log-analytics-data), описываемое далее в этой статье.

Дополнительные сведения о задержках данных журналов вы найдете [здесь](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Включение ведения журнала с помощью PowerShell

Используйте командлеты Azure PowerShell из примера ниже, чтобы включить журналы диагностики.

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Включение журналов диагностики в учетной записи хранения

Сначала войдите в систему и выберите подписку:

    Login-AzureRmAccount 

    Select-AzureSubscription -SubscriptionId 


Чтобы включить журналы диагностики в учетной записи хранения, используйте следующую команду:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```
Чтобы включить журналы диагностики в рабочей области OMS, используйте следующую команду:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
```



## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Использование журналов диагностики из службы хранилища Azure
В этом разделе описана схема данных основной аналитики CDN и структура их хранения в учетной записи хранения Azure, а также приведены примеры кода для скачивания журналов в формате CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Использование обозревателя хранилищ Microsoft Azure
Прежде чем обращаться к данным основной аналитики из учетной записи хранения Azure, сначала необходимо установить средство для доступа к содержимому в учетной записи хранения. На рынке доступно несколько таких средств, но мы рекомендуем использовать обозреватель хранилищ Microsoft Azure. Вы можете скачать это средство [на странице обозревателя хранилищ Azure](http://storageexplorer.com/). После скачивания и установки программы настройте в ней использование той же учетной записи хранения Azure, которая была настроена в качестве назначения для журналов диагностики CDN.

1.  Откройте **обозреватель хранилищ Microsoft Azure**.
2.  Найдите учетную запись хранения.
3.  Перейдите к узлу **Контейнеры Blob-объектов** в учетной записи хранения и разверните его.
4.  Выберите контейнер с именем **insights-logs-coreanalytics** и дважды щелкните его.
5.  Результаты отобразятся на панели справа, начиная с первого уровня, который обозначен как **resourceId=**. Продолжайте щелкать, пока не отобразится файл **PT1H.json**. Ознакомьтесь с примечанием ниже, в котором описан путь.
6.  Каждый файл **PT1H.json** в BLOB-объекте представляет журналы аналитики за один час для конкретной конечной точки CDN или соответствующего личного домена.
7.  Схема содержимого этого JSON-файла описана в разделе "Схемы журналов основной аналитики".


> [!NOTE]
> **Формат пути к BLOB-объекту**
> 
> Журналы основной аналитики создаются каждый час, собираются в один BLOB-объект и хранятся в нем в формате полезных данных JSON. Поскольку средство обозревателя хранилищ воспринимает символ "/" как разделитель каталогов и отслеживает иерархию, путь к BLOB-объекту Azure отображается как иерархическая структура, сформированная из его имени. Имя BLOB-объекта соответствует описанному ниже соглашению об именовании. 
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Описание полей**

|value|description|
|-------|---------|
|Идентификатор подписки    |Идентификатор подписки Azure в формате GUID.|
|Ресурс |Имя группы ресурсов, к которой относятся ресурсы CDN.|
|Имя профиля |Имя профиля CDN.|
|Имя конечной точки |Имя конечной точки CDN.|
|Год|  4-значное обозначение года, например 2017.|
|Месяц| 2-значное обозначение номера месяца. 01 — январь, 12 — декабрь.|
|День|   2-значное обозначение дня месяца.|
|PT1H.json| Фактический JSON-файл, в котором хранятся данные аналитики.|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Экспорт данных основной аналитики в CSV-файл

Чтобы упростить вам работу, мы предлагаем пример кода для средства работы с основной аналитикой. Это средство выполняет скачивание JSON-файлов с преобразованием в неструктурированные файлы с разделителями-запятыми, на основе которых очень удобно создавать диаграммы или вычислять статистику.

Вот как использовать это средство:

1.  Перейдите на GitHub по ссылке [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ).
2.  Скачайте код.
3.  Следуйте инструкциям по компиляции и настройке.
4.  Запустите средство.
5.  Полученный CSV-файл позволяет просмотреть данные аналитики в простой одноуровневой структуре.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Использование журналов диагностики из репозитория OMS Log Analytics
Log Analytics — это служба в Operations Management Suite (OMS), которая отслеживает облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников. 

Для использования Log Analytics нужно [включить ведение журнала](#enable-logging-with-azure-storage) в репозитории Azure OMS Log Analytics, который рассматривается выше в этой статье.

### <a name="using-the-oms-repository"></a>Использование репозитория OMS

 На следующей схеме показана архитектура входов и выходов репозитория:

![Репозиторий OMS Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Рисунок 3. Репозиторий OMS Log Analytics*.

Решения по управлению позволяют отобразить данные различными способами. Решения по управлению можно получить из [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Эти решения для управления можно установить из Azure Мarketplace, щелкнув ссылку для **скачивания** в нижней части каждого решения.

### <a name="adding-an-oms-cdn-management-solution"></a>Добавление решения по управлению OMS CDN

Чтобы добавить решение по управлению, сделайте следующее:

1.   Войдите на портал Azure, используя подписку Azure, если вы еще этого не сделали, и перейдите на панель мониторинга.
    ![Панель мониторинга Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. В колонке **Создать** выберите **Marketplace** > **Мониторинг и управление**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. В колонке **Мониторинг и управление** щелкните **See all** (Просмотреть все).

    ![Смотреть все](./media/cdn-diagnostics-log/15_See-all.png)

4.  Введите CDN в поле поиска.

    ![Смотреть все](./media/cdn-diagnostics-log/16_Search-for.png)

5.  Выберите **Azure CDN Core Analytics**. 

    ![Смотреть все](./media/cdn-diagnostics-log/17_Core-analytics.png)

6.  После того, как вы щелкнете **Создать**, вам будет предложено создать рабочую область OMS или использовать имеющуюся. 

    ![Смотреть все](./media/cdn-diagnostics-log/18_Adding-solution.png)

7.  Выберите созданную ранее рабочую область. Затем необходимо добавить учетную запись автоматизации.

    ![Смотреть все](./media/cdn-diagnostics-log/19_Add-automation.png)

8. На следующем снимке экрана показана форма автоматизации, которую необходимо заполнить. 

    ![Смотреть все](./media/cdn-diagnostics-log/20_Automation.png)

9. Создав учетную запись автоматизации, вы можете добавить решение. Нажмите кнопку **Создать** .

    ![Смотреть все](./media/cdn-diagnostics-log/21_Ready.png)

10. Решение добавлено в рабочую область. Вернитесь к панели мониторинга на портале Azure.

    ![Смотреть все](./media/cdn-diagnostics-log/22_Dashboard.png)

    Щелкните созданную рабочую область Log Analytics, чтобы перейти к ней. 

11. Щелкните плитку **OMS Portal** (Портал OMS), чтобы новое решение отобразилось на портале OMS.

    ![Смотреть все](./media/cdn-diagnostics-log/23_workspace.png)

12. Портал OMS должен выглядеть, как на снимке экрана ниже:

    ![Смотреть все](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Щелкните одну из плиток, чтобы увидеть несколько представлений данных.

    ![Смотреть все](./media/cdn-diagnostics-log/25_Interior-view.png)

    Представления можно прокрутить влево или вправо, чтобы увидеть дополнительные плитки, показывающие отдельные представления данных. 

    Щелкнув одну из плиток, вы получите дополнительные сведения о данных.

     ![Смотреть все](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Предложения и ценовые категории

Предложения и ценовые категории для решений по управлению OMS см. [здесь](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Настройка представлений

Представление о данных можно настроить с помощью **конструктора представлений**. Чтобы начать разработку, перейдите к рабочей области OMS и щелкните плитку **Конструктор представлений**.

![«Просмотреть конструктор»](./media/cdn-diagnostics-log/27_Designer.png)

Здесь вы можете перетаскивать типы диаграмм и вносить сведения о данных для анализа.

![«Просмотреть конструктор»](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Задержка данных журнала

Задержка данных журнала Verizon | Задержка данных журнала Akamai
--- | ---
Задержка данных журнала Verizon составляет 1 час. От момента распространения конечной точки до начала отображения данных может пройти до 2 часов. | Задержка данных для журнала Akamai составляет 24 часа. Данные, созданные более 24 часов назад, начнут отображаться с задержкой до 2 часов. Если данные созданы недавно, задержка отображения журналов может составить до 25 часов.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Типы журналов диагностики для основной аналитики CDN

В настоящее время все предлагаемые журналы основной аналитики содержат только метрики для статистики HTTP-ответов и исходящих данных, зафиксированные в точках присутствия и (или) границах CDN.

### <a name="core-analytics-metrics-details"></a>Сведения о метриках основной аналитики
В таблице ниже приведен список метрик, доступных в журналах основной аналитики. Не все метрики доступны у всех поставщиков, хотя различия минимальны. В следующей таблице помимо прочего указано, доступна ли та или иная метрика у определенного поставщика. Обратите внимание, что метрики доступны только для конечных точек CDN, на которых зарегистрирован трафик.


|Метрика                     | ОПИСАНИЕ   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Общее количество запросов за этот период.| Yes  |Yes   |
| RequestCountHttpStatus2xx |Количество запросов, на которые возвращены ответы с кодами HTTP 2xx (200, 202 и т. д.).              | Yes  |Yes   |
| RequestCountHttpStatus3xx | Количество запросов, на которые возвращены ответы с кодами HTTP 3xx (300, 302 и т. д.).              | Yes  |Yes   |
| RequestCountHttpStatus4xx |Количество запросов, на которые возвращены ответы с кодами HTTP 4xx (400, 404 и т. д.).               | Yes   |Yes   |
| RequestCountHttpStatus5xx | Количество запросов, на которые возвращены ответы с кодами HTTP 5xx (500, 504 и т. д.).              | Yes  |Yes   |
| RequestCountHttpStatusOthers |  Количество всех остальных кодов HTTP (кроме 2xx–5xx). | Yes  |Yes   |
| RequestCountHttpStatus200 | Количество всех запросов, в результате которых вернулся код HTTP 200.              |Нет    |Yes   |
| RequestCountHttpStatus206 | Количество всех запросов, в результате которых вернулся код HTTP 206.              |Нет    |Yes   |
| RequestCountHttpStatus302 | Количество всех запросов, в результате которых вернулся код HTTP 302.              |Нет    |Yes   |
| RequestCountHttpStatus304 |  Количество всех запросов, в результате которых вернулся код HTTP 304.             |Нет    |Yes   |
| RequestCountHttpStatus404 | Количество всех запросов, в результате которых вернулся код HTTP 404.              |Нет    |Yes   |
| RequestCountCacheHit |Количество всех запросов, в результате которых произошло попадание в кэш. Такие ресурсы возвращаются клиенту непосредственно из точки присутствия.               | Yes  |Нет    |
| RequestCountCacheMiss | Количество всех запросов, в результате которых произошел промах кэша. Это означает, что ресурс не найден в ближайшей к клиенту точке присутствия и поэтому был получен из источника.              |Yes   | Нет   |
| RequestCountCacheNoCache | Количество всех запросов к ресурсу, кэширование которых запрещено из-за конфигурации пользователя на границе.              |Yes   | Нет   |
| RequestCountCacheUncacheable | Количество всех запросов к ресурсам, кэширование которых запрещено заголовками Cache-Control и Expires, указывающими, что ресурс не должен кэшироваться в точке присутствия или с помощью клиента HTTP.                |Yes   |Нет    |
| RequestCountCacheOthers | Количество всех запросов с состоянием кэша, не указанным выше.              |Yes   | Нет   |
| EgressTotal | Объем передачи исходящих данных в ГБ.              |Yes   |Yes   |
| EgressHttpStatus2xx | Объем передачи исходящих данных* для ответов с кодами состояния HTTP 2xx в ГБ.            |Yes   |Нет    |
| EgressHttpStatus3xx | Объем передачи исходящих данных для ответов с кодами состояния HTTP 3xx в ГБ.              |Yes   |Нет    |
| EgressHttpStatus4xx | Объем передачи исходящих данных для ответов с кодами состояния HTTP 4xx в ГБ.               |Yes   | Нет   |
| EgressHttpStatus5xx | Объем передачи исходящих данных для ответов с кодами состояния HTTP 5xx в ГБ.               |Yes   |  Нет  |
| EgressHttpStatusOthers | Объем передачи исходящих данных для ответов с другими кодами состояния HTTP в ГБ.                |Yes   |Нет    |
| EgressCacheHit |  Объем передачи исходящих данных для ответов, которые были доставлены непосредственно из кэша CDN на точки присутствия и границы CDN.  |Yes   |  Нет  |
| EgressCacheMiss | Объем передачи исходящих данных для ответов, не найденных на ближайшем POP-сервере и полученных с сервера-источника.              |Yes   |  Нет  |
| EgressCacheNoCache | Объем передачи исходящих данных для ресурсов, кэширование которых запрещено из-за конфигурации пользователя на границе.                |Yes   |Нет    |
| EgressCacheUncacheable | Объем передачи исходящих данных для ресурсов, кэширование которых запрещено заголовками Cache-Control и (или) Expires. Эти заголовки указывают, что ресурс не должен кэшироваться в точке присутствия или в HTTP-клиенте.                   |Yes   | Нет   |
| EgressCacheOthers |  Объем передачи исходящих данных для других сценариев с использованием кэша.             |Yes   | Нет   |

*Передача исходящих данных — это трафик, который доставляется с POP-серверов CDN клиенту.


### <a name="schema-of-the-core-analytics-logs"></a>Схема журналов основной аналитики 

Все журналы хранятся в формате JSON, и каждая запись содержит строковые поля, как показано на схеме ниже.

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Здесь time представляет начало периода, для которого выводятся статистические данные. Если метрика не поддерживается поставщиком CDN, вместо значения типа double или integer ей присваивается значение NULL. Это значение указывает на отсутствие метрики, то есть по смыслу отличается от значения 0. Каждому домену, настроенному на конечной точке, соответствует один набор этих метрик.

Пример набора свойств:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Сбор и использование диагностических данных из ресурсов Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Анализ вариантов использования CDN Azure](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Что такое Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Log Analytics REST API Reference](https://docs.microsoft.com/rest/api/loganalytics) (Справочник по REST API Log Analytics)







