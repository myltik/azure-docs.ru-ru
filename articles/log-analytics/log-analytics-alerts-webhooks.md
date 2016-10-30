<properties
   pageTitle="Пример webhook для оповещения в Log Analytics"
   description="Одно из действий, которое можно выполнить в ответ на оповещение Log Analytics, — это *webhook*. Оно позволяет вызвать внешний процесс посредством одного HTTP-запроса. В этой статье рассматривается пример создания действия webhook в оповещении Log Analytics с использованием Slack."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/27/2016"
   ms.author="bwren" />


# <a name="webhooks-in-log-analytics-alerts"></a>Действия webhook в оповещениях Log Analytics

Одно из действий, которое можно выполнить в ответ на оповещение [Log Analytics](log-analytics-alerts.md), — это *webhook*. Оно позволяет вызвать внешний процесс посредством одного HTTP-запроса.  Подробные сведения об оповещениях и действиях webhook см. в статье [Оповещения в Log Analytics](log-analytics-alerts.md).

В этой статье рассматривается пример создания действия webhook в оповещении Log Analytics с использованием службы обмена сообщениями Slack.

>[AZURE.NOTE] Для работы этим с примером требуется учетная запись Slack.  Ее можно получить бесплатно на сайте [slack.com](http://slack.com).

## <a name="step-1---enable-webhooks-in-slack"></a>Шаг 1. Включение действий webhook в Slack
2.  Войдите в службу Slack по адресу [slack.com](http://slack.com).
3.  Выберите канал в разделе **Channels** (Каналы) в левой области.  Это канал, в который будет отправлено сообщение.  Можно выбрать один из каналов по умолчанию, например **general** или **random**.  В рабочих сценариях чаще всего создается специальный канал, например **criticalservicealerts**. <br>

    ![Каналы Slack](media/log-analytics-alerts-webhooks/oms-webhooks01.png)

3. Щелкните **Add an app or custom integration** (Добавить приложение или настраиваемую интеграцию) для открытия каталога приложений.
3.  Введите *webhooks* в поле поиска, а затем выберите **Incoming WebHooks**(Входящие webhook). <br>

    ![Каналы Slack](media/log-analytics-alerts-webhooks/oms-webhooks02.png)

4.  Щелкните элемент **Install** (Установить) рядом с именем группы.
5.  Щелкните **Add Configuration**(Добавить конфигурацию).
6.  Выберите канал, который вы собираетесь использовать для этого примера, и щелкните **Add Incoming WebHooks integration**(Добавить интеграцию входящих webhook).  
6. Скопируйте значение **Webhook URL**(URL-адрес webhook).  Его потребуется вставить в конфигурацию оповещения. <br>

    ![Каналы Slack](media/log-analytics-alerts-webhooks/oms-webhooks05.png)

## <a name="step-2---create-alert-rule-in-log-analytics"></a>Шаг 2. Создание правила генерации оповещений в Log Analytics
1.  [Создайте правило генерации оповещений](log-analytics-alerts.md) со приведенными ниже параметрами.
    - Запрос: ```    Type=Event EventLevelName=error ```
    - Check for this alert every: 5 minutes (Проверять это оповещение: каждые 5 минут)
    - The number of results is: greater than 10 (Количество результатов: больше 10)
    - Over this time window: 60 minutes (За период: 60 минут)
    - Выберите **Yes** (Да) для **Webhook** и **No** (Нет) для других действий.
7. Вставьте URL-адрес Slack в поле **Webhook URL** (URL-адрес webhook).
8. Выберите параметр **Include custom JSON payload**(Включить настраиваемые полезные данные JSON).
9. Slack ожидает полезные данные в формате JSON с параметром *text*.  Это текст, который отображается в создаваемом сообщении.  Можно использовать один или несколько параметров оповещения с помощью символа *#* , как показано в следующем примере.

    ```
    {
    "text":"#alertrulename fired with #searchresultcount records which exceeds the over threshold of #thresholdvalue ."
    }
    ```

    ![пример полезных данных JSON](media/log-analytics-alerts-webhooks/oms-webhooks07.png)

9.  Щелкните **Save** (Сохранить), чтобы сохранить правило генерации оповещений.

10. Подождите достаточное время для создания оповещения и проверьте, появилось ли в Slack сообщение, аналогичное следующему.

    ![пример webhook в Slack](media/log-analytics-alerts-webhooks/oms-webhooks08.png)


### <a name="advanced-webhook-payload-for-slack"></a>Расширенные полезные данные webhook для Slack

Slack позволяет настроить входные сообщения в широких пределах. Дополнительные сведения см. в статье [Incoming Webhooks](https://api.slack.com/incoming-webhooks) (Входящие webhook) на веб-сайте Slack. Ниже приведены более сложные полезные данные для создания сообщения с форматированием.

    {
        "attachments": [
            {
                "title":"OMS Alerts Custom Payload",
                "fields": [
                    {
                        "title": "Alert Rule Name",
                        "value": "#alertrulename"},
                    {
                        "title": "Link To SearchResults",
                        "value": "<#linktosearchresults|OMS Search Results>"},
                    {
                        "title": "Search Interval",
                        "value": "#searchinterval"},
                    {
                        "title": "Threshold Operator",
                        "value": "#thresholdoperator"},
                    {
                        "title": "Threshold Value",
                        "value": "#thresholdvalue"}
                ],
                "color": "#F35A00"
            }
        ]
    }


При этом в Slack создается сообщение, аналогичное следующему.

![пример сообщения в Slack](media/log-analytics-alerts-webhooks/oms-webhooks09.png)

## <a name="summary"></a>Сводка

После создания этого правила генерации оповещений при каждом соблюдении условий в Slack должно отправляться сообщение.  

Это лишь один пример действия, которое можно создать в ответ на оповещение.  Можно создать действие webhook, которое вызывает другую внешнюю службу, действие Runbook, чтобы запустить модуль Runbook в службе автоматизации Azure, или действие электронной почты для отправки сообщения себе или другим получателям.   

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения, включая другие действия, см. в статье [Оповещения в Log Analytics](log-analytics-alerts.md).
- [В службе автоматизации Azure можно создать модули Runbook](../automation/automation-webhooks.md) , вызываемые из действия webhook.



<!--HONumber=Oct16_HO2-->


