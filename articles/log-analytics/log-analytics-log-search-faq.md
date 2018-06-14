---
title: Вопросы и ответы по новым возможностям поиска по журналам Log Analytics | Документация Майкрософт
description: Эта статья содержит часто задаваемые вопросы о переходе Log Analytics на новый язык запросов.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/22/2018
ms.locfileid: "27993875"
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Вопросы и ответы по новым возможностям поиска по журналам Log Analytics и известные проблемы

Эта статья содержит часто задаваемые вопросы о [переходе Log Analytics на новый язык запросов](log-analytics-log-search-upgrade.md), а также связанные с этим известные проблемы.  Перед обновлением рабочей области мы рекомендуем ознакомиться с содержанием этой статьи.


## <a name="alerts"></a>Оповещения

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Вопрос. У меня много правил генерации оповещений. Придется ли мне после обновления создать их заново на новом языке?  
Нет, все правила оповещений автоматически преобразуются на новый язык поиска в процессе обновления.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Вопрос. У меня есть правила оповещений с действиями веб-перехватчика и модуля runbook. Они будут и дальше работать после обновления?

Нет, были внесены некоторые изменения в действия веб-перехватчика и модуля runbook, которые могут потребовать изменения способа обработки полезных данных. Мы внесли эти изменения, чтобы стандартизировать различные форматы выходных данных и уменьшить размер полезных данных. Дополнительные сведения об этих форматах см. в руководстве по [добавлению действий в правила оповещений в службе Log Analytics](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Группы компьютеров

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Вопрос. Попытки использовать группы компьютеров завершаются ошибкой.  Их синтаксис изменился?
Да. После обновления рабочей области изменяется синтаксис использования групп компьютеров.  Дополнительные сведения см. в статье [Использование групп компьютеров при поиске по журналам Log Analytics](log-analytics-computer-groups.md).


## <a name="dashboards"></a>Панели мониторинга

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Вопрос. Можно ли по-прежнему использовать панели мониторинга в обновленной рабочей области?
С выпуском этого обновления мы прекращаем поддержку плитки **Моя панель мониторинга**.  Вы можете и дальше использовать любые плитки, добавленные на панель мониторинга до обновления рабочей области, но после обновления вы не сможете их изменять или добавлять новые.  Вы можете и дальше создавать и изменять представления с помощью [конструктора представлений](log-analytics-view-designer.md) с расширенным набором функций, а также создавать панели мониторинга на портале Azure.


## <a name="log-searches"></a>Поиск по журналам

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Вопрос. До обновления я сохранял поисковые запросы вне рабочей области. Могу ли я теперь автоматически преобразовать их на новый язык поиска?
Вы можете преобразовать каждый запрос отдельно с помощью средства преобразования, которое доступно на странице поиска по журналам.  Невозможно автоматически преобразовать несколько поисковых запросов вне процесса обновления рабочей области.

### <a name="question-why-are-my-query-results-not-sorted"></a>Вопрос. Почему результаты запроса не отсортированы?
В новом языке запросов результаты не сортируются по умолчанию.  Чтобы отсортировать результаты по одному или нескольким свойствам, используйте [оператор SORT](https://go.microsoft.com/fwlink/?linkid=856079).

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Вопрос. Где после обновления находится представление метрик?
Представление метрик — это графическое отображение данных о производительности, полученных с помощью функции поиска по журналам.  Это представление после обновления недоступно.  Вы можете использовать [оператор отображения](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) для преобразования выходных данных из запроса во временную диаграмму.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Вопрос. Куда делать функция уменьшения после обновления?
Уменьшение — это функция, позволяющая получить итоговое представление результатов поиска.  После обновления параметр Minify (Уменьшить) больше не отображается на портале поиска по журналам.  Вы можете получить аналогичную функцию с помощью нового языка поиска, используя [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) или [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>API поиска по журналам

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Вопрос. Обновляется ли API поиска по журналам после обновления рабочей области?
Прежние версии [API поиска п журналам](log-analytics-log-search-api.md) не будут работать после обновления рабочей области.  Сведения о новых API. см. в руководстве по [API REST Log Analytics Azure](https://dev.loganalytics.io/).


## <a name="portals"></a>Порталы

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Вопрос. Следует ли использовать новый портал углубленной аналитики или продолжать использовать портал поиска по журналам?
Сравнение этих двух порталов см. в статье [Порталы для создания и изменения запросов к журналу в службе Azure Log Analytics](log-analytics-log-search-portals.md).  Каждый портал имеет свои преимущества, так что вы можете выбрать наиболее подходящий вариант в соответствии со своими требованиями.  Часто пользователи пишут запросы на портале углубленной аналитики, а затем вставляют их в другие расположения, например конструктор представлений.  При использовании этого варианта вам стоит ознакомиться с [потенциальными проблемами](log-analytics-log-search-portals.md#advanced-analytics-portal).


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Вопрос. После обновления при попытке выполнить запросы появляется сообщение об ошибке. Кроме того, я вижу ошибки в представлениях.

Ваш браузер запрашивает доступ к следующим адресам для выполнения запросов Log Analytics после обновления.  Если браузер обращается к порталу Azure через брандмауэр, необходимо разрешить доступ для этих адресов.

| URI | IP-адрес | порты; |
|:---|:---|:---|
| portal.loganalytics.io | Динамический | 80, 443 |
| api.loganalytics.io    | Динамический | 80, 443 |
| docs.loganalytics.io   | Динамический | 80, 443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Вопрос. Затронут ли эти изменения интеграцию с Power BI?
Да.  После обновления рабочей области процесс экспорта данных Log Analytics в Power BI работать не будет.  Все существующие расписания, созданные перед обновлением, будут отключены.  

После обновления в Azure Log Analytics работает та же платформа, что и в Application Insights, и вы можете использовать для экспорта запросов Log Analytics в Power BI тот же процесс, что и для [экспорта запросов Application Insights в Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  Теперь при экспорте в Power BI конечная точка API вызывается напрямую. Таким образом вы можете получить 500 000 строк или 64 000 000 байт данных, экспортировать длительные запросы и настраивать время ожидания запроса (время ожидания по умолчанию равно 3 минутам, а максимальное — 10 минутам).

## <a name="powershell-cmdlets"></a>Командлеты PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Вопрос. Обновляется ли командлет PowerShell поиска по журналам после обновления рабочей области?
Командлет [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) будет считаться нерекомендуемым после завершения обновления всех рабочих областей.  Используйте [командлет Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) для выполнения поиска по журналам в обновленных рабочих областях.




## <a name="resource-manager-templates"></a>Шаблоны диспетчера ресурсов

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Вопрос. Можно ли создать обновленную рабочую область с помощью шаблона Resource Manager?
Да.  Используйте API версии 2017-03-15-preview и добавить раздел **features**, как показано в примере ниже.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Решения

### <a name="question-will-my-solutions-continue-to-work"></a>Вопрос. Будут ли и дальше работать мои решения?
Все решения будут продолжать работать в обновленной рабочей области. Новый язык запросов позволяет увеличить их производительность.  В этом разделе описаны известные проблемы, возникающие с некоторыми имеющимися решениями.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Известная проблема. Перспективы в соединителе Application Insights
Перспективы в [решении "Соединитель Application Insights"](log-analytics-app-insights-connector.md) больше не поддерживаются.  Создать пользовательские представления с данными Application Insights можно с помощью конструктора представлений.

### <a name="known-issue-backup-solution"></a>Известная проблема: решение архивации
Решение архивации не может собирать данные, если оно было установлено перед обновлением рабочей области. Удалите решение, а затем установите последнюю версию.  Классические резервные хранилища не поддерживаются в новой версии решения, поэтому их также необходимо обновить до хранилищ служб восстановления, чтобы продолжить использование решения.

## <a name="upgrade-process"></a>Процесс обновления

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Вопрос. У меня есть несколько рабочих областей. Можно ли обновить все рабочие области одновременно?  
Нет.  Каждое обновление применяется только к одной рабочей области. В настоящее время не существует возможности обновить сразу несколько рабочих областей. Обратите внимание, что обновление затронет и других пользователей обновленной рабочей области.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Вопрос. Изменятся ли после обновления имеющиеся данные в журналах, собранных в рабочей области?  
Нет. Данные журналов, доступные для поиска в рабочей области, не изменяются в процессе обновления. Сохраненные поисковые запросы, оповещения и представления автоматически преобразуются на новый язык поиска.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Вопрос. Что произойдет, если не обновлять рабочую область?  
Старая версия поиска по журналам будет считаться устаревшей через несколько месяцев. Рабочие области, которые вы не обновите к этому времени, будут обновлены автоматически.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Вопрос. Можно ли отменить изменения после обновления?
До выпуска общедоступной версии изменения, внесенные в рабочие области после обновления, можно было отменить.  Теперь, когда новый язык перешел на этап общедоступной версии, эта возможность удалена, так как мы прекращаем использование платформы прежних версий.


## <a name="views"></a>Views

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Вопрос. Как создать представление с помощью конструктора представлений?
До обновления рабочей области вы могли создать представление с помощью конструктора представлений на плитке главной панели мониторинга на портале OMS.  После обновления эта плитка удаляется.  Вы можете создать представление с помощью конструктора представлений на портале OMS, нажав зеленую кнопку "+" в меню слева.  Чтобы создать представление на портале Azure, щелкните плитку конструктора представлений.


## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [переключении рабочей области на новый язык запросов Log Analytics](log-analytics-log-search-upgrade.md).
