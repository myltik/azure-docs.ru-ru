<properties
	pageTitle="Прямое подключение компьютеров к Operational Insights"
	description="Компьютеры можно напрямую подключить к Operational Insights, установив на них агент Operational Insights."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/10/2015" 
	ms.author="banders"/>
# Прямое подключение компьютеров к Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Компьютеры можно напрямую подключить к Operational Insights, установив на них агент Operational Insights.

> [AZURE.TIP]Для виртуальных машин, запущенных в Azure, установите агент, следуя инструкциям в разделе [Анализ данных с серверов в Microsoft Azure](operational-insights-analyze-data-azure.md).

## Загрузка и установка агента
Чтобы загрузить и установить агент Operational Insights, выполните следующие действия.

### Загрузка файла установки агента
1. На портале Operations Insights на странице **Обзор** щелкните плитку **Параметры**. Откройте расположенную сверху вкладку **Подключенные источники**.![страницы «Параметры»](./media/operational-insights-direct-agent/direct-agent01.png)
2. В разделе **Подключить серверы напрямую (64-разрядная версия)** нажмите кнопку «Загрузить агент», чтобы загрузить файл установки.
3. Справа от **идентификатора рабочей области** нажмите значок копирования и вставьте идентификатор в Блокнот.
4. Справа от **первичного ключа** нажмите значок копирования и вставьте идентификатор в Блокнот.![страницы «Параметры»](./media/operational-insights-direct-agent/direct-agent02.png)

### Установка агента с помощью программы установки
1. Запустите программу установки, чтобы установить агент на компьютер, которым требуется управлять.
2. Выберите **Подключить агент к Microsoft Azure Operational Insights**, а затем нажмите кнопку **Далее**.
3. При появлении запроса введите **идентификатор рабочей области** и **первичный ключ**, скопированные в Блокнот в предыдущем шаге.

4. Нажмите кнопку **Далее**. Агент проверяет соединение с Operational Insights.
5. По завершении на **панели управления** появится **агент управления Майкрософт**.

### Установка агента с помощью командной строки
- Измените, а затем используйте следующий пример для установки агента с помощью командной строки.```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Настройка агента Microsoft Monitoring Agent (необязательно)
Используйте следующие сведения, чтобы включить агент для прямого взаимодействия со службой Microsoft Azure Operational Insights. После настройки агент будет зарегистрирован в службе агента и получит необходимые данные конфигурации и пакеты управления, содержащие сведения о решении.

После сбора данных с компьютеров, отслеживаемых агентом, количество этих компьютеров отобразится на портале Operational Insights на вкладке **Подключенные источники** в области **Параметры** раздела **Подключить серверы напрямую (64-разрядная версия)**. На портале Operational Insights можно просмотреть данные и сведения об оценке любого компьютера, который отправляет данные.

При необходимости агент можно отключить или включить с помощью командной строки или скрипта.

### Настройка агента
1. После установки **Microsoft Monitoring Agent** откройте **панель управления**.
2. Откройте агент Microsoft Monitoring Agent и перейдите на вкладку Azure Operational Insights.
3. Выберите **Подключение к Azure Operational Insights**.
4. В поле **Идентификатор рабочей области** вставьте идентификатор рабочей области, указанный на портале службы Operational Insights.
5. В поле **Ключ учетной записи** вставьте **первичный ключ**, указанный на портале службы Operational Insights, и нажмите кнопку **ОК**.

### Отключение агента
1. После установки агента откройте **панель управления**.
2. Откройте агент Microsoft Monitoring Agent и перейдите на вкладку **Azure Operational Insights**.
3. Снимите флажок **Подключение к Azure Operational Insights**.

### Включение агента с помощью командной строки или скрипта
- В следующем примере можно использовать Windows PowerShell или скрипт VB.

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## Настройка параметров прокси-сервера и брандмауэра (необязательно)
Если в вашей среде используются прокси-серверы или брандмауэры, ограничивающие доступ к Интернету, может потребоваться выполнить следующие действия, чтобы Operations Manager или агенты могли подключаться к службе Operational Insights.

- [Настройка параметров прокси-сервера и брандмауэра (необязательно)](operational-insights-proxy-firewall.md)

<!---HONumber=Sept15_HO3-->