---
title: "Анализ журналов для Azure CDN | Документация Майкрософт"
description: "Клиент может включить анализ журналов для Azure CDN."
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 03ff74ae4e40d3f2279caaf4f73e9b4aac6a2ebb
ms.contentlocale: ru-ru
ms.lasthandoff: 08/08/2017

---

# <a name="diagnostics-logs-for-azure-cdn"></a>Журналы диагностики для Azure CDN

После включения CDN для вашего приложения вам, скорее всего, потребуется отслеживать использование CDN, проверять работоспособность службы доставки и устранять возможные неполадки. Azure CDN предоставляет эти возможности с помощью [CDN Core Analytics](cdn-analyze-usage-patterns.md) и [журналов диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="cdn-core-analytics"></a>CDN Core Analytics
Как текущий пользователь Azure CDN с профилем Verizon уровня "Стандартный" или "Премиум" вы уже можете просматривать данные основной аналитики на дополнительном портале, доступ к которому можно получить с помощью команды "Управление" на портале Azure. 

## <a name="azure-diagnostic-logs"></a>Журналы диагностики Azure

Эта новая функция Azure позволяет просматривать основную аналитику и сохранять ее в одно или несколько целевых расположений:

 - Учетная запись хранения Azure
 - Концентраторы событий Azure
 - [репозиторий OMS Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started).
 
 Эта функция доступна для всех конечных точек CDN, относящихся к профилям CDN Verizon (уровни "Стандартный" и "Премиум") и Akamai (уровень "Стандартный").

Журналы диагностики позволяют экспортировать основные метрики использования из конечной точки CDN в различные источники и настраивать их для дальнейшего использования. Например, вам доступны следующие типы экспорта данных:

- экспорт данных в хранилище BLOB-объектов, экспорт в CSV-файл и создание диаграмм в Excel;
- экспорт данных в концентраторы событий и их сопоставление с данными из других служб Azure;
- экспорт данных в Log Analytics и их просмотр в собственной рабочей области OMS.

На следующем рисунке показано типичное представление CDN Core Analytics с данными.

![Портал — журналы диагностики](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Рисунок 1. Представление CDN Core Analytics*

В следующих пошаговых инструкциях описана схема данных основной аналитики, действия, необходимые для включения этой функции, доставки данных в различные расположения и их использования в этих расположениях.

## <a name="enable-logging-with-azure-portal"></a>Включение ведения журнала с помощью портала Azure

> [!NOTE]
> По умолчанию журналы диагностики **отключены**. 

Выполните следующие действия, чтобы включить ведение журнала с помощью CDN Core Analytics:

Войдите на [портал Azure](http://portal.azure.com). Если в вашем рабочем процессе еще не включена служба CDN, [включите Azure CDN](cdn-create-new-endpoint.md), прежде чем продолжить.

1. На портале Azure перейдите к **профилю CDN**.
2. Выберите профиль CDN, а затем конечную точку CDN, на которой необходимо включить **журналы диагностики**.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Перейдите в колонку **Журналы диагностики** в разделе **Мониторинг** и измените состояние на **Включено**.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Включение ведения журнала с помощью службы хранилища Azure
    
Чтобы использовать службу хранилища Azure для хранения журналов, установите флажок **Archive to a storage account** (Архивировать в учетной записи хранения), выберите количество дней хранения и установите флажок **CoreAnalytics** в разделе **Журнал**.

![Портал — журналы диагностики](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png)

*Рисунок 2. Ведение журнала с помощью службы хранилища Azure*

### <a name="logging-with-oms-log-analytics"></a>Ведение журнала с помощью OMS Log Analytics

Чтобы использовать OMS Log Analytics для хранения журналов, сделайте следующее:

1. В колонке **Журналы диагностики** в разделе **Мониторинг** установите флажок **Send to Log Analytics** (Отправлять в Log Analytics). 

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Настройте ведение журнала Log Analytics, щелкнув "Настроить". Откроется диалоговое окно, где можно выбрать предыдущую рабочую область или создать ее.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Щелкните **Create New Workspace** (Создание рабочей области).

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/07_Create-new.png)

4. Далее необходимо выбрать имя новой рабочей области, имеющуюся подписку, группу ресурсов (новую или имеющуюся), расположение и ценовую категорию. Вы можете закрепить эту конфигурацию на панели мониторинга. Нажмите кнопку "ОК", чтобы завершить настройку.

    Далее для рабочей области отобразятся имена рабочей области OMS и группы ресурсов. Имена должны быть уникальными. В них могут использоваться только буквы, цифры и дефисы. Запрещено использовать пробелы и знаки подчеркивания. 

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/08_Workspace-resource.png)

5. Далее появится короткое сообщение о создании рабочей области, после чего вы вернетесь на экран конфигурации ведения журналов. Вы можете проверить имя рабочей области Log Analytics.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/09_Return-to-logging.png)

    Настроив конфигурацию Log Analytics, также установите флажок CoreAnalytics для ведения журналов CDN.

6. Если все готово, нажмите кнопку **Сохранить** в верхней части диалогового окна настройки.

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/10_Save-me.png)

    Кнопка **Сохранить** стала неактивной и установлен переключатель "ВКЛ.", только теперь он не синего, а фиолетового цвета.

7. Если вы хотите просмотреть новую рабочую область OMS, перейдите на панель мониторинга портала Azure и щелкните имя рабочей области Log Analytics. Далее отобразится рабочая область (убедитесь, что рабочая область OMS выделена слева). Щелкните плитку портала OMS, чтобы увидеть рабочую область в репозитории OMS. 

    ![Портал — журналы диагностики](./media/cdn-diagnostics-log/11_OMS-dashboard.png) 

    Репозиторий OMS готов для регистрации данных в журнале. Чтобы воспользоваться этими данными, необходимо использовать [решение OMS](#consuming-oms-log-analytics-data), описываемое далее в этой статье.

Дополнительные сведения о задержках данных журнала см. [здесь](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Включение ведения журнала с помощью PowerShell

Ниже приведен пример включения и получения журналов диагностики с помощью командлетов Azure PowerShell.

###<a name="enabling-diagnostic-logs-in-a-storage-account"></a>Включение журналов диагностики в учетной записи хранения

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
В этом разделе описываются схемы основной аналитики CDN, их структура в учетной записи хранения Azure и примеры кода для загрузки журналов в CSV-файл.

### <a name="using-microsoft-azure-storage-explorer"></a>Использование обозревателя хранилищ Microsoft Azure
Прежде чем обращаться к данным основной аналитики из учетной записи хранения Azure, сначала необходимо установить средство для доступа к содержимому в учетной записи хранения. На рынке доступно несколько таких средств, но мы рекомендуем использовать обозреватель хранилищ Microsoft Azure. Скачать это средство можно [отсюда](http://storageexplorer.com/). После скачивания и установки программы настройте в ней использование той же учетной записи хранения Azure, которая была настроена в качестве места назначения для журналов диагностики CDN.

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
> Журналы основной аналитики создаются каждый час. Все данные за час собираются и сохраняются в одном большом двоичном объекте Azure в виде полезных данных JSON. Путь к этому большому двоичному объекту Azure отображается в виде иерархической структуры. Это объясняется тем, что обозреватель хранилищ интерпретирует "/" как разделитель каталогов и отображает иерархию для удобства. На самом деле весь путь просто представляет имя Blob-объекта. Это имя соответствует приведенному ниже соглашению об именовании.   
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Описание полей**

|value|description|
|-------|---------|
|Идентификатор подписки    |Идентификатор подписки Azure. в формате GUID.|
|Ресурс |Имя группы ресурсов, к которой относятся ресурсы CDN.|
|Имя профиля |Имя профиля CDN.|
|Имя конечной точки |Имя конечной точки CDN.|
|Год|  4-значное обозначение года, например 2017.|
|Месяц| 2-значное обозначение номера месяца. 01 — январь, 12 — декабрь.|
|День|   2-значное обозначение дня месяца.|
|PT1H.json| Фактический JSON-файл, в котором хранятся данные аналитики.|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Экспорт данных основной аналитики в CSV-файл

Для упрощения доступа к Core Analytics мы предоставляем пример кода для средства, с помощью которого можно скачивать JSON-файлы в формате неструктурированного файла с разделителями-запятыми. Этот файл может использоваться для создания диаграмм или других статистических вычислений.

Вот как использовать это средство:

1.  Перейдите на GitHub по ссылке [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.  Загрузка кода
3.  Следуйте инструкциям по компиляции и настройке.
4.  Запустите средство.
5.  Полученный CSV-файл отображает данные аналитики в простой неструктурированной иерархии.

## <a name="consuming-diagnostics-logs-from-an-oms-log-analytics-repository"></a>Использование журналов диагностики из репозитория OMS Log Analytics
Log Analytics — это служба в Operations Management Suite (OMS), которая отслеживает облачные и локальные среды, чтобы поддерживать уровень их доступности и производительности. Она собирает данные, формируемые ресурсами в облачных и локальных средах, а также другими средствами мониторинга, и на их основе предоставляет аналитические сведения для нескольких источников. 

Для использования Log Analytics нужно [включить ведение журнала](#enable-logging-with-azure-storage) в репозитории Azure OMS Log Analytics, который рассматривается выше в этой статье.

### <a name="using-the-oms-repository"></a>Использование репозитория OMS

 На следующей схеме показана архитектура входов и выходов репозитория:

![Репозиторий OMS Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Рисунок 3. Репозиторий OMS Log Analytics*.

Решения по управлению позволяют отобразить данные различными способами. Решения по управлению можно получить из [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

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

10. Решение добавлено в рабочую область. Вернитесь на панель мониторинга портала Azure.

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

Предложения и ценовые категории для решений по управлению OMS см. [здесь](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers).

### <a name="customizing-views"></a>Настройка представлений

Представление о данных можно настроить с помощью **конструктора представлений**. Перейдите к рабочей области OMS и начните разработку, щелкнув плитку **View Designer** (Конструктор представлений).

![«Просмотреть конструктор»](./media/cdn-diagnostics-log/27_Designer.png)

Слева вы можете перетаскивать типы диаграмм и заполнять сведения о данных, которые необходимо проанализировать.

![«Просмотреть конструктор»](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Задержка данных журнала

Задержка данных журнала Verizon | Задержка данных журнала Akamai
--- | ---
Задержка данных журнала Verizon составляет 1 час. От момента распространения конечной точки до начала отображения данных может пройти до 2 часов. | Задержка данных журнала Akamai составляет 24 часа. До начала отображения данных, созданных более 24 часов назад, проходит до 2 часов. Если данные созданы недавно, задержка отображения журналов может составить до 25 часов.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Типы журналов диагностики для CDN Core Analytics

Сейчас мы предлагаем только журналы Core Analytics, содержащие метрики со статистикой HTTP-ответов и исходящих данных, как видно по точкам присутствия и границам CDN.

### <a name="core-analytics-metrics-details"></a>Сведения о метриках основной аналитики
В следующей таблице приведен список метрик, доступных в журналах Core Analytics. Не все метрики доступны у всех поставщиков, хотя различия минимальны. В следующей таблице помимо прочего указано, доступна ли та или иная метрика у определенного поставщика. Обратите внимание, что метрики доступны только для тех конечных точек CDN, на которых есть трафик.


|Метрика                     | Описание   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Общее количество запросов за этот период.| Да  |Да   |
| RequestCountHttpStatus2xx |Количество всех запросов, в результате которых вернулся код HTTP 2xx (например, 200, 202).              | Да  |Да   |
| RequestCountHttpStatus3xx | Количество всех запросов, в результате которых вернулся код HTTP 3xx (например, 300, 302).              | Да  |Да   |
| RequestCountHttpStatus4xx |Количество всех запросов, в результате которых вернулся код HTTP 4xx (например, 400, 404).               | Да   |Да   |
| RequestCountHttpStatus5xx | Количество всех запросов, в результате которых вернулся код HTTP 5xx (например, 500, 504).              | Да  |Да   |
| RequestCountHttpStatusOthers |  Количество всех остальных кодов HTTP (кроме 2xx–5xx). | Да  |Да   |
| RequestCountHttpStatus200 | Количество всех запросов, в результате которых вернулся код HTTP 200.              |Нет   |Да   |
| RequestCountHttpStatus206 | Количество всех запросов, в результате которых вернулся код HTTP 206.              |Нет   |Да   |
| RequestCountHttpStatus302 | Количество всех запросов, в результате которых вернулся код HTTP 302.              |Нет   |Да   |
| RequestCountHttpStatus304 |  Количество всех запросов, в результате которых вернулся код HTTP 304.             |Нет   |Да   |
| RequestCountHttpStatus404 | Количество всех запросов, в результате которых вернулся код HTTP 404.              |Нет   |Да   |
| RequestCountCacheHit |Количество всех запросов, в результате которых произошло попадание в кэш. Это означает, что ресурс был обработан для клиента непосредственно из точки присутствия.               | Да  |Нет   |
| RequestCountCacheMiss | Количество всех запросов, в результате которых произошел промах кэша. Это означает, что ресурс не найден в ближайшей к клиенту точке присутствия и поэтому был получен из источника.              |Да   | Нет  |
| RequestCountCacheNoCache | Количество всех запросов к ресурсу, кэширование которых запрещено из-за конфигурации пользователя на границе.              |Да   | Нет  |
| RequestCountCacheUncacheable | Количество всех запросов к ресурсам, кэширование которых запрещено заголовками Cache-Control и Expires, указывающими, что ресурс не должен кэшироваться в точке присутствия или с помощью клиента HTTP.                |Да   |Нет   |
| RequestCountCacheOthers | Количество всех запросов с состоянием кэша, не указанным выше.              |Да   | Нет  |
| EgressTotal | Объем передачи исходящих данных в ГБ.              |Да   |Да   |
| EgressHttpStatus2xx | Объем передачи исходящих данных* для ответов с кодами состояния HTTP 2xx в ГБ.            |Да   |Нет   |
| EgressHttpStatus3xx | Объем передачи исходящих данных для ответов с кодами состояния HTTP 3xx в ГБ.              |Да   |Нет   |
| EgressHttpStatus4xx | Объем передачи исходящих данных для ответов с кодами состояния HTTP 4xx в ГБ.               |Да   | Нет  |
| EgressHttpStatus5xx | Объем передачи исходящих данных для ответов с кодами состояния HTTP 5xx в ГБ.               |Да   |  Нет |
| EgressHttpStatusOthers | Объем передачи исходящих данных для ответов с другими кодами состояния HTTP в ГБ.                |Да   |Нет   |
| EgressCacheHit |  Объем передачи исходящих данных для ответов, которые были доставлены непосредственно из кэша CDN на точки присутствия и границы CDN.  |Да   |  Нет |
| EgressCacheMiss | Объем передачи исходящих данных для ответов, не найденных на ближайшем POP-сервере и полученных с сервера-источника.              |Да   |  Нет |
| EgressCacheNoCache | Объем передачи исходящих данных для ресурсов, кэширование которых запрещено из-за конфигурации пользователя на границе.                |Да   |Нет   |
| EgressCacheUncacheable | Объем передачи исходящих данных для ресурсов, кэширование которых запрещено заголовками Cache-Control и (или) Expires, указывающими, что ресурс не должен кэшироваться в точке присутствия или с помощью клиента HTTP.                    |Да   | Нет  |
| EgressCacheOthers |  Объем передачи исходящих данных для других сценариев с использованием кэша.             |Да   | Нет  |

*Передача исходящих данных — это трафик, который доставляется с POP-серверов CDN клиенту.


### <a name="schema-of-the-core-analytics-logs"></a>Схема журналов основной аналитики 

Все журналы хранятся в формате JSON, и каждая запись содержит строковые поля, соответствующие приведенной ниже схеме.

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

Здесь time представляет начало периода, для которого выводятся статистические данные. Если метрика не поддерживается поставщиком CDN, вместо значения типа double или целого числа используется значение NULL. Это значение указывает на отсутствие метрики и отличается от значения 0. Также обратите внимание, что одному домену, настроенному на конечной точке, соответствует один набор этих метрик.

Примеры свойств приведены ниже

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
* [Что такое Log Analytics?](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-overview)
* [Log Analytics REST API Reference](https://docs.microsoft.com/en-us/rest/api/loganalytics) (Справочник по REST API Log Analytics)








