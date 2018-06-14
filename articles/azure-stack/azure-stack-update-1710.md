---
title: Обновление 1710 Azure Stack (сборка 20171020.1) | Документация Майкрософт
description: Узнайте, что находится в обновлении 1710 для интегрированных систем Azure Stack, об известных проблемах и где можно скачать обновление.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 8c7c39ecdc332c994e5c00f8415462f208e7d20b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30311929"
---
# <a name="azure-stack-1710-update-build-201710201"></a>Обновление 1710 Azure Stack (сборка 20171020.1)

*Область применения: интегрированные системы Azure Stack*

В этой статье представлены улучшения и исправления в этом пакете обновлений, известные проблемы выпуска и сведения о том, где можно скачать обновление. Известные проблемы можно разделить на известные проблемы, которые непосредственно относятся к процессу обновления, и известные проблемы со сборкой (после установки).

> [!IMPORTANT]
> Этот пакет обновления предназначен только для интегрированных систем Azure Stack. Не применяйте этот пакет обновления к Пакету средств разработки Azure Stack.

## <a name="improvements-and-fixes"></a>Улучшения и исправления

Это обновление включает следующие улучшения качества и исправления для Azure Stack.
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Улучшения и исправления Windows Server 2016

- Обновления для Windows Server 2016: 10 октября 2017 г. — KB4041691 (сборка ОС 14393.1770). Дополнительные сведения см. по ссылке [https://support.microsoft.com/help/4041691](https://support.microsoft.com/help/4041691).

### <a name="additional-quality-improvements-and-fixes"></a>Дополнительные улучшения качества и исправления

- Добавлены командлеты привилегированной конечной точки PowerShell для помощи в устранении неполадок и обновления сервера NTP.
- Добавлена поддержка обновления модулей конечной точки Just Enough Administration (JEA) привилегированной конечной точки и список разрешений командлета. 
- Исправлены ошибки местных языков в привилегированной конечной точке.
- Добавлена возможность смены учетных данных шлюза.
- Удалена учетная запись локального администратора CBLocalAdmin. 
- Исправлено содержание шаблона оповещений о пульсе, чтобы убедиться, что предупреждения о пульсе работают правильно после обновления.
- Исправлен поставщик ресурсов структуры для работы со значениями времени ожидания при выполнении операций FRU. 
- Разработчикам облака добавлена возможность использовать профили API Azure Resource Manager в Azure Stack.
- Отключена служба центра обновления Windows на виртуальной машине развертывания (DVM). 
- Удалены действия включения и выключения питания узла из пользовательского интерфейса.
- Различные исправления производительности и стабильности. 
 
## <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления

Этот раздел содержит известные проблемы, которые могут возникнуть во время установки обновления 1710.

> [!IMPORTANT]
> В случае сбоя обновления при следующей попытке выполнить его используйте командлет `Resume-AzureStackUpdate` из привилегированной конечной точки. Не возобновляйте обновление с помощью портала администрирования. (Это известная проблема в этом выпуске.) Дополнительные сведения см. в статье [Мониторинг обновлений в Azure Stack с помощью привилегированной конечной точки](azure-stack-monitor-update.md).

| Симптом  | Причина:  | Способы устранения: |
|---------|---------|---------|
|В ходе обновления может возникнуть следующая ошибка<br>на шаге перезапуска узла хранилища хостами хранилища<br> в рамках плана действий по обновлению.<br><br>**{"name":"Restart Storage Hosts","description":"Restart<br> Storage Hosts.","errorMessage":"Type 'Restart' of Role<br> 'BareMetal' raised an exception:\n\n The computer<br> HostName-05 is skipped. Fail to retrieve its LastBootUpTime<br> via the WMI service with the following error message:<br> The RPC server is unavailable.<br> (Исключение из HRESULT: 0x800706BA).\nat Restart-Host** | Эта проблема вызвана потенциально неисправным драйвером в некоторых конфигурациях. | 1. Войдите в веб-интерфейс контроллера управления основной платой (BMC) и перезапустите узел, указанный в сообщении об ошибке.<br><br>2. Возобновите обновление с помощью привилегированной конечной точки. |
| В ходе обновления кажется, что процесс обновления остановился<br> после шага 2.4 (установка обновления)<br> в рамках плана действий по обновлению.<br><br>За этим шагом следует ряд процессов копирования файлов .nupkg<br> в общие файловые ресурсы внутренней инфраструктуры. Например: <br><br>**Copying 1 files from content\PerfCollector\VirtualMachines to <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>Или же может отобразится такое сообщение:<br><br>**WarningMessage:Task: Invocation of interface 'LiveUpdate'<br> of role 'Cloud\Fabric\VirtualMachines' failed:<br> Type 'LiveUpdate' of Role 'VirtualMachines' raised an<br> exception: There is not enough space on the disk.** (Предупреждение для задания. Вызов интерфейса LiveUpdate роли Cloud\Fabric\VirtualMachines завершился сбоем. Тип LiveUpdate роли VirtualMachines создал исключение. Не хватает места на диске.)  | Проблема вызвана переполнением дисков на виртуальной машине инфраструктуры и проблемой в масштабируемом файловом сервере (SOFS) Windows Server. Она будет решена в последующем обновлении файлов журнала. | Обратитесь за помощью в службу поддержки пользователей Майкрософт. | 
| В ходе обновления может возникнуть следующая ошибка<br> на шаге 2.13.2 (обновление<br> *имя_виртуальной_машины*) в рамках плана действий по обновлению. (Имя виртуальной машины<br> может отличаться.)<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: Invocation of interface 'LiveUpdate' of<br> role 'Cloud\Fabric\WAS'failed: Type 'LiveUpdate' of Role<br> 'WAS' raised an exception: ERROR during storage<br> initialization: An error occurred while trying to make an API<br> call to Microsoft Storage service: {"Message": "A timeout<br> occurred while communicating with Service Fabric.<br> Exception Type: TimeoutException.<br> Сообщение об исключении: превышено время ожидания операции."}**  | Проблема вызвана истечением времени ожидания операции ввода-вывода в Windows Server, которое будет устранено в последующих обновлениях. | Обратитесь за помощью в службу поддержки пользователей Майкрософт.
| В ходе обновления может возникнуть следующая ошибка<br> на шаге 21 (перезапуск виртуальных машин сервера SQL).<br><br>**Type 'LiveUpdateRestart' of Role 'VirtualMachines' raised an<br> exception: VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> Querying for VM MachineName-Sql01. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> VM is marked as HighlyAvailable. - 10/13/2017 5:11:50 PM<br> VerboseMessage:[VirtualMachines:LiveUpdateRestart] at<br>MS.Internal.ServerClusters.ExceptionHelp.Build at<br>MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(Boolean force) at Microsoft.FailoverClusters.PowerShell.<br>StopClusterResourceCommand.BeginTimedOperation() at <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() at Microsoft.FailoverClusters.PowerShell.<br>FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM Warning<br>Message: Task: Invocation of interface 'LiveUpdateRestart' of<br> role 'Cloud\Fabric\VirtualMachines' failed:** | Эта проблема может возникать, если не удалось перезагрузить виртуальную машину. | Обратитесь за помощью в службу поддержки пользователей Майкрософт.
| При выполнении обновления, может возникнуть ошибка следующего вида:<br><br>**2017-10-22T01:37:37.5369944Z Type 'Shutdown' of Role 'SQL'<br> raised an exception: An error occurred pausing node<br> 's45r1004-Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 at<br> Shutdown,C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>Applications \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br>Deployment\Classes\SQL\SQL.psm1: line 50 at <ScriptBlock&#62;,<br> <No file>: line 18 at <ScriptBlock&#62;, <No file&#62;: line 16** | Эта проблема может возникать, если виртуальную машину нельзя поместить в неактивное состояние для истощения роли. | Обратитесь за помощью в службу поддержки пользователей Майкрософт.
| При выполнении обновления, может возникнуть одна из следующих ошибок:<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error checking ADFS<br> probe endpoint *endpoint_URI*: Exception calling<br> "GetResponse" with "0" argument(s): "The remote server<br> returned an error: (503) Server Unavailable." at Invoke-<br>ADFSGraphValidation**<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error fetching<br> ADFS properties: Could not connect to <br>net.tcp://localhost:1500/policy. The connection attempt lasted<br> for a time span of 00:00:02.0498923. TCP error code<br> 10061: No connection could be made because the target<br> machine actively refused it 127.0.0.1:1500.<br> at Invoke-ADFSGraphValidation** | Плану действий обновления не удалось проверить работоспособность служб федерации Active Directory (AD FS). | Обратитесь за помощью в службу поддержки пользователей Майкрософт.

## <a name="known-issues-post-installation"></a>Известные проблемы (после установки)

В этом разделе содержатся известные проблемы со сборкой 20171020.1 после установки.

### <a name="portal"></a>Microsoft Azure

- Просмотр вычислений или ресурсов хранилища на портале администратора может оказаться невозможным. Это означает, что во время установки обновления произошла ошибка, а затем об обновлении было неверно сообщено (как об успешном). При возникновении этой проблемы обратитесь за помощью в службу поддержки пользователей Майкрософт.
- На портале может появиться пустая панель мониторинга. Чтобы восстановить панель мониторинга, щелкните значок шестеренки в правом верхнем углу портала и выберите **Восстановление параметров по умолчанию**.
- При просмотре свойств группы ресурсов кнопка **Перемещение** отключена. Это ожидаемое поведение. Перемещение групп ресурсов между подписками в настоящее время не поддерживается.
- Для любого рабочего процесса, где в раскрывающемся списке выбираются подписки, группа ресурсов или расположение, возникают следующие проблемы:

   - Может появиться пустая строка в верхней части списка. По-прежнему можно будет выбрать элемент должным образом.
   - Если список элементов в раскрывающемся списке короткий, возможно, вы не сможете просмотреть ни одно из имен элементов.
   - При наличии нескольких подписок пользователей раскрывающийся список группы ресурсов может быть пуст. 

   Чтобы обойти последние две проблемы, можно ввести имя группы подписки или ресурса (если известно) или вместо этого использовать PowerShell.
- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.
- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. В качестве временного решения разрешения можно проверить с помощью PowerShell.
- Не удается загрузить колонку **Работоспособность службы**. При открытии колонки "Работоспособность службы" на портале администрирования или пользовательском портале в Azure Stack отображается ошибка и не загружается информация. Это ожидаемое поведение. Хотя вы можете выбрать и открыть колонку "Работоспособность службы", эта функция еще недоступна. Она будет реализована в будущей версии Azure Stack.
 

### <a name="backup"></a>Архивация

- Не включайте резервное копирование инфраструктуры в колонке **Infrastructure backup** (Архивация инфраструктуры).

### <a name="health-and-monitoring"></a>Работоспособность и мониторинг

- Если перезагрузить экземпляр роли инфраструктуры, может появиться сообщение, указывающее на сбой перезагрузки. Однако перезагрузка фактически будет выполнена успешно.

### <a name="marketplace"></a>Marketplace
- При попытке добавить элементы в Azure Stack Marketplace с помощью параметра **Add from Azure** (Добавить из Azure) для скачивания могут отображаться не все элементы.
- Пользователи могут просматривать весь Marketplace без подписки и видеть некоторые административные элементы, такие как планы и предложения. Эти элементы бесполезны для пользователей.

### <a name="compute"></a>Среда выполнения приложений
- Пользователи могут создавать виртуальную машину с географически избыточным хранилищем. Но такой выбор конфигурации проводит к сбою при создании виртуальной машины.
- Группу доступности виртуальной машины можно настроить только с одним доменом сбоя и одним доменом обновления.
- На Marketplace не предусмотрено создание масштабируемых наборов виртуальных машин. Масштабируемый набор можно создать с помощью шаблона.
- Параметры масштабирования для масштабируемых наборов виртуальной машины на портале недоступны. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Из-за различий между версиями PowerShell используйте параметр `-Name` вместо параметра `-VMScaleSetName`.
 
### <a name="networking"></a>Сеть
- Вы не можете создать подсистему балансировки нагрузки с общедоступным IP-адресом с помощью портала. В качестве временного решения для создания подсистемы балансировки нагрузки можно использовать PowerShell.
- При создании подсистемы балансировки сетевой нагрузки необходимо создать правило преобразования сетевых адресов (NAT). Если этого не сделать, вы получите ошибку при попытке добавить правило NAT после создания подсистемы балансировки нагрузки.
- Невозможно отменить сопоставление общедоступного IP-адреса c виртуальной машиной после создания виртуальной машины и ее сопоставления с этим IP-адресом. Будет выглядеть так, будто вам удалось отменить сопоставление, но ранее присвоенный общедоступный IP-адрес останется связанным с исходной виртуальной машиной. Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине, к которой он был привязан. В настоящее время для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.
 
### <a name="sqlmysql"></a>SQL и MySQL
- После создания новых номеров SKU для SQL или MySQL может пройти до одного часа, прежде чем клиенты смогут создавать в них базы данных. 
- Создание элементов непосредственно на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается и может привести к несоответствию состояний.
 
### <a name="app-service"></a>Служба приложений
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.
 
### <a name="field-replaceable-unit-fru-procedures"></a>Процедуры заменяемого в полевых условиях блока (FRU)

- Во время выполнения обновления исправления автономных образов не устанавливается. Если необходимо заменить узел единицы масштабирования, обратитесь к поставщику оборудования OEM, чтобы убедиться, что у замененного узла есть последний уровень исправления.

## <a name="download-the-update"></a>Скачивание обновления

Пакет обновления 1710 можно скачать [здесь](https://aka.ms/azurestackupdatedownload).

## <a name="next-steps"></a>Дополнительная информация

- Общие сведения об управлении обновлениями в Azure Stack см. в [этой статье](azure-stack-updates.md).
- Сведения о том, как применять обновления, см. в статье [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).
