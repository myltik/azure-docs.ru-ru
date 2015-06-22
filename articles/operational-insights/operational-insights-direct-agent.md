<properties 
   pageTitle="connect-scom" 
   description="Прямое подключение компьютеров к Operational Insights" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# Прямое подключение компьютеров к Operational Insights 

Компьютеры можно напрямую подключить к Operational Insights, установив на них агент Operational Insights. 


##Загрузка, установка и настройка агента
###Загрузка файла установки агента
1. На **портале Operations Insights** на странице **Обзор** щелкните **Серверы и использование**.
1. В разделе **Напрямую подключенные серверы** щелкните **Настроить**.
1. Рядом с полем **Добавить агенты** щелкните ссылку на агент, чтобы загрузить файл установки.
1. В поле **Первичный ключ рабочей области** выберите ключ и скопируйте его (CTRL+C).


### Установка агента с помощью программы установки
1. Запустите программу установки, чтобы установить агент на компьютер, которым требуется управлять.
1. Выберите **Подключить агент к Microsoft Azure Operational Insights**, а затем нажмите кнопку **Далее**.
1. При появлении запроса введите данные, скопированные на шаге 4.
1. По завершении **Microsoft Management Agent** появится на **панели управления**.

### Установка агента с помощью командной строки
Измените, а затем используйте следующий пример для установки агента с помощью командной строки.

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Перенастройка агента Microsoft Monitoring Agent (необязательно)
Используйте следующие сведения, чтобы включить агент для прямого взаимодействия со службой Microsoft Azure Operational Insights. После настройки агент будет зарегистрирован в службе агента и получит необходимые данные конфигурации и пакеты управления, содержащие сведения о пакете аналитики.

После сбора данных с компьютеров, отслеживаемых агентом, количество этих компьютеров появится на портале Operational Insights на странице "Использование" в разделе **Напрямую подключенные агенты**. На портале Operational Insights можно просмотреть данные и сведения об оценке любого компьютера, который отправляет данные.

При необходимости агент можно отключить или включить с помощью командной строки или скрипта.

### Настройка агента
- После установки **Microsoft Monitoring Agent** откройте **панель управления**.
- Откройте агент Microsoft Monitoring Agent и перейдите на вкладку Azure Operational Insights.
- Выберите **Подключение к Azure Operational Insights**.
- В поле **Идентификатор рабочей области** введите идентификатор рабочей области, указанный на портале службы Operational Insights.
- В поле "Ключ учетной записи" вставьте **первичный ключ рабочей области**, скопированный при установке агента, а затем нажмите кнопку **OK**.

### Отключение агента
- После установки агента откройте **панель управления**.
- Откройте агент Microsoft Monitoring Agent и перейдите на вкладку **Azure Operational Insights**.
- Снимите флажок **Подключение к Azure Operational Insights**.

### Включение агента с помощью командной строки или скрипта
В следующем примере можно использовать Windows PowerShell или скрипт VB.

	$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
	$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
	$healthServiceSettings.ReloadConfiguration()




### Настройка параметров прокси-сервера и брандмауэра (необязательно)
Если в вашей среде используются прокси-серверы или брандмауэры, ограничивающие доступ к Интернету, может потребоваться выполнить следующие процедуры, чтобы Operations Manager и (или) агенты могли подключаться к службе Operational Insights. 



- [Настройка параметров прокси-сервера и брандмауэра (необязательно)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## Похожий контент

- [Запись блога: Прямое подключение серверов к Operational Insights](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [Запись блога: Включение Operational Insights для виртуальных машин Azure](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52--> 