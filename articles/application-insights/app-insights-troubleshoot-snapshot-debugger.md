---
title: "Руководство по устранению неполадок с помощью отладчика моментальных снимков Azure Application Insights | Документация Майкрософт"
description: "Часто задаваемые вопросы об отладчике моментальных снимков Azure Application Insights."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Отладчик моментальных снимков: руководство по устранению неполадок

Отладчик моментальных снимков Application Insights позволяет автоматически собирать отладочные моментальные снимки из динамических веб-приложений. Моментальный снимок отображает состояние исходного кода и переменных в момент порождения исключения. Если у вас возникли трудности с установкой и выполнением отладчика моментальных снимков Application Insights, эта статья поможет вам понять, как он работает, а также поможет ознакомиться с решениями для распространенных сценариев устранения неполадок. 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Как работает отладчик моментальных снимков Application Insights

Отладчик моментальных снимков Application Insights является частью конвейера телеметрии Application Insights (экземпляр ITelemetryProcessor). Сборщик моментальных снимков отслеживает исключения, порождаемые в коде (AppDomain.FirstChanceException), и исключения, отслеживаемые конвейером телеметрии исключений Application Insights. После того, как сборщик моментальных снимков был успешно добавлен в проект и он обнаружил одно исключение в конвейере телеметрии Application Insights, отправляются пользовательские события Application Insights с именами AppInsightsSnapshotCollectorLogs и SnapshotCollectorEnabled в разделе "Пользовательские данные". В то же время он запускает процесс MinidumpUploader.exe для передачи файлов данных собранных моментальных снимков в Application Insights.  Когда запускается процесс MinidumpUploader.exe, отправляется пользовательское событие UploaderStart. После выполнения предыдущих шагов сборщик моментальных снимков переходит в обычный режим мониторинга.

Хотя сборщик моментальных снимков отслеживает телеметрию исключений Application Insights, он использует параметры (например, ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval), определенные в конфигурации, чтобы определить, когда нужно получить моментальный снимок. Если условия всех правил выполнены, сборщик запрашивает моментальный снимок для следующего исключения, порожденного в том же месте. Одновременно отправляются пользовательские события Application Insights с именами AppInsightsSnapshotCollectorLogs и RequestSnapshots. Так как компилятор оптимизирует код выпуска, локальные переменные могут не отображаться в полученном моментальном снимке. Сборщик моментальных снимков попытается деоптимизировать метод, который породил исключение, когда будет запрашивать моментальные снимки. В это время отправляются пользовательские события Application Insights с именами AppInsightsSnapshotCollectorLogs и ProductionBreakpointsDeOptimizeMethod.  После получения моментального снимка следующего исключения локальные переменные будут доступны. После получения моментального снимка код будет повторно оптимизирован для обеспечения производительности. 

> [!NOTE]
> Для деоптимизации требуется установка расширения сайта Application Insights.

При запросе моментального снимка для конкретного исключения сборщик моментальных снимков начнет мониторинг конвейера обработки исключений приложения (AppDomain.FirstChanceException). Если исключение появится снова, сборщик начнет получение моментального снимка (пользовательские события Application Insights с именами AppInsightsSnapshotCollectorLogs и SnapshotStart в разделе "Пользовательские данные"). Затем создается теневая копия запущенного процесса (будет продублирована таблица страниц). Как правило, это занимает 10–20 миллисекунд. После этого отправляются пользовательские события Application Insights с именами AppInsightsSnapshotCollectorLogs и SnapshotStop в разделе "Пользовательские данные". При создании разветвленного процесса общий объем выгружаемой памяти будет увеличен на размер выгружаемой памяти для запущенного приложения (рабочий набор будет намного меньше). Во время обычного выполнения процесса приложения дамп памяти процесса теневой копии будет записываться на диск и отправляться в Application Insights. После передачи моментального снимка отправляется пользовательское событие Application Insights с именем UploadSnapshotFinish.

## <a name="is-the-snapshot-collector-working-properly"></a>Правильно ли работает сборщик моментальных снимков?

### <a name="how-to-find-snapshot-collector-logs"></a>Как найти журналы сборщика моментальных снимков
Журналы сборщика моментальных снимков отправляются в вашу учетную запись Application Insight, если установлен [пакет NuGet сборщика моментальных снимков](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) версии 1.1.0 или более поздней версии. Убедитесь, что *ProvideAnonymousTelemetry* не присвоено значение false (значение по умолчанию — true).

* Перейдите к ресурсу Application Insights на портале Azure.
* Щелкните *Найти* в разделе "Обзор".
* В поле поиска введите следующую строку.
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Примечание. При необходимости измените *диапазон времени*.

![Снимок экрана поиска журналов сборщика моментальных снимков](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Изучение журналов сборщика моментальных снимков
При поиске журналов сборщика моментальных снимков в выбранном диапазоне времени должны существовать события UploadSnapshotFinish. Если кнопка "Открыть моментальный снимок отладки" для открытия моментального снимка по-прежнему отсутствует, отправьте сообщение со своим ключом инструментирования Application Insights на адрес электронной почты: snapshothelp@microsoft.com.

![Снимок экрана изучения журналов сборщика моментальных снимков](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Не удается найти моментальный снимок для открытия
Если приведенные ниже действия не помогут устранить эту проблему, отправьте сообщение со своим ключом инструментирования Application Insights на адрес электронной почты: snapshothelp@microsoft.com.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Шаг 1. Убедитесь, что приложение отправляет данные телеметрии и исключений в Application Insights
Перейдите к ресурсу Application Insights и убедитесь, что данные передаются из приложения.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Шаг 2. Убедитесь, что сборщик моментальных снимков правильно добавлен в конвейер телеметрии Application Insights приложения
Если вам удалось найти файлы журнала на шаге "Как найти журналы сборщика моментальных снимков", то сборщик моментальных снимков добавлен в проект правильно. Пропустите этот шаг.

Если журналы сборщика моментальных снимков отсутствуют, проверьте следующее.
* Для классических приложений ASP.NET: проверьте строку *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">* в файле *ApplicationInsights.config*.

* Для приложений ASP.NET Core: убедитесь, что класс *ITelemetryProcessorFactory* с *SnapshotCollectorTelemetryProcessor* добавлен в службы *IServiceCollection*.

* Кроме того, убедитесь, что в опубликованном приложении используется правильный ключ инструментирования.

* Сборщик моментальных снимков не поддерживает несколько ключей инструментирования в одном приложении. Он передает моментальные снимки для ключа инструментирования первого исключения, которое обнаруживает.

* Если вы задали *InstrmentationKey* вручную в коде, обновите элемент *InstrumentationKey* из *ApplicationInsights.config*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Шаг 3. Убедитесь, что отправитель минидампов запущен
В журналах сборщика моментальных снимков найдите *UploaderStart* (введите "UploaderStart" в текстовом поле поиска). В них должно быть событие, зарегистрированное, когда сборщик моментальных снимков отслеживал первое исключение. Если это событие не существует, проверьте сведения в других журналах. Один из возможных способов устранения этой проблемы — перезапуск приложения.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Шаг 4. Убедитесь, что сборщик моментальных снимков выразил намерение собирать моментальные снимки
В журналах сборщика моментальных снимков найдите *RequestSnapshots* (введите ```RequestSnapshots``` в текстовом поле поиска).  Если такие события отсутствуют, проверьте конфигурацию, например параметр *ThresholdForSnapshotting*, который задает количество определенных исключений, которые могут быть порождены, прежде чем начнется сбор моментальных снимков.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Шаг 5. Убедитесь, что создание моментального снимка не отключено из-за защиты памяти
Чтобы защитить производительность приложения, моментальный снимок создается только при наличии достаточного буфера памяти. В журналах сборщика моментальных снимков найдите события CannotSnapshotDueToMemoryUsage. В разделе "Пользовательские данные" события будет указано подробное описание причины. Если приложение выполняется в веб-приложении Azure, ограничение может быть строгим. Так как веб-приложение Azure будет перезапускать приложение при соблюдении определенных правил памяти. Можно попытаться масштабировать план обслуживания, выбрав компьютеры с большим объемом памяти, чтобы устранить эту проблему.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Шаг 6. Убедитесь, что моментальные снимки были записаны
В журналах сборщика моментальных снимков найдите события ```RequestSnapshots```.  Если они отсутствуют, проверьте конфигурацию, например параметр ```ThresholdForSnapshotting```. Он указывает количество определенных исключений, порождаемых перед тем, как создается моментальный снимок.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Шаг 7. Убедитесь, что моментальные снимки передаются правильно
В журналах сборщика моментальных снимков найдите события ```UploadSnapshotFinish```.  Если они отсутствуют, отправьте сообщение со своим ключом инструментирования Application Insights на адрес электронной почты snapshothelp@microsoft.com. Если это событие существует, откройте один из журналов и скопируйте значение SnapshotId из раздела "Пользовательские данные". Затем найдите это значение. Вы найдете исключение, соответствующее моментальному снимку. Щелкните это исключение и откройте отладочный моментальный снимок. (Если соответствующее исключение отсутствует, возможно, из-за большого объема используется выборка данных телеметрии. Попробуйте другой snapshotId.)

![Снимок экрана поиска snapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Снимок экрана открытого исключения](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Снимок экрана открытого отладочного моментального снимка](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>В представлении моментального снимка отображены не все локальные переменные

Отсутствуют некоторые локальные переменные. Если выполняется код выпуска приложения, компилятор оптимизирует некоторые переменные. Например:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Если у вас иной случай, возможно, проблема вызвана ошибкой. Отправьте сообщение со своим ключом инструментирования Application Insights и фрагментом кода на адрес электронной почты snapshothelp@microsoft.com.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Представление моментального снимка: не удается получить значение локальной переменной или аргумента
Убедитесь, что [расширение сайта Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) установлено. Если проблема не исчезла, отправьте сообщение со своим ключом инструментирования Application Insights на адрес электронной почты snapshothelp@microsoft.com.
