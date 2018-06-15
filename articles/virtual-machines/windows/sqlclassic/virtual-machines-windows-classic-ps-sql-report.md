---
title: Использование PowerShell для создания виртуальных машин с помощью сервера отчетов, работающего в основном режиме | Документация Майкрософт
description: 'В этом разделе описывается процесс развертывания и настройки сервера отчетов собственного режима служб Reporting Services SQL Server на виртуальной машине Azure. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: edfae3a56bc13e4c41a1676bfc0f4e8cf4cd9d30
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31425084"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Использование PowerShell для создания виртуальной машины Azure с помощью сервера отчетов, работающего в собственном режиме
> [!IMPORTANT] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.

В этом разделе описывается процесс развертывания и настройки сервера отчетов собственного режима служб Reporting Services SQL Server на виртуальной машине Azure. Процедуры, описанные в этом документе, сочетают в себе выполняемые вручную действия для создания виртуальной машины и использование сценария Windows PowerShell для настройки служб Reporting Services на виртуальной машине. Сценарий конфигурации включает в себя открытие порта брандмауэра для протоколов HTTP и HTTPS.

> [!NOTE]
> Если протокол **HTTPS** на сервере отчетов не требуется, **пропустите шаг 2**.
> 
> После создания виртуальной машины в шаге 1 перейдите в раздел "Использование сценария для настройки сервера отчетов и HTTP". После выполнения сценария сервер отчетов будет готов к использованию.

## <a name="prerequisites-and-assumptions"></a>Предварительные требования и предположения
* **Подписка Azure**: проверьте, какое количество ядер доступно в вашей подписке Azure. При создании виртуальной машины рекомендуемого размера **A3** необходимо **4** доступных ядра. При использовании виртуальной машины размера **A2** необходимо **2** доступных ядра.
  
  * Чтобы проверить ограничение для числа ядер в подписке, на портале Azure щелкните элемент "Параметры" на левой панели, а затем выберите "Использование" в меню вверху.
  * Для увеличения квоты на ядра обратитесь в [службу поддержки Azure](https://azure.microsoft.com/support/options/). Дополнительные сведения о размере виртуальной машины см. в разделе [Размеры виртуальных машин в Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Сценарии Windows PowerShell**: в данном разделе предполагается, что вы обладаете базовыми навыками работы с Windows PowerShell. Дополнительные сведения об использовании Windows PowerShell см. в следующих разделах:
  
  * [Запуск Windows PowerShell в Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
  * [Приступая к работе с Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Шаг 1. Подготовка виртуальной машины Azure
1. Перейдите на портал Azure.
2. Щелкните элемент **Виртуальные машины** на левой панели.
   
    ![виртуальные машины microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Нажмите кнопку **Создать**.
   
    ![кнопка создания](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Щелкните **Из коллекции**.
   
    ![новая виртуальная машина из коллекции](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Выберите **SQL Server 2014 RTM Standard — Windows Server 2012 R2** , а затем щелкните стрелку, чтобы продолжить.
   
    ![Далее](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Если вам необходима функция управляемых данными подписок в службах Reporting Services, выберите **SQL Server 2014 RTM Enterprise — Windows Server 2012 R2**. Дополнительные сведения о выпусках SQL Server и поддержке различных функций см. на странице [Функции, поддерживаемые различными выпусками SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. На странице **Конфигурация виртуальной машины** измените следующие поля:
   
   * Если в поле **Дата выпуска версии**указано несколько значений, выберите самую последнюю версию.
   * **Имя виртуальной машины**: имя машины также используется на следующей странице конфигурации в качестве DNS-имени облачной службы по умолчанию. Это DNS-имя должно быть уникальным в пределах службы Azure. Рекомендуется настроить виртуальную машину с таким именем компьютера, которое описывает назначение данной виртуальной машины. Например, ssrsnativecloud.
   * **Уровень**: «Стандартный».
   * **Размер: A3**. Это рекомендуемый размер виртуальной машины для рабочих нагрузок SQL Server. Если виртуальная машина используется только в качестве сервера отчетов, размера A2 достаточно, если только сервер отчетов не обрабатывает большой объем данных. Для получения информации о ценах на виртуальные машины см. страницу [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Имя нового пользователя**. Указанное имя используется как имя администратора виртуальной машины.
   * **Новый пароль** и **Подтверждение**. Этот пароль используется для учетной записи администратора, поэтому рекомендуется использовать надежный пароль.
   * Нажмите кнопку **Далее**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. На следующей странице измените следующие поля:
   
   * **Облачная служба**: выберите **Создать новую облачную службу**.
   * **DNS-имя облачной службы**. Это общедоступное DNS-имя облачной службы, связанной с виртуальной машиной. По умолчанию используется имя, введенное в качестве имени виртуальной машины. Если в последующих разделах вы создадите доверенный SSL-сертификат, то в качестве значения параметра **Кому выдано** будет использоваться DNS-имя.
   * **Регион, территориальная группа, виртуальная сеть**. Выберите регион, ближайший к конечным пользователям.
   * **Учетная запись хранения**. Используйте автоматически созданную учетную запись хранения.
   * **Группа доступности**: нет.
   * **Конечные точки**. Сохраните конечные точки **Удаленный рабочий стол** и **PowerShell**, а затем добавьте конечную точку HTTP или HTTPS в зависимости от вашей среды.
     
     * **HTTP**. Общий и частный порты по умолчанию имеют номер **80**. Обратите внимание, что при использовании частного порта, отличного от 80, следует изменить параметр **$HTTPport = 80** в сценарии HTTP.
     * **HTTPS**. Общий и частный порты по умолчанию имеют номер **443**. Из соображений безопасности рекомендуется изменить частный порт и настроить брандмауэр и сервер отчетов на использование этого частного порта. Дополнительную информацию о конечных точках см. в статье [Настройка связи с виртуальной машиной](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Обратите внимание, что при использовании порта, отличного от 443, следует изменить параметр **$HTTPsport = 443** в сценарии HTTPS.
   * Нажмите кнопку «Далее». ![Далее](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. На последней странице мастера оставьте параметр по умолчанию **Установить агент ВМ**. В действиях, описанных в этом разделе, данный агент виртуальной машины не используется, но, если планируется сохранить эту виртуальную машину, агент и расширения позволят расширить ее возможности.  Дополнительные сведения об агенте ВМ см. в статье [VM Agent and Extensions – Part 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/) (Агент виртуальной машины и расширения. Часть 1). Одним из установленных и запущенных расширений по умолчанию является BGINFO, которое отображает на рабочем столе виртуальной машины системные данные, такие как внутренний IP-адрес и свободное место на диске.
9. Щелкните «Готово». ![ОК](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. Параметр **Состояние** виртуальной машины получает значение **Запуск (Подготовка)** во время процесса подготовки и значение **Выполняется**, когда виртуальная машина подготовлена и готова к использованию.

## <a name="step-2-create-a-server-certificate"></a>Шаг 2. Создание сертификата сервера
> [!NOTE]
> Если протокол HTTPS на сервере отчетов не требуется, можно **пропустить шаг 2** и перейти к разделу **Использование сценария для настройки сервера отчетов и HTTP**. Воспользуйтесь сценарием HTTP, чтобы быстро настроить сервер отчетов и подготовить его к работе.

Для использования протокола HTTPS на виртуальной машине необходим доверенный SSL-сертификат. В зависимости от сценария можно использовать один из двух следующих способов:

* Допустимый SSL-сертификат, выданный центром сертификации (ЦС) и являющийся доверенным для корпорации Майкрософт. Сертификаты корневого ЦС должны распространяться по программе корневых сертификатов корпорации Майкрософт. Дополнительные сведения об этой программе см. на страницах [Программа корневых SSL-сертификатов Майкрософт (участвующие ЦС) Windows и Windows Phone 8](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) и [Общие сведения о программе корневых сертификатов корпорации Майкрософт](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Самозаверяющий сертификат. Самозаверяющие сертификаты не рекомендуется использовать для рабочих сред.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Использование сертификата, созданного доверенным центром сертификации (ЦС)
1. **Запросите сертификат сервера для веб-сайта из центра сертификации**. 
   
    С помощью мастера сертификатов веб-сервера можно создать файл запроса на сертификат (Certreq.txt), который отправляется в центр сертификации, или создать запрос для сетевого центра сертификации. Например, службы сертификатов Майкрософт в Windows Server 2012. В зависимости от уровня контроля идентификации, обеспечиваемого сертификатом сервера, на утверждение запроса и отправку вам файла сертификата центру сертификации может потребоваться от нескольких дней до нескольких месяцев. 
   
    Дополнительные сведения о запросе сертификатов сервера см. в следующих разделах: 
   
   * Использование [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx)
   * Средства безопасности для администрирования Windows Server 2012.
     
     [Средства безопасности для администрирования Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > Значение в поле **Кому выдан** доверенного SSL-сертификата должно совпадать со значением **DNS-имени облачной службы**, заданного для новой виртуальной машины.

2. **Установите сертификат сервера на веб-сервере**. Веб-сервер в этом случае представляет собой виртуальную машину, на которой размещен сервер отчетов, а веб-сайт создается позднее при настройке служб Reporting Services. Дополнительные сведения об установке сертификата сервера на веб-сервере с помощью оснастки сертификатов MMC см. в разделе [Установка сертификата сервера](https://technet.microsoft.com/library/cc740068).
   
    Если вы хотите использовать приведенный в этом разделе сценарий для настройки сервера отчетов, в качестве параметра сценария требуется указать значение сертификата **Отпечаток** . Сведения о том, как получить отпечаток сертификата, см. в следующем разделе.
3. Назначьте сертификат сервера серверу отчетов. Назначение завершается в следующем разделе после настройки сервера отчетов.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Использование самозаверяющего сертификата виртуальных машин
Самозаверяющий сертификат был создан на виртуальной машине при ее подготовке. Имя этого сертификата совпадает с DNS-именем виртуальной машины. Во избежание ошибок сертификата необходимо, чтобы сертификат был доверенным как на самой виртуальной машине, а также для всех пользователей сайта.

1. Чтобы обеспечить доверие корневому ЦС сертификата на локальной виртуальной машине, добавьте этот сертификат в список **Доверенные корневые центры сертификации**. Ниже приведены сводные сведения о необходимых действиях. Подробные инструкции по обеспечению доверия к ЦС см. в разделе [Установка сертификата сервера](https://technet.microsoft.com/library/cc740068).
   
   1. На портале Azure выберите виртуальную машину и щелкните команду подключения. В зависимости от конфигурации браузера вам может быть предложено сохранить RDP-файл для подключения к виртуальной машине.
      
       ![подключение к виртуальной машине Azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Используйте имя виртуальной машины, имя пользователя и пароль, которые были настроены при создании виртуальной машины. 
      
       Например, на следующем рисунке имя виртуальной машины — **ssrsnativecloud**, а имя пользователя — **testuser**.
      
       ![имя для входа включает в себя имя виртуальной машины](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Запустите mmc.exe. Дополнительные сведения см. в разделе [Практическое руководство. Просмотр сертификатов с помощью оснастки MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. В меню **Файл** консольного приложения добавьте оснастку **Сертификаты**, при отображении запроса выберите **Учетная запись компьютера** и нажмите кнопку **Далее**.
   4. Выберите нужный **локальный компьютер** и нажмите кнопку **Готово**.
   5. Нажмите кнопку **ОК**, разверните узлы **Сертификаты — Личные** и щелкните элемент **Сертификаты**. Имя сертификата совпадает с DNS-именем виртуальной машины и заканчивается на **cloudapp.net**. Щелкните правой кнопкой мыши имя сертификата и выберите пункт **Копировать**.
   6. Разверните узел **Доверенные корневые центры сертификации**, щелкните правой кнопкой мыши элемент **Сертификаты** и выберите пункт **Вставить**.
   7. Для проверки дважды щелкните имя сертификата в области **Доверенные корневые центры сертификации** и убедитесь, что ошибки отсутствуют, а сам сертификат отображается. Если вы хотите использовать приведенный в этом разделе сценарий HTTPS для настройки сервера отчетов, в качестве параметра сценария требуется указать значение сертификата **Отпечаток** . **Для получения значения отпечатка**воспользуйтесь приведенной ниже процедурой. Существует также пример PowerShell по извлечению отпечатка в разделе [Использование сценария для настройки сервера отчетов и HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Дважды щелкните имя сертификата, например ssrsnativecloud.cloudapp.net.
      2. Откройте вкладку **Сведения** .
      3. Щелкните **Отпечаток**. Значение отпечатка появится в поле сведений, например ‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Скопируйте отпечаток и сохраните значение для использования в дальнейшем либо отредактируйте сценарий прямо сейчас.
      5. (*) Перед выполнением сценария удалите пробелы между парами значений. Например, указанный выше отпечаток должен иметь вид ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Назначьте сертификат сервера серверу отчетов. Назначение завершается в следующем разделе после настройки сервера отчетов.

Если вы используете самозаверяющий SSL-сертификат, имя сертификата уже соответствует имени узла виртуальной машины. Таким образом, DNS-имя машины уже зарегистрировано на глобальном уровне и может использоваться с любого клиента.

## <a name="step-3-configure-the-report-server"></a>Шаг 3. Настройка сервера отчетов
В этом разделе описывается настройка виртуальной машины в качестве сервера отчетов служб Reporting Services в собственном режиме. Для настройки сервера отчетов можно использовать один из следующих методов:

* Использование сценария для настройки сервера отчетов
* Использование диспетчера конфигурации для настройки сервера отчетов.

Более подробные инструкции см. в разделе [Подключение к виртуальной машине и запуск диспетчера конфигурации служб Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Примечание о проверке подлинности.** Проверка подлинности Windows является рекомендуемым способом проверки подлинности и используется по умолчанию для служб Reporting Services. Только пользователи, которые настроены на виртуальной машине, могут осуществлять доступ к службам Reporting Services и назначаться ролям служб Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Использование сценария для настройки сервера отчетов и HTTP
Чтобы использовать сценарий Windows PowerShell для настройки сервера отчетов, выполните следующие действия. Данная процедура настройки содержит указания для HTTP, но не для HTTPS:

1. На портале Azure выберите виртуальную машину и щелкните команду подключения. В зависимости от конфигурации браузера вам может быть предложено сохранить RDP-файл для подключения к виртуальной машине.
   
    ![подключение к виртуальной машине Azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Используйте имя виртуальной машины, имя пользователя и пароль, которые были настроены при создании виртуальной машины. 
   
    Например, на следующем рисунке имя виртуальной машины — **ssrsnativecloud**, а имя пользователя — **testuser**.
   
    ![имя для входа включает в себя имя виртуальной машины](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. На виртуальной машине откройте **интегрированную среду сценариев Windows PowerShell** с правами администратора. Интегрированная среда сценариев Windows PowerShell устанавливается по умолчанию в Windows Server 2012. Рекомендуется использовать интегрированную среду сценариев вместо стандартного окна Windows PowerShell, чтобы можно было вставить сценарий в эту среду, отредактировать его и выполнить.
3. В интегрированной среде сценариев Windows PowerShell откройте меню **Вид** и выберите **Показать область сценариев**.
4. Скопируйте следующий сценарий и вставьте его в области сценариев интегрированной среды сценариев Windows PowerShell.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Если вы создали виртуальную машину с HTTP-портом, отличным от 80, измените параметр $HTTPport = 80.
6. Сценарий настроен для служб Reporting Services. Если вы хотите выполнить сценарий для служб Reporting Services, измените версию в пути пространства имен на значение v11 в операторе Get-WmiObject.
7. Выполните скрипт.

**Проверка**. Чтобы проверить работу базовых функций сервера отчетов, см. раздел [Проверка конфигурации](#verify-the-configuration) далее в этой статье.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Использование сценария для настройки сервера отчетов и HTTPS
Чтобы использовать Windows PowerShell для настройки сервера отчетов, выполните следующие действия. Данная процедура настройки содержит указания для HTTPS, но не для HTTP:

1. На портале Azure выберите виртуальную машину и щелкните команду подключения. В зависимости от конфигурации браузера вам может быть предложено сохранить RDP-файл для подключения к виртуальной машине.
   
    ![подключение к виртуальной машине Azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Используйте имя виртуальной машины, имя пользователя и пароль, которые были настроены при создании виртуальной машины. 
   
    Например, на следующем рисунке имя виртуальной машины — **ssrsnativecloud**, а имя пользователя — **testuser**.
   
    ![имя для входа включает в себя имя виртуальной машины](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. На виртуальной машине откройте **интегрированную среду сценариев Windows PowerShell** с правами администратора. Интегрированная среда сценариев Windows PowerShell устанавливается по умолчанию в Windows Server 2012. Рекомендуется использовать интегрированную среду сценариев вместо стандартного окна Windows PowerShell, чтобы можно было вставить сценарий в эту среду, отредактировать его и выполнить.
3. Чтобы включить выполнение сценариев, выполните следующую команду Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Для проверки политики можно выполнить следующую команду:
   
        Get-ExecutionPolicy
4. В **интегрированной среде сценариев Windows PowerShell** откройте меню **Вид** и выберите пункт **Показать область сценариев**.
5. Скопируйте следующий сценарий и вставьте его в области сценариев интегрированной среды сценариев Windows PowerShell.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Измените параметр **$certificatehash** в сценарии:
   
   * Это **обязательный** параметр. Если вы не сохранили значение сертификата из предыдущих шагов, используйте один из следующих методов для копирования значение хэша сертификата из отпечатка сертификата:
     
       На виртуальной машине откройте интегрированную среду сценариев Windows PowerShell и выполните следующую команду:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Выходные данные будут выглядеть следующим образом. Если сценарий возвращает пустую строку, например, когда сертификат для виртуальной машины не настроен, см. раздел [Использование самозаверяющего сертификата виртуальных машин](#to-use-the-virtual-machines-self-signed-certificate).
     
     Или
   * На виртуальной машине выполните файл mmc.exe, а затем добавьте оснастку **Сертификаты** .
   * В узле **Доверенные корневые центры сертификации** дважды щелкните имя сертификата. При использовании самозаверяющего сертификата виртуальной машины имя сертификата совпадает с DNS-именем виртуальной машины и заканчивается на **cloudapp.net**.
   * Откройте вкладку **Сведения** .
   * Щелкните **Отпечаток**. Значение отпечатка отображается в поле сведений, например af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48.
   * **Перед выполнением сценария**удалите пробелы между парами значений. Например, af1160b64b288d890a8212ff6ba9c3664f319048.
7. Измените параметр **$httpsport** : 
   
   * Если для конечной точки HTTPS использовался порт 443, обновлять этот параметр в сценарии не требуется. В противном случае укажите значение порта, выбранное при настройке частной конечной точки HTTPS на виртуальной машине.
8. Измените параметр **$DNSName** : 
   
   * Сценарий настроен для шаблона сертификата $DNSName="+". Если вы не хотите настраивать привязку шаблона сертификата, закомментируйте $DNSName ="+"и включите следующую строку, полную ссылку $DNSNAme — ##$DNSName="$server.cloudapp.net".
     
       Измените значение $DNSName, если не хотите использовать DNS-имя виртуальной машины для служб Reporting Services. Если этот параметр используется, сертификат также должен использовать это имя, а само имя следует глобально зарегистрировать на DNS-сервере.
9. Сценарий настроен для служб Reporting Services. Если вы хотите выполнить сценарий для служб Reporting Services, измените версию в пути пространства имен на значение v11 в операторе Get-WmiObject.
10. Выполните скрипт.

**Проверка**. Чтобы проверить работу базовых функций сервера отчетов, см. раздел [Проверка конфигурации](#verify-the-connection) далее в этой статье. Чтобы проверить привязку сертификата, откройте командную строку с правами администратора и запустите следующую команду:

    netsh http show sslcert

Результат будет включать в себя следующие сведения:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Использование диспетчера конфигурации для настройки сервера отчетов
Если вы не хотите запускать сценарий PowerShell для настройки сервера отчетов, выполните действия, описанные в этом разделе, чтобы использовать диспетчер конфигурации служб Reporting Services в собственном режиме для настройки сервера отчетов.

1. На портале Azure выберите виртуальную машину и щелкните команду подключения. Используйте имя пользователя и пароль, которые были настроены при создании виртуальной машины.
   
    ![подключение к виртуальной машине Azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Запустите Центр обновления Windows и установите обновления для виртуальной машины. Если требуется перезагрузка виртуальной машины, перезапустите ее и повторно подключитесь к ней с портала Azure.
3. В меню "Пуск" на виртуальной машине введите **Reporting Services** и откройте **диспетчер конфигурации служб Reporting Services**.
4. Оставьте значения по умолчанию для параметров **Имя сервера** и **Экземпляр сервера отчетов**. Щелкните **Подключить**.
5. Щелкните **URL-адрес веб-службы**на левой панели.
6. По умолчанию для служб Reporting Services задан HTTP-порт 80 с IP-адресом "Все назначенные". Чтобы добавить HTTPS, выполните следующие действия:
   
   1. В поле **SSL-сертификат** выберите сертификат, который вы хотите использовать, например [имя виртуальной машины].cloudapp.net. Если сертификаты в списке отсутствуют, см. раздел **Шаг 2. Создание сертификата сервера** для получения сведений об установке сертификата и обеспечении доверия к нему на виртуальной машине.
   2. В поле **SSL-порт**выберите значение 443. Если вы настроили частную конечную точку HTTPS в виртуальной машине с другим частным портом, укажите это значение здесь.
   3. Нажмите кнопку **Применить** и дождитесь завершения операции.
7. Щелкните элемент **База данных**на левой панели.
   
   1. Щелкните **Изменение базы данных**.
   2. Щелкните элемент **Создать новую базу данных сервера отчетов** и нажмите кнопку **Далее**.
   3. Оставьте значение по умолчанию для параметра **Имя сервера**, в качестве которого используется имя виртуальной машины, а также оставьте для параметра **Тип проверки подлинности** значение по умолчанию **Текущий пользователь** — **Встроенная система безопасности**. Нажмите кнопку **Далее**.
   4. Оставьте значение по умолчанию для параметра **Имя базы данных** — **ReportServer** и нажмите кнопку **Далее**.
   5. Оставьте для параметра **Тип проверки подлинности** значение по умолчанию **Учетные данные службы** и нажмите кнопку **Далее**.
   6. Щелкните элемент **Далее** on the **Далее** .
   7. Завершив настройку, нажмите кнопку **Готово**.
8. Щелкните элемент **URL-адрес диспетчера отчетов**на левой панели. Оставьте для параметра **Виртуальный каталог** значение по умолчанию **Отчеты** и нажмите кнопку **Применить**.
9. Щелкните **Выйти** , чтобы закрыть диспетчер конфигурации служб Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Шаг 4. Открытие порта в брандмауэре Windows
> [!NOTE]
> Этот раздел можно пропустить, если использовался один из сценариев для настройки сервера отчетов. Такой сценарий содержит действие для открытия порта брандмауэра. По умолчанию использовался порт 80 для HTTP и порт 443 для HTTPS.
> 
> 

Для удаленного подключения к диспетчеру отчетов или серверу отчетов из виртуальной машины на ней должна присутствовать конечная точка TCP. Необходимо открыть тот же самый порт в брандмауэре виртуальной машины. Эта конечная точка была создана при подготовке виртуальной машины.

Данный раздел содержит основные сведения о том, как открыть порт брандмауэра. Дополнительные сведения см. в статье [Настройка брандмауэра для доступа к серверу отчетов](https://technet.microsoft.com/library/bb934283.aspx).

> [!NOTE]
> Этот раздел можно пропустить, если использовался сценарий для настройки сервера отчетов. Такой сценарий содержит действие для открытия порта брандмауэра.
> 
> 

Если вы настроили для протокола HTTPS частный порт, отличный от 443, соответствующим образом измените следующий сценарий. Чтобы открыть порт **443** в брандмауэре Windows, выполните следующие действия:

1. Откройте окно Windows PowerShell с правами администратора.
2. Если при настройке конечной точки HTTPS на виртуальной машине вы использовали порт, отличный от 443, измените номер порта в следующей команде, а затем выполните ее:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Когда команда будет выполнена, в окне командной строки отобразится слово **ОК** .

Чтобы убедиться, что порт открыт, откройте окно Windows PowerShell и выполните следующую команду:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Проверка конфигурации
Чтобы убедиться, что базовые функции сервера отчетов теперь работают, откройте браузер с правами администратора и перейдите по следующим URL-адресам сервера отчетов и диспетчера отчетов:

* На виртуальной машине перейдите по URL-адресу сервера отчетов:
  
        http://localhost/reportserver
* На виртуальной машине перейдите по URL-адресу диспетчера отчетов:
  
        http://localhost/Reports
* На локальном компьютере перейдите к **удаленному** диспетчеру отчетов на виртуальной машине. Соответствующим образом обновите DNS-имя в следующем примере. При запросе пароля используйте учетные данные администратора, созданные при подготовке виртуальной машины. Имя пользователя имеет формат [домен] \[имя пользователя], где домен — это имя компьютера виртуальной машины, например ssrsnativecloud\testuser. Если вы не используете HTTP**S**, удалите букву **s** в URL-адресе. Сведения о создании дополнительных пользователей на виртуальной машине см. в следующем разделе.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* На локальном компьютере перейдите по URL-адресу удаленного сервера отчетов. Соответствующим образом обновите DNS-имя в следующем примере. Если вы не используете HTTPS, удалите букву s в URL-адресе.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Создание пользователей и назначение ролей
После настройки и проверки сервера отчетов часто требуется создать одного или нескольких пользователей и назначить их ролям служб Reporting Services. Дополнительную информацию см. в следующих разделах.

* [Создание локальной учетной записи пользователя](https://technet.microsoft.com/library/cc770642.aspx)
* [Предоставление пользователям доступа к серверу отчетов (диспетчеру отчетов)](https://msdn.microsoft.com/library/ms156034.aspx)
* [Создание назначений ролей и управление ими](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Создание и публикация отчетов на виртуальной машине Azure
В следующей таблице перечислены некоторые параметры, доступные для публикации существующих отчетов с локального компьютера на сервер отчетов, размещенный на виртуальной машине Microsoft Azure.

* **Сценарий RS.exe**. Используйте сценарий RS.exe, чтобы скопировать элементы отчета с существующего сервера отчетов в виртуальную машину Microsoft Azure. Дополнительные сведения см. в подразделе "С сервера отчетов в собственном режиме на другой сервер отчетов в собственном режиме — виртуальная машина Microsoft Azure" раздела [Образец скрипта программы rs.exe служб Reporting Services для копирования содержимого между серверами отчетов](https://msdn.microsoft.com/library/dn531017.aspx).
* **Построитель отчетов**. Виртуальная машина включает в себя ClickOnce-версию построителя отчетов Microsoft SQL Server. Для первого запуска построителя отчетов на виртуальной машине выполните следующие действия.
  
  1. Запустите браузер с правами администратора.
  2. Перейдите к диспетчеру отчетов на виртуальной машине и щелкните элемент **Построитель отчетов** на ленте.
     
     Дополнительные сведения см. в статье [Установка, удаление и поддержка построителя отчетов](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: виртуальная машина**. Если виртуальная машина была создана с помощью SQL Server 2012, компонент SQL Server Data Tools устанавливается на виртуальной машине и может использоваться для создания **проектов сервера отчетов** и отчетов на виртуальной машине. SQL Server Data Tools может публиковать отчеты на сервере отчетов на виртуальной машине.
  
    При создании виртуальной машины с помощью SQL Server 2014 можно установить SQL Server Data Tools — BI для Visual Studio. Дополнительную информацию см. в следующих разделах.
  
  * [Microsoft SQL Server Data Tools — бизнес-аналитика для Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools — Business Intelligence для Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools и SQL Server Business Intelligence (SSDT-BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)
* **SQL Server Data Tools: удаленно**. Создайте на локальном компьютере проект служб Reporting Services в SQL Server Data Tools, содержащий отчеты служб Reporting Services. Настройте проект для подключения к URL-адресу веб-службы.
  
    ![свойства проекта SSDT для проекта SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Использование сценария**. Используйте сценарий для копирования содержимого сервера отчетов. Дополнительные сведения см. в разделе [Образец скрипта программы rs.exe служб Reporting Services для копирования содержимого между серверами отчетов](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Минимизация затрат на неиспользуемую виртуальную машину
> [!NOTE]
> Чтобы свести к минимуму расходы на неиспользуемые виртуальные машины Azure, завершите работу виртуальной машины на портале Azure. Если использовать для завершения работы виртуальной машины параметры электропитания Windows внутри нее, плата за эту виртуальную машину будет взиматься в полном объеме. Для сокращения расходов необходимо завершить работу виртуальной машины на портале Azure. Если виртуальная машина больше не нужна, не забудьте удалить ее и связанные с ней VHD-файлы во избежание расходов на хранение. Дополнительные сведения см. в разделе вопросов и ответов на странице [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Дополнительные сведения
### <a name="resources"></a>Ресурсы
* Аналогичные материалы, относящиеся к развертыванию SQL Server Business Intelligence и SharePoint 2013 с одним сервером, см. в статье [Use Windows PowerShell to Create an Azure VM With SQL Server BI and SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx) (Использование PowerShell для создания ВМ Azure с помощью SQL Server BI и SharePoint 2013).
* Похожие материалы о развертывании SQL Server Business Intelligence и SharePoint 2013 с несколькими серверами см. в статье [Deploy SQL Server Business Intelligence in Azure Virtual Machines](https://msdn.microsoft.com/library/dn321998.aspx) (Развертывание Server Business Intelligence на виртуальных машинах Azure).
* Общие сведения о развертываниях SQL Server Business Intelligence на виртуальных машинах Azure см. в статье [SQL Server Business Intelligence на виртуальных машинах Azure](virtual-machines-windows-classic-ps-sql-bi.md).
* Дополнительные сведения о стоимости вычислений Azure см. на вкладке "Виртуальные машины" [калькулятора цен Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Материалы сообщества
* Пошаговые инструкции по созданию сервера отчетов служб Reporting Services в основном режиме без использования сценария см. в статье [Размещение служб SQL Reporting Services на виртуальной машине Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Ссылки на другие ресурсы по работе SQL Server на виртуальных машинах Azure
[Обзор. SQL Server на виртуальных машинах Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

