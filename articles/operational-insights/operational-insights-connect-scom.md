<properties
   pageTitle="Подключение к Operational Insights из System Center Operations Manager"
   description="Узнайте, как подключиться к службе Operational Insights с помощью Operations Manager."
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# Подключение к Operational Insights из System Center Operations Manager


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Службу Operational Insights можно подключить к существующей среде System Center Operations Manager. Это позволит использовать существующие агенты Operations Manager для сбора данных. Дополнительные сведения об использовании Operations Manager с Operational Insights см. в разделе [Факторы, которые необходимо учитывать при использовании Operations Manager с Operational Insights](operational-insights-operations-manager.md).

Если Operations Manager используется для мониторинга следующих рабочих нагрузок, для них потребуется задать учетные записи запуска от имени в Operations Manager. Дополнительные сведения о настройке учетных записей см. в разделе [Факторы, которые необходимо учитывать при использовании Operations Manager с Operational Insights](operational-insights-operations-manager.md).

- Оценка SQL
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]Поддержка службы Operational Insights доступна в Operations Manager 2012 с пакетом обновления 1 (SP1) и с накопительным пакетом обновления 6 (UR6) и в Operations Manager 2012 R2 с накопительным пакетом обновления 2 (UR2). Поддержка прокси-сервера была добавлена в System Center Operations Manager 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 7 (UR7) и в System Center Operations Manager 2012 R2 с накопительным пакетом обновления 3(UR3).

## Подключение Operations Manager к Operational Insights и добавление агентов

1. В консоли Operations Manager щелкните **Администрирование**.

2. Разверните узел **Operational Insights** и щелкните **Operational Insights Connection**.

3. Щелкните ссылку **Зарегистрироваться в Operational Insights** и следуйте инструкциям на экране.

4. После завершения работы мастера регистрации нажмите кнопку **Добавить компьютер/группу**. ![Добавление компьютера или группы в Operations Manager](./media/operational-insights-connect-scom/om01.png)
5. В диалоговом окне **Поиск компьютера** можно выполнить поиск компьютеров или групп, отслеживаемых с помощью Operations Manager. Выберите компьютеры или группы для подключения к Operational Insights, щелкните **Добавить**, а затем нажмите кнопку **ОК**. ![Добавление компьютеров в Operations Manager](./media/operational-insights-connect-scom/om02.png)
## Дальнейшие действия

[Настройка параметров прокси-сервера и брандмауэра (необязательно)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO4-->