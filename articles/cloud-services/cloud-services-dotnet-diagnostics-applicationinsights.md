<properties
   pageTitle="Устранение неполадок облачных служб с помощью Application Insights | Microsoft Azure"
   description="Узнайте, как устранять неполадки облачной службы, используя Application Insights для обработки данных из системы диагностики Azure."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="saurabh" />


# Устранение неполадок облачных служб с помощью Application Insights

Теперь [пакет SDK Azure 2.8](https://azure.microsoft.com/downloads/) и расширение диагностики Azure 1.5 позволяют отправлять данные диагностики Azure для облачной службы непосредственно в Application Insights. В Application Insights можно отправлять различные типы журналов, собранных службой диагностики Azure, включая журналы приложений, журналы событий Windows и счетчики производительности, а затем визуализировать их в графическом интерфейсе портала Application Insights. Используя службу вместе с пакетом SDK Application Insights, вы сможете получать данные из метрик и журналов, создаваемых вашим приложением, а также данные на уровне системы и инфраструктуры, исходящие из системы диагностики Azure.

## Настройка диагностики Azure для отправки данных в Application Insights

Чтобы настроить проект облачной службы на отправку данных диагностики Azure в Application Insights, выполните описанные ниже действия.

1\) В обозревателе решений Visual Studio щелкните роль правой кнопкой мыши и выберите **Свойства**. Откроется конструктор ролей.

![Свойства роли обозревателя решений][1]

2\) В разделе "Диагностика" конструктора ролей установите флажок **Отправлять данные диагностики в Application Insights**.

![Конструктор ролей отправляет данные диагностики в Application Insights][2]

3\) Во всплывающем диалоговом окне выберите ресурс Application Insights, в который следует отправлять данные диагностики Azure. В этом диалоговом окне можно выбрать существующий ресурс Application Insights из подписки или ввести ключ инструментирования для ресурса Application Insights вручную. Если у вас нет существующего ресурса Application Insights, можно его создать, щелкнув ссылку **Создать ресурс**. В браузере откроется новое окно с классическим порталом Azure, где можно создать ресурс Application Insights. Дополнительные сведения о создании ресурса Application Insights см. в статье [Создание ресурса Application Insights](../application-insights/app-insights-create-new-resource.md).

![выберите ресурс application insights][3]

4\) После добавления ресурса Application Insights соответствующий ему ключ инструментирования будет сохранен как параметр конфигурации службы с именем **APPINSIGHTS\_INSTRUMENTATIONKEY**. Этот параметр конфигурации можно изменить в соответствии с конфигурацией службы или средой, выбрав другую конфигурацию в раскрывающемся списке "Конфигурация службы" и указав для нее  новый ключ инструментирования.

![выберите конфигурацию службы][4]

Visual Studio использует параметр конфигурации **APPINSIGHTS\_INSTRUMENTATIONKEY** для настройки расширения диагностики с использованием соответствующих данных ресурса Application Insights во время публикации. Параметр конфигурации — это удобный способ определения различных ключей инструментирования для разных конфигураций службы. Visual Studio преобразует этот параметр и вставляет его в открытую конфигурацию расширения диагностики при публикации. Выходные данные пакета Visual Studio содержат, среди прочего, общедоступный XML-файл конфигурации с соответствующим ключом инструментирования Application Insights. Это позволяет упростить процесс настройки расширения с помощью PowerShell. Общедоступные файлы конфигураций создаются в папке "Расширения" с именами в формате bvt.bnPaaSDiagnostics.<RoleName>.PubConfig.xml. Все развертывания с использованием PowerShell могут использовать этот шаблон имени для сопоставления каждой конфигурации с ролью.

5\) Установка флажка **Отправлять данные диагностики в Application Insights** обеспечивает автоматическую настройку диагностики Azure на отправку всех счетчиков производительности и журналов ошибок, собранных агентом диагностики Azure, в Application Insights. Чтобы указать, какие данные должны отправляться в службу Application Insights, вручную отредактируйте файл *diagnostics.wadcfgx* для каждой роли. Дополнительные сведения об обновлении конфигурации вручную см. в статье [Настройка диагностики Azure для отправки данных в Application Insights](../azure-diagnostics-configure-applicationinsights.md).

Настроив облачную службу на отправку данных диагностики Azure в Application Insights, вы сможете развернуть ее в Azure, как обычно, включив расширение диагностики Azure. См. статью [Публикация облачной службы с помощью Visual Studio](../vs-azure-tools-publishing-a-cloud-service.md).

## Просмотр данных диагностики Azure в Application Insights
Телеметрия диагностики Azure появится в ресурсе Application Insights, настроенном для вашей облачной службы.

Вот как различные типы журналов диагностики Azure соотносятся с понятиями Application Insights:

-  Счетчики производительности отображаются в Application Insights в виде пользовательских метрик.
-  Журналы событий Windows отображаются в Application Insights в виде трассировок и пользовательских событий.
-  Журналы приложений, журналы ETW и все журналы инфраструктуры диагностики отображаются в Application Insights в виде трассировок.

Чтобы просмотреть данные диагностики Azure в Application Insights, сделайте следующее:

- Используйте [обозреватель метрик](../application-insights/app-insights-metrics-explorer.md) для визуализации пользовательских счетчиков производительности или счетчиков различных типов событий журналов событий Windows.

![Пользовательские метрики в обозревателе метрик][5]

- Используйте функцию [Поиск](../application-insights/app-insights-diagnostic-search.md) для поиска в различных журналах трассировки, отправляемых системой диагностики Azure. Например, если в роли есть необработанное исключение, которое вызвало сбой и перезапуск этой роли, соответствующие данные появятся в канале *Приложение* *журнала событий Windows*. Функцию поиска можно использовать для поиска ошибки в журнале событий Windows и получения полной трассировки стека для исключения, позволяющей установить причину возникшей проблемы.

![Поиск трассировок][6]

## Дальнейшие действия

- [Добавьте пакет SDK Application Insights в свою облачную службу](../application-insights/app-insights-cloudservices.md) для отправки данных о запросах, исключениях, зависимостях и других данных пользовательской телеметрии из приложения. В сочетании с данными диагностики Azure вы сможете получить полное представление о приложении и системе в одном и том же ресурсе Application Insight.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

<!---HONumber=AcomDC_0413_2016-->