---
title: "Развертывание в режиме фокус-тестирования (бета-тестирование) в службе приложений Azure"
description: "Из этого учебника вы узнаете, как выполнять фокус-тестирование новых функций в приложении или бета-тестирование обновлений. Этот режим тестирования объединяет такие функции службы приложений, как непрерывная публикация, управление слотами, маршрутизация трафика и интеграция с Application Insights."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 17953c51-38f8-442d-bb0b-f69c1542f0e9
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 84b5f28fcd0640fd85b5f8c9d655105790c70d62


---
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Развертывание в режиме фокус-тестирования (бета-тестирование) в службе приложений Azure
В этом учебнике показано, как выполнить *развертывание для фокус-тестирования*, интегрируя различные возможности [службы приложений Azure](http://go.microsoft.com/fwlink/?LinkId=529714) и [Azure Application Insights](/services/application-insights/).

*Фокус-тестирование* — это процесс развертывания, в ходе которого выполняется проверка новой функции или внесенного изменения с привлечением ограниченного количества реальных клиентов. В рабочих сценариях это основной режим тестирования. Он аналогичен режиму бета-тестирования и иногда называется управляемым фокус-тестированием. Многие крупные предприятия, обозначившие свое веб-присутствие, используют этот подход для выполнения ранней проверки обновлений своих приложений в рамках методологии [гибкой разработки](https://en.wikipedia.org/wiki/Agile_software_development). Служба приложений Azure позволяет включить тестирование в рабочую среду с непрерывной публикацией, а служба Application Insights — реализацию того же сценария DevOps. Этот подход отличается следующими преимуществами.

* **Получение реальных данных *перед* выпуском обновлений в рабочей среде**. Разумеется, вы можете получить отклик сразу же после выпуска продукта. Но будет лучше, если это произойдет до выпуска. Вы можете тестировать обновления с привлечением текущего пользовательского трафика и на основе поведения пользователей на любом (даже самом раннем) этапе жизненного цикла продукта.
* **Оптимизация [непрерывной разработки на основе тестирования (CTDD)](https://en.wikipedia.org/wiki/Continuous_test-driven_development)**. Непрерывная интеграция в рабочую среду процессов тестирования и инструментирования средствами Application Insights делает возможным раннюю автоматическую проверку с привлечением пользователей, выполняемую на требуемом этапе жизненного цикла продукта. Это помогает сократить временные затраты при выполнении тестирования вручную.
* **Оптимизация процесса тестирования**. Автоматизация тестирования в рабочей среде методом непрерывного наблюдательного инструментирования позволяет с высокой долей вероятности решить задачи, которые ставятся перед разными типами тестирования, в рамках единого процесса. Эти задачи включают в себя тестирование [интеграции](https://en.wikipedia.org/wiki/Integration_testing), [регрессии](https://en.wikipedia.org/wiki/Regression_testing), [удобства использования](https://en.wikipedia.org/wiki/Usability_testing), доступности, локализации, [производительности](https://en.wikipedia.org/wiki/Software_performance_testing), [безопасности](https://en.wikipedia.org/wiki/Security_testing) и [приемки](https://en.wikipedia.org/wiki/Acceptance_testing).

Развертывание в режиме фокус-тестирования используется не только для маршрутизации текущего трафика. При таком развертывании предполагается максимально быстрое получение информации, включая сведения о непредвиденной ошибке, снижении производительности или проблемах взаимодействия с пользователями. Помните: вы имеете дело с реальными пользователями. И чтобы все сделать правильно, убедитесь, что вы настроили параметры фокус-тестирования для сбора всех данных, необходимых для принятия обоснованного решения перед выполнением следующего шага. В этом учебнике показано, как собирать данные с помощью Application Insights. Кроме того, вы можете использовать средство New Relic или другие подходящие вам технологии.

## <a name="what-you-will-do"></a>Выполняемая задача
Из этого учебника вы узнаете, как использовать следующие сценарии для тестирования приложения службы приложений в рабочей среде:

* [Маршрутизация рабочего трафика](app-service-web-test-in-production-get-start.md) в бета-версию приложения.
* [Инструментирование приложения](../application-insights/app-insights-web-track-usage.md) для получения полезных метрик.
* Непрерывное развертывание бета-версии приложения с отслеживанием метрик в режиме реального времени.
* Сравнение метрик рабочего приложения и бета-версии приложения для оценки того, как изменения кода преобразуются в результаты.

## <a name="what-you-will-need"></a>Необходимые условия
* Учетная запись Azure.
* Учетная запись [GitHub](https://github.com/) .
* Visual Studio 2015 — вы можете скачать выпуск [Community Edition](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).
* Оболочка Git Shell (устанавливается вместе с [GitHub для Windows](https://windows.github.com/)) — позволяет запускать команды PowerShell и Git в рамках одного сеанса.
* Последняя версия [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) .
* Базовые знания таких компонентов.
  * Развертывание шаблона [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) (ознакомьтесь также с разделом [Предсказуемое развертывание сложного приложения в Azure](app-service-deploy-complex-application-predictably.md)).
  * [Git.](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> Для работы с этим учебником требуется учетная запись Azure.
>
> * Вы можете [открыть учетную запись Azure бесплатно](https://azure.microsoft.com/pricing/free-trial/) — вы получаете кредиты, которые можно использовать для опробования платных служб Azure, и даже после израсходования кредитов вы сохраняете учетную запись и возможность использовать бесплатные службы Azure, такие как веб-приложения.
> * Вы можете [активировать преимущества подписчика Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) — каждый месяц ваша подписка Visual Studio предоставляет вам кредиты, которые можно использовать для оплаты служб Azure.
>
> Чтобы приступить к работе со службой приложений Azure до создания учетной записи Azure, перейдите к разделу [Пробное использование службы приложений](https://azure.microsoft.com/try/app-service/), где вы можете быстро создать кратковременное веб-приложение начального уровня в службе приложений. Никаких кредитных карт и обязательств.
>
>

## <a name="set-up-your-production-web-app"></a>Настройка рабочего веб-приложения
> [!NOTE]
> Сценарий, используемый в этом учебнике, автоматически настроит непрерывную публикацию из репозитория GitHub. Для этого необходимо, чтобы учетные данные GitHub уже хранились в Azure. Иначе при попытке настроить параметры системы управления версиями для веб-приложений произойдет сбой развертывания, заложенного в сценарий.
>
> Чтобы сохранить учетные данные GitHub в Azure, создайте веб-приложение на [портале Azure](https://portal.azure.com/) и [настройте развертывание GitHub](app-service-continuous-deployment.md). Это нужно сделать только один раз.
>
>

В типичном сценарии DevOps у вас есть приложение, которое работает в Azure, и вам нужно внести в него изменения с помощью непрерывной публикации. В этом сценарии вам нужно будет развернуть в рабочей среде разработанный и протестированный шаблон.

1. Создайте разветвление в репозитории [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp). Дополнительные сведения о создании разветвления см. в статье [Fork a Repo](https://help.github.com/articles/fork-a-repo/) (Разветвление репозитория). После создания разветвления его можно просматривать в браузере.

   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. Откройте сеанс Git Shell. Если у вас нет Git Shell, установите [GitHub для Windows](https://windows.github.com/) .
3. Создайте локальный клон разветвления, выполнив следующую команду:

     git clone https://github.com/<разветвление>/ToDoApp.git
4. Создав локальный клон, перейдите в каталог *&lt;корень_репозитория>*\ARMTemplates и запустите сценарий deploy.ps1 с уникальным суффиксом, как показано ниже.

     .\deploy.ps1 –RepoUrl https://github.com/<разветвление>/todoapp.git -ResourceGroupSuffix <суффикс>
5. При появлении запроса введите желаемое имя пользователя и пароль для доступа к базе данных. Запомните учетные данные базы данных, так как вам нужно будет еще раз указать их при обновлении группы ресурсов.

   Вы увидите ход подготовки различных ресурсов Azure. После завершения развертывания скрипт запустит приложение в браузере и сообщит вам об этом с помощью звукового сигнала.
   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)
6. Вернитесь в сеанс Git Shell и выполните следующую команду:

     .\swap –Name ToDoApp<суффикс>

   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)
7. После выполнения сценария вернитесь назад и перейдите по адресу внешнего интерфейса (http://ToDoApp*&lt;ваш_суффикс>*master.azurewebsites.net/), чтобы посмотреть на работу приложения в рабочей среде.
8. Перейдите на [портале Azure](https://portal.azure.com/) и посмотрите на результаты.

   Вы увидите два веб-приложения в одной группе ресурсов. Имя одного из них будет содержать суффикс `Api`. Если вы посмотрите на представление группы ресурсов, вы также увидите базу данных SQL и сервер, план службы приложений и промежуточные слоты для веб-приложений. Просмотрите различные ресурсы и сравните их с файлом *&lt;repository_root>*\ARMTemplates\ProdAndStage.json, чтобы узнать, как они настроены в шаблоне.

   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Вы настроили рабочее приложение.  Теперь представим, что вы получили негативный отзыв об удобстве использования приложения. Нужно разобраться, в чем проблема. Вам нужно инструментировать приложение, чтобы узнать, в чем дело.

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>Анализ: инструментирование клиентского приложения для мониторинга метрик
1. Откройте в Visual Studio файл *&lt;корень_репозитория>*\src\MultiChannelToDo.sln.
2. Восстановите все пакеты NuGet, щелкнув правой кнопкой мыши нужное решение и выбрав **Управление пакетами NuGet для решения** > **Восстановить**.
3. Щелкните правой кнопкой мыши **MultiChannelToDo** и выберите >  **Добавить телеметрию Application Insights** > **Настройка параметров**. После этого измените группу ресурсов на ToDoApp*&lt;ваш_суффикс>* и щелкните >  **Добавить Application Insights в проект**.
4. На портале Azure откройте колонку для ресурса Application Insight **MultiChannelToDo.Web** . Затем в части **Работоспособность приложения** щелкните **Дополнительные сведения о сборе данных о загрузке страниц браузера** и скопируйте код.
5. Добавьте скопированный код инструментирования JS в файл *&lt;корень_репозитория>*\src\MultiChannelToDo.Web\app\Index.cshtml непосредственно перед закрывающим тегом `<heading>`. Он должен содержать уникальный ключ инструментирования вашего ресурса Application Insight.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>
6. Настройте отправку пользовательских событий щелчка мыши в Application Insights, добавив следующий код в конец блока:

       <script>
           $(document.body).find("*").click(function(event) {

               appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
           });
       </script>

   Этот фрагмент кода JavaScript отправляет пользовательское событие в Application Insights каждый раз, когда пользователь щелкает в любом месте веб-приложения.
7. В Git Shell зафиксируйте и отправьте внесенные изменения в разветвление GitHub. Дождитесь, пока клиенты обновят браузер.

       git add -A :/
       git commit -m "add AI configuration for client app"
       git push origin master
8. Перенесите изменения, внесенные в развернутое приложение, в рабочую среду:

     .\swap –Name ToDoApp<суффикс>
9. Перейдите к настроенному ресурсу Application Insights. Щелкните «Настраиваемые события».

   ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

   Если вы не видите метрик, характеризующих пользовательские события, подождите несколько минут и щелкните **Обновить**.

Предположим, что вы видите диаграмму, похожую на представленную ниже:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

Под этой диаграммой отображена сетка событий:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

Согласно коду приложения ToDoApp событие **BUTTON** соответствует кнопке отправки, а событие **INPUT** — текстовому полю. С этим все понятно. Однако похоже на то, что из всего количества щелчков (а их довольно много) только некоторые относятся к элементам списка дел (события **LI** ).

На основе этого можно предположить, что некоторые пользователи не всегда могут определить, какие части пользовательского интерфейса являются интерактивными. Причина — при наведении на элементы списка и соответствующие значки курсор выглядит как средство для выделения текста.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Этот пример может выглядеть несколько надуманным. Тем не менее вам нужно улучшить приложение, а затем выполнить развертывание в режиме фокус-тестирования, чтобы получить отзывы реальных клиентов об удобстве использования.

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>Инструментирование серверного приложения для получения метрик и для мониторинга
Эта тема будет освещена вкратце, так как в описываемых в учебнике сценариях используется только клиентское приложение. Однако для полноты картины мы настроим также и серверное приложение.

1. Щелкните правой кнопкой мыши **MultiChannelToDo** и выберите >  **Добавить телеметрию Application Insights** > **Настройка параметров**. После этого измените группу ресурсов на ToDoApp*&lt;ваш_суффикс>* и щелкните >  **Добавить Application Insights в проект**.
2. В Git Shell зафиксируйте и отправьте внесенные изменения в разветвление GitHub. Дождитесь, пока клиенты обновят браузер.

       git add -A :/
       git commit -m "add AI configuration for server app"
       git push origin master
3. Перенесите изменения, внесенные в развернутое приложение, в рабочую среду:

     .\swap –Name ToDoApp<суффикс>

Вот и все!

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>Анализ: добавление связанных со слотом тегов к метрикам клиентского приложения
В этом разделе вы настроите разные слоты развертывания для отправки связанных со слотом данных телеметрии в один ресурс Application Insights. Таким образом вы сможете сравнить данные телеметрии через призму трафика из разных слотов (сред развертывания), чтобы увидеть результат изменений, внесенных в приложение. Кроме того, вы сможете отделить рабочий трафик от остального, при необходимости продолжая наблюдать за рабочим приложением.

Так как вы собираете данные о поведении клиента, вам нужно [добавить в код JavaScript инициализатор телеметрии](../application-insights/app-insights-api-filtering-sampling.md) в файле index.cshtml. А чтобы выполнить тестирование производительности на стороне сервера, вы сможете выполнить эти же действия с серверным кодом (ознакомьтесь со статьей [API Application Insights для пользовательских событий и метрик](../application-insights/app-insights-api-custom-events-metrics.md)).

1. Во-первых, вставьте код между двумя комментариями `//` в блоке JavaScript ниже, который вы ранее добавили к тегу `<heading>`.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Этот код инициализатора вызывает объект `appInsights` для добавления пользовательского свойства с именем `Environment` ко всем отправляемым элементам телеметрии.
2. Затем добавьте это пользовательское свойство как [параметр слота](web-sites-staged-publishing.md#AboutConfiguration) для веб-приложения Azure. Чтобы сделать это, выполните следующую команду в оболочке Git Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    Файл Web.config в проекте уже определяет параметр `environment` приложения. С этим параметром при локальном тестировании приложения метрики будут помечаться как `VS Debugger`. Однако, если изменения отправляются в Azure, вместо этого будет найден и использован параметр приложения `environment` в конфигурации веб-приложения, а метрики будут обозначены тегом `Production`.
3. Зафиксируйте и отправьте изменения кода в разветвление на GitHub и подождите, пока пользователи начнут использовать новое приложение (необходимо обновить браузер). Чтобы новое свойство отобразилось в ресурсе `MultiChannelToDo.Web` Application Insights, требуется около 15 минут.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master
4. Теперь снова перейдите к колонке **Пользовательские события** и отфильтруйте метрики по `Environment=Production`. Применив этот фильтр, вы сможете увидеть все новые пользовательские события в рабочем слоте.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)
5. Нажмите кнопку **Избранное**, чтобы сохранить текущие параметры обозревателя метрик с именем наподобие **Пользовательские события: рабочая среда**. Позже вы сможете легко переключаться между этим представлением и представлением слота развертывания.

   > [!TIP]
   > Если вам нужны более мощные средства аналитики, рассмотрите возможность [интеграции ресурса Application Insights с Power BI](../application-insights/app-insights-export-power-bi.md).
   >
   >

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>Добавление связанных со слотом тегов к метрикам серверного приложения
Снова-таки, для полноты картины мы настроим также серверное приложение. В отличие от клиентского приложения, которое инструментируется в JavaScript, связанные со слотом теги для серверного приложения инструментируются с помощью кода .NET.

1. Откройте файл *&lt;корень_репозитория>*\src\MultiChannelToDo\Global.asax.cs. Вставьте следующий блок кода непосредственно перед закрывающей фигурной скобкой пространства имен.

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }
2. Исправьте ошибки разрешения имен, добавив следующие инструкции `using` в начало файла:

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;
3. Добавьте следующий код в начало метода `Application_Start()` :

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());
4. Зафиксируйте и отправьте изменения кода в разветвление на GitHub и подождите, пока пользователи начнут использовать новое приложение (необходимо обновить браузер). Чтобы новое свойство отобразилось в ресурсе `MultiChannelToDo` Application Insights, требуется около 15 минут.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>Обновление: настройка ветви бета-версии
1. Откройте файл *&lt;корень_репозитория>*\ARMTemplates\ProdAndStagetest.json и найдите ресурсы `appsettings` (выполните поиск `"name": "appsettings"`). Существует 4 таких ресурса, по одному для каждого слота.
2. Для каждого ресурса `appsettings` добавьте параметр приложения `"environment": "[parameters('slotName')]"` в конец массива `properties`. Не забудьте завершить предыдущую строку запятой.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)

    Вы только что добавили параметр приложения `environment` для всех слотов в шаблоне.
3. В этом же файле найдите ресурсы `slotconfignames` (выполните поиск `"name": "slotconfignames"`). Существует 2 таких ресурса, по одному для каждого приложения.
4. Для каждого ресурса `slotconfignames` добавьте `"environment"` в конец массива `appSettingNames`. Не забудьте завершить предыдущую строку запятой.

    Вы только что связали параметр приложения `environment` с соответствующим слотом развертывания для двух приложений.  
5. В сеансе Git Shell выполните следующие команды с тем же суффиксом группы ресурсов, который вы использовали ранее.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta
6. При появлении запроса укажите учетные данные базы данных SQL, как и ранее. Затем при появлении запроса на обновление группы ресурсов введите `Y` и `ENTER`.

    После завершения скрипта все ресурсы в исходной группе ресурсов будут сохранены. При этом в ней будет создан новый слот с именем beta с такой же конфигурацией, как и у промежуточного слота, который был создан в начале.

   > [!NOTE]
   > Этот метод создания разных сред развертывания отличается от метода [гибкой разработки программного обеспечения с помощью службы приложений Azure](app-service-agile-software-development.md). Он предполагает создание сред развертывания со слотами развертывания — как если бы вы создавали среды развертывания с группами ресурсов. Управление средами развертывания с помощью групп ресурсов среды ограничивает доступ к рабочей среде для разработчиков. И хотя выполнять тестирование в рабочей среде сложно, вы можете легко решить эту задачу с помощью слотов.
   >
   >

При желании вы можете создать альфа-версию приложения, запустив следующий код:

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Этот учебник предполагает использование бета-версии приложения.

## <a name="update-push-your-updates-to-the-beta-app"></a>Обновление: отправка обновлений в бета-версию приложения
Перейдите к приложению, которое нужно улучшить.

1. Убедитесь, что вы находитесь в ветке бета-версии.

        git checkout beta
2. В файле *&lt;корень_репозитория>*\src\MultiChannelToDo.Web\app\Index.cshtml найдите тег `<li>` и добавьте атрибут `style="cursor:pointer"`, как показано ниже.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)
3. Зафиксируйте изменение и отправьте его в Azure.
4. Убедитесь, что изменение теперь отображается в слоте бета-версии, перейдя по адресу http://todoapp*&lt;ваш_суффикс>*-beta.azurewebsites.net/. Если изменение еще не отображается, обновите браузер для получения нового кода javascript.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Теперь, когда изменение в слоте бета-версии запущено, можно выполнить развертывание в режиме фокус-тестирования.

## <a name="validate-route-traffic-to-the-beta-app"></a>Проверка: маршрутизация трафика в бета-версию приложения
В этом разделе будет выполнена маршрутизация трафика в бета-версию приложения. Для наглядности вы выполните маршрутизацию значительной части пользовательского трафика. В реальности объем перенаправляемого трафика будет зависеть от конкретной ситуации. Например, если у вас сайт масштаба microsoft.com, для получения полезных данных вам потребуется менее&1; % от всего трафика.

1. В сеансе Git Shell выполните следующие команды для маршрутизации половины рабочего трафика в слот бета-версии:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

   Свойство `ReroutePercentage=50` указывает на то, что 50 % рабочего трафика будет перенаправлено на URL-адрес бета-версии приложения (определяется свойством `ActionHostName`).
2. Теперь перейдите по адресу http://ToDoApp*&lt;ваш-суффикс>*.azurewebsites.net. 50 % трафика теперь будет перенаправляться в слот бета-версии.
3. В ресурсе Application Insights примените к метрикам следующий фильтр: environment="beta".

   > [!NOTE]
   > Сохранив это отфильтрованное представление в избранном, вы сможете легко переключаться в обозревателе метрик между представлением рабочей версии и бета-версии.
   >
   >

Предположим, что в Application Insights вы увидите что-то подобное:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Эти значения демонстрируют не просто небольшое увеличение количества щелчков тегов `<li>`, но и резкий подъем активности в отношении тега `<li>`. Из этого можно заключить, что пользователи обнаружили новые интерактивные теги `<li>` и теперь обозначают все ранее завершенные задачи в приложении как выполненные.

На основе данных развертывания в режиме фокус-тестирования вы можете решить, что новый пользовательский интерфейс готов к выпуску.

## <a name="go-live-move-your-new-code-into-production"></a>Ввод в эксплуатацию: перенос нового кода в рабочую среду
Теперь вы готовы применить обновления в рабочей среде. У вас есть преимущество — вы уже знаете, что обновление проверено *перед* отправкой в рабочую среду. Теперь его можно с уверенностью развернуть. После внесения обновления в клиентское приложение AngularJS вам остается только проверить код на стороне клиента. Если вы внесли изменения во внутреннее приложение веб-API, эти изменения вы сможете проверить так же легко и просто.

1. В оболочке Git Shell удалите правило маршрутизации трафика, выполнив следующую команду:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()
2. Выполните команды Git:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master
3. Подождите несколько минут, пока новый код будет развернут в промежуточный слот, а затем запустите сайт http://ToDoApp*&lt;ваш_суффикс>*-staging.azurewebsites.net и убедитесь, что новое обновление активировано в промежуточном слоте. Помните, что главная ветвь разветвления связана с промежуточным слотом приложения.
4. Теперь переключите промежуточный слот в рабочую среду.

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>Сводка
Служба приложений Azure упрощает процесс тестирования приложений для клиентов малого и среднего бизнеса в рабочей среде — сценарий, который традиционно реализовывался в крупных организациях. Мы надеемся, что приведенная в этом учебнике информация поможет вам использовать службу приложений наряду с Application Insights для развертывании в режиме фокус-тестирования и реализации других сценариев тестирования в рабочей среде в рамках DevOps.

## <a name="more-resources"></a>Дополнительные ресурсы
* [Гибкая разработка программного обеспечения с помощью службы приложений Azure.](app-service-agile-software-development.md)
* [Настройка промежуточных сред для веб-приложений в службе приложений Azure](web-sites-staged-publishing.md)
* [Предсказуемое развертывание сложного приложения в Azure](app-service-deploy-complex-application-predictably.md)
* [Создание шаблонов диспетчера ресурсов Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [JSONLint — проверяющий элемент управления JSON](http://jsonlint.com/)
* [Ветвление Git — основные ветвления и слияния](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Вики-сайт проекта Kudu](https://github.com/projectkudu/kudu/wiki)



<!--HONumber=Jan17_HO3-->


