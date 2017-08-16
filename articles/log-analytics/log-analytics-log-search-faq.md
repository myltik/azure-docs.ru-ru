---
title: "Вопросы и ответы по новым возможностям поиска по журналам Log Analytics | Документация Майкрософт"
description: "Эта статья содержит часто задаваемые вопросы о переходе Log Analytics на новый язык запросов."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: ru-ru
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Вопросы и ответы по новым возможностям поиска по журналам Log Analytics

Эта статья содержит часто задаваемые вопросы о [переходе Log Analytics на новый язык запросов](log-analytics-log-search-upgrade.md).



## <a name="upgrade-process"></a>Процесс обновления

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>У меня есть несколько рабочих областей. Можно ли обновить все рабочие области одновременно?  
Ответ. Нет.  Каждое обновление применяется только к одной рабочей области. В настоящее время не существует возможности обновить сразу несколько рабочих областей. Обратите внимание, что обновление затронет и других пользователей обновленной рабочей области.  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Изменятся ли после обновления существующие данные в журналах, собранных в мою рабочую область?  
Нет. Данные журналов, доступные для поиска в рабочей области, не изменяются в процессе обновления. Сохраненные поисковые запросы, оповещения и представления автоматически преобразуются на новый язык поиска.  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>Что произойдет, если я не буду обновлять рабочую область?  
Старая версия поиска по журналам будет считаться устаревшей через несколько месяцев. Рабочие области, которые вы не обновите к этому времени, будут обновлены автоматически.

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>Я не выполнял обновление, но моя рабочая область обновилась сама! Как это могло произойти?  
Возможно, вашу рабочую область обновил другой администратор. Также обратите внимание, что после выхода общедоступной версии нового языка все рабочие области обновятся автоматически.  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>Я случайно обновил рабочую область, а теперь хочу отменить обновление и вернуть все, как было. Что мне делать?  
Не беспокойтесь.  Мы создали моментальный снимок рабочей области перед обновлением, и вы можете ее восстановить. Но учтите, что при этом будут утрачены все поиски, оповещения и представления, которые вы успели сохранить после обновления.  Чтобы восстановить среду рабочей области, воспользуйтесь процедурой из раздела [Can I go back after I upgrade?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)(Можно ли вернуться обратно после обновления?).



## <a name="log-searches"></a>Поиск по журналам

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>До обновления я сохранял поисковые запросы вне рабочей области. Могу ли я теперь автоматически преобразовать их на новый язык поиска?
Вы можете преобразовать каждый запрос отдельно с помощью средства преобразования, которое доступно на странице поиска по журналам.  Невозможно автоматически преобразовать несколько поисковых запросов вне процесса обновления рабочей области.


## <a name="alerts"></a>Оповещения

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>У меня очень много правил для оповещений. Придется ли мне после обновления создать их заново на новом языке?  
Нет, все правила оповещений автоматически преобразуются на новый язык поиска в процессе обновления.  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>Затронут ли эти изменения интеграцию с Power BI?
Да.  После обновления рабочей области процесс экспорта данных Log Analytics в Power BI работать не будет.  Все существующие расписания, созданные перед обновлением, будут отключены.  После обновления в Azure Log Analytics работает та же платформа, что и в Application Insights, и вы можете использовать для экспорта запросов Log Analytics в Power BI тот же процесс, что и для [экспорта запросов Application Insights в Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

## <a name="dashboards"></a>Панели мониторинга

### <a name="can-i-still-use-dashboards"></a>Могу ли я по-прежнему использовать панели мониторинга?
Вы можете и далее использовать панели мониторинга, созданные до обновления рабочей области, но вы не сможете их изменить или создать новые.  Вы можете продолжить создание и изменение представлений с помощью [конструктора представлений](log-analytics-view-designer.md). 

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [переключении рабочей области на новый язык запросов Log Analytics](log-analytics-log-search-upgrade.md).

