<properties 
   pageTitle="Подключение к Operational Insights из System Center Operations Manager" 
   description="Узнайте, как подключиться к службе Operational Insights с помощью Operations Manager." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Подключение к Operational Insights из System Center Operations Manager 

Службу Operational Insights можно подключить к существующей среде System Center Operations Manager. Это позволит использовать существующие агенты Operations Manager в качестве агентов службы Operational Insights.

 >[AZURE.NOTE] Поддержка службы Operational Insights доступна в Operations Manager 2012 с пакетом обновления 1 (SP1) и с накопительным пакетом обновления 6 (UR6) и в Operations Manager 2012 R2 с накопительным пакетом обновления 2 (UR2). Поддержка прокси-сервера была добавлена в SCOM 2012 с пакетом обновления 1 (SP1) и накопительным пакетом обновления 7 (UR7) и в SCOM 2012 R2 с накопительным пакетом обновления 3 (UR3).

## Подключение SCOM к службе Operational Insights и добавление агентов

1. В консоли Operations Manager щелкните **Администрирование**.

2. Разверните узел **Operational Insights** и щелкните **Подключение к Operational Insights**.

3. Щелкните ссылку **Зарегистрироваться в Operational Insights** и следуйте инструкциям на экране. 

4. После завершения работы мастера регистрации нажмите кнопку **Добавить компьютер или группу**.

5. В диалоговом окне **Поиск компьютера** можно выполнить поиск компьютеров или групп, отслеживаемых с помощью Operations Manager. Выберите компьютеры или группы для использования в службе Operational Insights, нажмите кнопку **Добавить**, а затем кнопку **ОК**.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Дальнейшие действия

[Настройка параметров прокси-сервера и брандмауэра (необязательно)](https://msdn.microsoft.com/library/azure/dn884643.aspx)


<!--HONumber=52-->