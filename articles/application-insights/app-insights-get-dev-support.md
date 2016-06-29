<properties 
	pageTitle="Как получить техническую поддержку от команды разработчиков Application Insights | Microsoft Azure" 
	description="В случае необходимости специальной поддержки от команды разработчиков Application Insights, можно отправить сведения для получения поддержки следующим образом." 
	services="application-insights" 
    documentationCenter=""
	authors="alexbulankou" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="albulank"/>
	
# Как получить техническую поддержку от команды разработчиков Application Insights
	
Если у вас возникла техническая проблема с [Visual Studio Application Insights](app-insights-overview.md), вот как можно получить помощь.

## 1\. Проверка документов

* Пропали данные? Проверьте: [выборку](app-insights-sampling.md), [квоты и регулирование](app-insights-pricing.md).
* Устранение неполадок: [ASP.NET](app-insights-troubleshoot-faq.md) | [Java](app-insights-java-troubleshoot.md)

## 2\. Поиск на форумах

* [Форум MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
* [Stackoverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

## 3\. План поддержки Azure?

В некоторых ситуациях разработчикам нужно изучить вашу проблему подробнее.

Если у вас есть [план поддержки Microsoft Azure](https://azure.microsoft.com/support/plans/), вы можете [создать запрос в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## 4\. Обращение к команде Application Insights

Если у вас нет плана поддержки, наша группа разработчиков все равно всегда готова оказать вам (как и другим пользователям Application Insights) техническую поддержку в рамках подготовки к переходу к общедоступной версии. Представляем **новый вариант поддержки**: вы можете описать свою проблему, заполнив форму обратной связи на портале Azure, чтобы разработчик из команды Application Insights связался с вами.


1. На [портале Application Insights](https://portal.azure.com) щелкните смайлик в правом верхнем углу.  

    ![Кнопка обратной связи](./media/app-insights-get-dev-support/01.png)

2. В поле для комментария обязательно укажите **AppInsights** в первой строке, а ниже включите следующие сведения:

    ```

    AppInsights   
    ikey: <instrumentation key>   
    sdk: <SDK that you are using, including name and version>  
    issue: <please describe the problem you are having>

    ```   

    ![Диалоговое окно обратной связи](./media/app-insights-get-dev-support/02.png)

3. Установите флажок, указывающий, что вы разрешаете отправлять вам сообщения по электронной почте.

    ![Раздел отправки](./media/app-insights-get-dev-support/03.png)

Инженер из команды Application Insights свяжется с вами в ближайшее время. Так как эта услуга предусматривает наилучший возможный уровень исполнения, формальное соглашение об уровне обслуживания не предоставляется на этом этапе.

<!---HONumber=AcomDC_0615_2016-->