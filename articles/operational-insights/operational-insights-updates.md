<properties
   pageTitle="Установка системных обновлений на серверах"
   description="Узнайте, как использовать решение для системных обновлений в Microsoft Azure Operational Insights для установки пропущенных обновлений на серверах инфраструктуры."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders" />

# Установка системных обновлений на серверах

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Решение для системных обновлений в Microsoft Azure Operational Insights позволяет применять пропущенные обновления к серверам в инфраструктуре. Решение устанавливается с целью обновления агента Operations Manager и модуля базовой конфигурации в Operational Insights. Сведения об обновлениях считываются в отслеживаемых службах, а затем данные обновлений отправляются в службу оперативной аналитики в облако для обработки. К данным обновлений применяется логика, и облачная служба записывает данные. Пропущенные обновления отображаются на панели мониторинга **Обновления**. На панели мониторинга **Обновления** можно работать с пропущенными обновлениями и разработать план для всех серверов, которым требуются обновления.

## Установка системных обновлений на серверах

Для использования системных обновлений в службе Microsoft Azure Operational Insights необходимо установить решение. Дополнительные сведения об установке решений см. в разделе [Использование коллекции решений для добавления или удаления решений](operational-insights-setup-workspace.md). После установки можно просмотреть обновления, пропущенные на отслеживаемых серверах, с помощью плитки **Оценка системных обновлений** на странице **Обзор** в службе Operational Insights.

### Работа с обновлениями

1. На странице **Обзор** щелкните плитку **Оценка системных обновлений**.![image of the Overview page](./media/operational-insights-updates/updates01.png)
2. На панели мониторинга **Обновления** просмотрите категории обновлений.![image of the Updates page](./media/operational-insights-updates/updates02.png)
3. Прокрутите страницу вправо для просмотра колонки **Тип пропущенного обновления**, а затем щелкните **Обновления для системы безопасности**.![image of the Updates page](./media/operational-insights-updates/updates03.png)
4. На странице «Поиск» отображается список обновлений для системы безопасности, которые были пропущены для серверов инфраструктуры. Щелкните идентификатор статьи базы знаний (KBID), чтобы просмотреть дополнительные сведения о пропущенном обновлении. В данном примере: *KBID 3032323*. ![image of the Updates page](./media/operational-insights-updates/updates04.png)
5. Статья с описанием обновления откроется в веб-браузере. ![image of the Updates page](./media/operational-insights-updates/updates05.png)
6. Используя найденную информацию, можно создать план для установки пропущенных обновлений.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->