---
title: "Устранение неполадок облачных служб с помощью Application Insights | Документация Майкрософт"
description: "Узнайте, как устранять неполадки облачной службы, используя Application Insights для обработки данных из системы диагностики Azure."
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.contentlocale: ru-ru
ms.lasthandoff: 06/01/2017

---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>Устранение неполадок облачных служб с помощью Application Insights
[Пакет Azure SDK 2.8](https://azure.microsoft.com/downloads/) и расширение диагностики Azure 1.5 позволяют отправлять данные диагностики Azure для облачной службы непосредственно в Application Insights. В Application Insights можно отправлять журналы, собранные системой диагностики Azure, &mdash;включая журналы приложений, журналы событий Windows и счетчики производительности&mdash;. Затем эти сведения можно визуализировать в пользовательском интерфейсе портала Application Insights. Используя пакет SDK Application Insights, вы можете получать данные из метрик и журналов, создаваемых вашим приложением, а также данные на уровне системы и инфраструктуры, поступающие из системы диагностики Azure.

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>Настройка диагностики Azure для отправки данных в Application Insights
Чтобы настроить проект облачной службы для отправки данных диагностики Azure в Application Insights, выполните описанные ниже действия.

1. В обозревателе решений Visual Studio щелкните роль правой кнопкой мыши и выберите **Свойства**, чтобы открыть конструктор ролей.

    ![Свойства роли обозревателя решений][1]

2. В разделе **Диагностика** конструктора ролей выберите параметр **Отправка диагностических данных в Application Insights**.

    ![Конструктор ролей отправляет данные диагностики в Application Insights][2]

3. В появившемся диалоговом окне выберите ресурс Application Insights, в который требуется отправлять данные диагностики Azure. Это диалоговое окно позволяет выбрать имеющийся ресурс Application Insights из подписки или вручную задать ключ инструментирования для такого ресурса. Дополнительные сведения о создании ресурса Application Insights см. в статье [Создание ресурса Application Insights](../application-insights/app-insights-create-new-resource.md).

    ![выберите ресурс application insights][3]

    После добавления ресурса Application Insights соответствующий ему ключ инструментирования будет сохранен в качестве параметра конфигурации службы с именем **APPINSIGHTS_INSTRUMENTATIONKEY**. Этот параметр конфигурации можно изменить для каждой конфигурации службы или окружения. Для этого выберите другую конфигурацию в списке **Конфигурация службы** и укажите для нее новый ключ инструментирования.

    ![выберите конфигурацию службы][4]

    Visual Studio использует параметр конфигурации **APPINSIGHTS_INSTRUMENTATIONKEY** для настройки расширения диагностики с использованием соответствующих данных ресурса Application Insights во время публикации. Параметр конфигурации — это удобный способ определения различных ключей инструментирования для разных конфигураций службы. Visual Studio преобразует этот параметр и вставит его в открытую конфигурацию расширения диагностики при публикации. Выходные данные пакета Visual Studio содержат, среди прочего, общедоступный XML-файл конфигурации с соответствующим ключом инструментирования Application Insights. Это позволяет упростить процесс настройки расширения с помощью PowerShell. Общедоступные файлы конфигураций создаются в папке Extensions с именами в формате *PaaSDiagnostics.&lt;имя_роли&gt;.PubConfig.xml*. Все развертывания с использованием PowerShell могут использовать этот шаблон имени для сопоставления каждой конфигурации с ролью.

4) Чтобы настроить систему диагностики Azure для отправки всех счетчиков производительности и журналов ошибок, собранных агентом диагностики Azure, в Application Insights, установите флажок **Отправка диагностических данных в Application Insights**. 

    Чтобы указать, какие данные должны отправляться в службу Application Insights, вручную отредактируйте файл *diagnostics.wadcfgx* для каждой роли. Дополнительные сведения об обновлении конфигурации вручную см. в статье [Настройка диагностики Azure для отправки данных в Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights).

Настроив облачную службу для отправки диагностических данных Azure в Application Insights, вы можете развернуть ее в Azure, как обычно, включив расширение диагностики Azure. Дополнительные сведения см. в статье [Публикация облачной службы с помощью Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>Просмотр данных диагностики Azure в Application Insights
Телеметрия диагностики Azure появляется в ресурсе Application Insights, настроенном для вашей облачной службы.

Типы журналов системы диагностики Azure соотносятся с понятиями Application Insights по-разному:

* Счетчики производительности отображаются в Application Insights в виде пользовательских метрик.
* Журналы событий Windows отображаются в Application Insights в виде трассировок и пользовательских событий.
* Журналы приложений, журналы ETW и все журналы инфраструктуры диагностики отображаются в Application Insights в виде трассировок.

Чтобы просмотреть данные диагностики Azure в Application Insights, выполните одно из следующих действий:

* Используйте [обозреватель метрик](../application-insights/app-insights-metrics-explorer.md) для визуализации пользовательских счетчиков производительности или других типов счетчиков для событий журналов событий Windows.

    ![Пользовательские метрики в обозревателе метрик][5]

* Используйте функцию [Поиск](../application-insights/app-insights-diagnostic-search.md) для поиска в журналах трассировки, отправляемых системой диагностики Azure. Например, если из-за необработанного исключения произошел сбой и перезапуск роли, соответствующие данные появляются в канале *Приложение* *журнала событий Windows*. Можно воспользоваться поиском, чтобы просмотреть ошибку в журнале событий Windows и получить полную трассировку стека для исключения, что поможет найти причину проблемы.

    ![Поиск трассировок][6]

## <a name="next-steps"></a>Дальнейшие действия
* [Добавьте пакет SDK Application Insights в свою облачную службу](../application-insights/app-insights-cloudservices.md) для отправки данных о запросах, исключениях, зависимостях и других данных пользовательской телеметрии из приложения. В сочетании с данными диагностики Azure эта информация позволяет получить полное представление о приложении и системе в одном и том же ресурсе Application Insight.  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

