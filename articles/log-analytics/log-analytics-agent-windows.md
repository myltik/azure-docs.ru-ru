---
title: Подключение компьютеров Windows к Azure Log Analytics | Документация Майкрософт
description: В этой статье объясняется, как с помощью Microsoft Monitoring Agent (MMA) подключить компьютеры, размещенные в облаках или в локальной среде, к Log Analytics.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: magoedte
ms.openlocfilehash: 778810001952daf9ac63a7f1f880b05234549965
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/16/2018
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Подключение компьютеров Windows к службе Log Analytics в Azure

Чтобы отслеживать виртуальные машины или физические компьютеры и управлять ими в локальном центре обработки данных или другой облачной среде с помощью Log Analytics, разверните Microsoft Monitoring Agent и настройте его таким образом, чтобы он отправлял отчеты в одну или несколько рабочих областей Log Analytics.  Агент также поддерживает гибридную рабочую роль Runbook для службы автоматизации Azure.  

На отслеживаемом компьютере Windows агент отображается как служба Microsoft Monitoring Agent. Служба Microsoft Monitoring Agent собирает события из файлов журнала, журнала событий Windows, данных о производительности и других данных телеметрии. Даже если агент не может взаимодействовать со службой Log Analytics, в которую он отправляет отчеты, он продолжает запускать и добавлять в очередь собранные данные на диске отслеживаемого компьютера. При восстановлении подключения служба Microsoft Monitoring Agent отправляет собранные данные в службу.

Агент можно установить с помощью одного из указанных ниже методов. Чтобы установить разные типы компьютеров соответствующим образом, в большинстве установок используется сочетание этих методов.  Подробнее об использовании каждого метода рассказывается далее в этой статье.

* Установка вручную. Установка выполняется вручную на компьютере с помощью мастера установки из командной строки. Или выполняется развертывание с помощью имеющегося инструмента распространения программного обеспечения.
* Настройка требуемого состояния службы автоматизации Azure (DSC). Использование DSC в службе автоматизации Azure с помощью сценария для компьютеров Windows, развернутых в вашей среде.  
* Сценарий PowerShell.
* Шаблон Resource Manager для локальных виртуальных машин под управлением Windows в Azure Stack.  

Чтобы узнать о сетевых и системных требованиях для развертывания агента Windows, см. описание [необходимых компонентов для компьютеров Windows](log-analytics-concept-hybrid.md#prerequisites).

## <a name="obtain-workspace-id-and-key"></a>Получение идентификатора и ключа рабочей области
Перед установкой Microsoft Monitoring Agent для Windows требуется получить идентификатор и ключ для рабочей области Log Analytics.  При любом методе установки эта информация необходима для правильной настройки агента и обеспечения его взаимодействия с Log Analytics в Azure для коммерческих организаций и облаке для государственных организаций США.  

1. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
2. В списке рабочих областей Log Analytics выберите ту, в которую агент должен отправлять отчеты.
3. Выберите **Дополнительные параметры**.<br><br> ![Дополнительные параметры Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Выберите **Подключенные источники**, а затем выберите **Серверы с Windows**.   
5. Скопируйте **идентификатор рабочей области** и **первичный ключ** и вставьте их в удобный для вас редактор.    
   
## <a name="install-the-agent-using-setup-wizard"></a>Установка агента с помощью мастера установки
Ниже приведены инструкции по установке и настройке агента для Log Analytics в Azure и облаке Azure для государственных организаций с помощью мастера установки для Microsoft Monitoring Agent на компьютере.  

1. В рабочей области Log Analyics на ранее открытой странице **Серверы Windows** выберите соответствующую версию **Скачать агент для Windows** для скачивания в зависимости от архитектуры процессора, на котором выполняется операционная система Windows.   
2. Запустите программу установки, чтобы установить агент на компьютере.
2. На странице **приветствия** нажмите кнопку **Далее**.
3. На странице **Условия лицензии** прочтите лицензию и нажмите кнопку **Принимаю**.
4. На странице **Папка назначения** измените или оставьте папку установки по умолчанию и нажмите кнопку **Далее**.
5. На странице **Параметры установки агента** выберите подключение агента к Azure Log Analytics (OMS) и нажмите кнопку **Далее**.   
6. На странице **Azure Log Analytics** выполните следующее.
   1. Вставьте **идентификатор рабочей области** и **ключ рабочей области (первичный ключ)**, скопированные ранее.  Если компьютер должен передавать данные в рабочую область Log Analytics в облаке Azure для государственных организаций, выберите  **	Azure для государственных организаций США** из раскрывающегося списка **Облако Azure**.  
   2. Если компьютер должен обмениваться данными со службой Log Analytics через прокси-сервер, щелкните **Дополнительно** и укажите URL-адрес и номер порта прокси-сервера.  Если для доступа к прокси-серверу требуется аутентификация, введите имя пользователя и пароль для аутентификации на прокси-сервере, затем нажмите кнопку **Далее**.  
7. Нажмите кнопку **Далее** после завершения ввода необходимых параметров конфигурации.<br><br> ![Вставка идентификатора рабочей области и первичного ключа](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. На странице **Готовность к установке** просмотрите выбранные параметры и нажмите кнопку **Установить**.
9. На странице **Настройка успешно завершена** нажмите кнопку **Готово**.

После завершения установки на **панели управления** появится **Microsoft Monitoring Agent**. Чтобы убедиться, что он отправляет отчеты в Log Analytics, ознакомьтесь с разделом [Проверка подключения агента к Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Установка агента с помощью командной строки
Скачанный файл агента — это автономный пакет установки.  В нем содержатся программа установки агента и вспомогательные файлы. Чтобы установить агент с помощью командной строки, содержимое этого пакета нужно извлечь. Ниже описано, как это сделать.    

>[!NOTE]
>Для обновления агента необходимо использовать API сценариев службы Log Analytics. Дополнительные сведения см. в статье [Обслуживание агента Log Analytics для Windows и Linux и управление им](log-analytics-agent-manage.md).

В следующей таблице описаны определенные параметры Log Analytics, поддерживаемые программой установки агента, а также при развертывании с помощью Automation DSC.

|Параметры MMA                   |Заметки         |
|---------------------------------------|--------------|
| NOAPM=1                               | Необязательный параметр. Установка агента без мониторинга производительности приложений .NET.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 — настройка агента для передачи отчетов в рабочую область.                |
|OPINSIGHTS_WORKSPACE_ID                | Идентификатор добавляемой рабочей области (GUID).                    |
|OPINSIGHTS_WORKSPACE_KEY               | Ключ рабочей области, используемый для первоначальной аутентификации в рабочей области. |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Укажите облачную среду, в которой находится рабочая область. <br> 0 — коммерческое облако Azure (по умолчанию). <br> 1 — Azure для государственных организаций. |
|OPINSIGHTS_PROXY_URL               | Универсальный код ресурса (URI) для прокси-сервера. |
|OPINSIGHTS_PROXY_USERNAME               | Имя пользователя для доступа к прокси-серверу после аутентификации. |
|OPINSIGHTS_PROXY_PASSWORD               | Пароль для доступа к прокси-серверу после аутентификации. |

1. Чтобы извлечь файлы установки агента из командной строки с повышенными привилегиями, запустите файл `MMASetup-<platform>.exe /c`. После этого вы сможете выбрать путь для извлечения файлов.  Кроме того, путь можно указать, передав аргументы `MMASetup-<platform>.exe /c /t:<Path>`.  
2. Чтобы автоматически установить агент и настроить его для отправки отчетов в рабочую область коммерческого облака Azure, из папки, в которую вы извлекли файлы установки, выполните следующую команду: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

   Чтобы настроить агент для отправки отчетов в облако Azure для государственных организаций США, выполните следующую команду: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
    ```

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Установка агента с помощью DSC в службе автоматизации Azure

Вы можете использовать следующий пример сценария для установки агента с помощью DSC службы автоматизации Azure.   Если у вас нет учетной записи автоматизации, просмотрите статью [Начало работы со службой автоматизации Azure](../automation/automation-offering-get-started.md), чтобы ознакомиться с необходимыми требованиями и шагами для ее создания перед использованием Automation DSC.  Если вы не знакомы с Automation DSC, просмотрите статью [Приступая к работе с DSC службы автоматизации Azure](../automation/automation-dsc-getting-started.md).

В указанном ниже примере выполняется установка 64-разрядного агента, идентифицируемого значением `URI`. Вы также можете использовать 32-разрядную версию, заменив значение универсального кода ресурса (URI). URI для обеих версий:

- Агент 64-разрядной версии Windows — https://go.microsoft.com/fwlink/?LinkId=828603.
- Агент 32-разрядной версии Windows — https://go.microsoft.com/fwlink/?LinkId=828604.


>[!NOTE]
>Эти примеры процедуры и сценария не поддерживают обновление агента, развернутого на компьютере Windows.

32- и 64-разрядные версии пакета агента имеют разные коды продукта. Новые выпущенные версии также имеют уникальное значение.  Код продукта — это глобальный уникальный идентификатор, который представляет собой основной идентификатор приложения или продукта. Он представляется свойством **ProductCode** установщика Windows.  Значение `ProductId value` в сценарии **MMAgent.ps1** должно соответствовать коду продукта в пакете установщика 32- или 64-разрядного агента.

Чтобы извлечь код продукта из пакета установщика агента напрямую, вы можете воспользоваться Orca.exe (компонент пакета разработки программного обеспечения для Windows), который можно найти на странице [компонентов Windows SDK для разработчиков установщика Windows](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx). Кроме того, вы можете использовать PowerShell с [примером сценария](http://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/), написанного специалистом с рангом Microsoft Valuable Professional (MVP).  В обоих случаях сначала нужно извлечь файл**MOMagent.msi** из пакета установки MMASetup.  Эта процедура рассматривается выше в разделе [Установка агента с помощью командной строки](#install-the-agent-using-the-command-line).  

1. Импортируйте модуль DSC xPSDesiredStateConfiguration со страницы [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) в службу автоматизации Azure.  
2.  Создайте в службе автоматизации Azure ресурсы-контейнеры для переменных *OPSINSIGHTS_WS_ID* и *OPSINSIGHTS_WS_KEY*. В качестве значения параметра *OPSINSIGHTS_WS_ID* укажите идентификатор рабочей области Log Analytics, а для параметра *OPSINSIGHTS_WS_KEY* — первичный ключ рабочей области.
3.  Скопируйте сценарий и сохраните его как файл MMAgent.ps1.

    ```PowerShell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource –ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. [Переместите сценарий настройки MMAgent.ps1](../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) в учетную запись автоматизации. 
5. [Назначьте компьютер Windows или узел](../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-dsc) конфигурации. В течение 15 минут узел проверит свою конфигурацию, а агент будет помещен в узел.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Проверка подключения к Log Analytics

По завершении установки агента вы можете проверить, успешно ли он подключен и отправляет ли он отчеты. Есть два способа.  

На **панели управления** компьютера найдите элемент **Microsoft Monitoring Agent**.  Выберите его, и на вкладке **Azure Log Analytics (OMS)** агент должен отобразить следующее сообщение: **Microsoft Monitoring Agent успешно подключен к службе Microsoft Operations Management Suite.**<br><br> ![Состояние подключения MMA к Log Analytics](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

Вы также можете выполнить простой поиск по журналам на портале Azure.  

1. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.  
2. На странице рабочей области Log Analytics выберите целевую рабочую область, а затем щелкните плитку **Поиск по журналу**. 
2. На панели поиска журналов в поле поискового запроса введите следующее:  

    ```
    search * 
    | where Type == "Heartbeat" 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

В возвращаемых результатах поиска должны отображаться записи пульса для компьютера, указывающие, что агент успешно подключен и отправляет отчеты в службу.   

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об управлении агентом во время его жизненного цикла развертывания на ваших компьютерах см. в статье [Обслуживание агента Log Analytics для Windows и Linux и управление им](log-analytics-agent-manage.md).  