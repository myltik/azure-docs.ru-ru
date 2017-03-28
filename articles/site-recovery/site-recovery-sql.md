---
title: "Репликация приложений с помощью SQL Server и Azure Site Recovery | Документация Майкрософт"
description: "В этой статье рассматривается репликация SQL Server с помощью возможностей аварийного восстановления SQL Server и Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 9ea73dd91c9637692bbc3d6d2aa97fbed7ae500d
ms.openlocfilehash: 79c110031a47f1bdb78f4acfcadd7bff1e909807
ms.lasthandoff: 02/23/2017


---
# <a name="protect-sql-server-using-sql-server-disaster-recovery-and-azure-site-recovery"></a>Защита SQL Server с помощью аварийного восстановления SQL Server и Azure Site Recovery

В этой статье описывается способ организации защиты серверной части SQL Server приложения с помощью сочетания технологий непрерывности бизнес-процессов и аварийного восстановления (BCDR) SQL Server и [Azure Site Recovery](site-recovery-overview.md).

Прежде чем начать, убедитесь, что вы ознакомлены с возможностями аварийного восстановления SQL Server, включая отказоустойчивую кластеризацию, группы доступности AlwaysOn, зеркальное отображение базы данных и доставку журналов.


## <a name="sql-server-deployments"></a>Развертывания SQL Server

Многие рабочие нагрузки используют SQL Server как основу, которую можно интегрировать с такими приложениями, как SharePoint, Dynamics и SAP, для реализации служб данных.  Вы можете развернуть SQL Server несколькими способами:

* **Изолированный сервер SQL Server**. SQL Server и все базы данных размещаются на одном компьютере (физическом или виртуальном). Если сервер виртуальный, то для обеспечения локальной высокой доступности используется кластеризация размещения. Высокая доступность на уровне гостя не обеспечивается.
* **Экземпляры отказоустойчивых кластеров SQL Server (Always On FCI).** В этом случае два или несколько узлов экземпляров SQL Server с общими дисками настраиваются в отказоустойчивом кластере Windows. Если узел не работает, кластер может выполнить отработку отказа SQL Server в другой экземпляр. Эта настройка обычно используется для реализации высокого уровня доступности на основном сайте. Этот тип развертывания не защищает от сбоев и неполадок на уровне общего хранилища. Общий диск может быть реализован с использованием интерфейса iSCSI, Fiber Channel или общих VHDX-файлов.
* **Группы доступности SQL AlwaysOn.** В этом случае два или несколько узлов настраиваются в кластере без общих ресурсов, в котором базы данных SQL Server настроены в группе доступности с синхронной репликацией и автоматической отработкой отказа.

 Ниже описаны исходные технологии аварийного восстановления SQL для восстановления баз данных на удаленном сайте.

* Группы доступности SQL AlwaysOn, чтобы обеспечить аварийное восстановление для выпуска SQL Server Enterprise 2012 или 2014.
* Зеркальное отображение базы данных SQL в режиме высокого уровня безопасности для SQL Server Standard (любой версии) или SQL Server 2008 R2.

## <a name="site-recovery-support"></a>Поддержка Site Recovery

### <a name="supported-scenarios"></a>Поддерживаемые сценарии использования.
В таблице представлены сведения о защите SQL Server службой Site Recovery.

**Сценарий** | **На дополнительный сайт** | **В Azure**
--- | --- | ---
**Hyper-V** | Да | Да
**VMware** | Да | Да
**Физический сервер** | Да | Да

### <a name="supported-sql-server-versions"></a>Поддерживаемые версии SQL Server
Для соответствующих сценариев поддерживаются следующие версии SQL Server:

* SQL Server 2014 Enterprise и Standard;
* SQL Server 2012 Enterprise и Standard;
* SQL Server 2008 R2 Enterprise и Standard.

### <a name="supported-sql-server-integration"></a>Поддерживаемая интеграция SQL Server

Site Recovery может интегрироваться с собственными технологиями SQL Server BCDR, перечисленными в таблице ниже, для создания решения аварийного восстановления.

**Компонент** | **Дополнительные сведения** | **SQL Server** |
--- | --- | ---
**Группа доступности AlwaysOn** | Несколько отдельных экземпляров SQL Server, каждый из которых запущен в отказоустойчивом кластере с несколькими узлами.<br/><br/>Базы данных можно объединить в группы отработки отказа, которые затем можно скопировать (зеркальное отображение) на экземпляры SQL Server, благодаря чему исключается необходимость в общем хранилище.<br/><br/>Обеспечивает аварийное восстановление между основным сайтом и одним или несколькими дополнительными сайтами. Два узла можно настроить в кластере без общих ресурсов, в котором базы данных SQL Server настроены в группе доступности с синхронной репликацией и автоматической отработкой отказа. | SQL Server Enterprise 2014 и 2012
**Отказоустойчивая кластеризация (AlwaysOn FCI)** | SQL Server применяет отказоустойчивую кластеризацию Windows для обеспечения высокого уровня доступности локальных рабочих нагрузок SQL Server.<br/><br/>Узлы, на которых выполняются экземпляры SQL Server с общими дисками, настроены в отказоустойчивом кластере. Если экземпляр выходит из строя, выполняется переход кластера на другой ресурс.<br/><br/>Кластер не обеспечивает защиту от сбоев или простоев в общем хранилище. Общий диск может быть реализован с использованием интерфейса iSCSI, Fiber Channel или с помощью в общих VHDX-файлов. | Выпуски SQL Server Enterprise<br/><br/>SQL Server Standard (ограничено только двумя узлами)
**Зеркальное отображение базы данных (в режиме высокого уровня безопасности)** | Защищает одну базу данных переносом в одну дополнительную копию. Доступно как в режиме репликации с высоким уровнем безопасности (синхронный режим), так и в режиме репликации с высоким уровнем производительности (асинхронный режим). Не требует наличия отказоустойчивого кластера. | SQL Server 2008 R2<br/><br/>Все выпуски SQL Server Enterprise
**Автономный SQL Server** | SQL Server и база данных находятся на одном сервере (физическом или виртуальном). Если сервер виртуальный, то кластеризация размещения используется для обеспечения высокой доступности. Без высокой доступности гостевого уровня. | Выпуск Enterprise или Standard

## <a name="deployment-recommendations"></a>Рекомендации по развертыванию

В таблице перечислены рекомендации относительно интеграции технологий SQL Server BCDR в Site Recovery.

| **Версия** | **Выпуск** | **Развертывание** | **От локального к локальным** | **От локального к Azure** |
| --- | --- | --- | --- | --- |
| SQL Server 2014 или 2012 |Enterprise |Экземпляр отказоустойчивого кластера |Группы доступности AlwaysOn |Группы доступности AlwaysOn |
|| Enterprise |Группы доступности AlwaysOn для обеспечения высокой доступности |Группы доступности AlwaysOn |Группы доступности AlwaysOn | |
|| Стандарт |Экземпляр отказоустойчивого кластера (FCI) |Репликация Site Recovery с локальным зеркалом |Репликация Site Recovery с локальным зеркалом | |
|| Enterprise или Standard |Автономный |Репликация Site Recovery |Репликация Site Recovery | |
| SQL Server 2008 R2 |Enterprise или Standard |Экземпляр отказоустойчивого кластера (FCI) |Репликация Site Recovery с локальным зеркалом |Репликация Site Recovery с локальным зеркалом |
|| Enterprise или Standard |Автономный |Репликация Site Recovery |Репликация Site Recovery | |
| SQL Server (любая версия) |Enterprise или Standard |Экземпляр отказоустойчивого кластера — приложение DTC |Репликация Site Recovery |Не поддерживается |

## <a name="deployment-prerequisites"></a>Предварительные условия для развертывания

* Локальное развертывание SQL Server с поддерживаемой версией SQL Server. Обычно также требуется служба Active Directory для SQL Server.
* Необходимые условия для сценария, который требуется развернуть. Ознакомьтесь с дополнительными сведениями о требованиях к поддержке для [репликации в Azure](site-recovery-support-matrix-to-azure.md), [в локальную среду](site-recovery-support-matrix.md) и с [предварительными требованиями для развертывания](site-recovery-prereq.md).
* Для настройки восстановления в Azure на виртуальных машинах SQL Server запустите средство [оценки готовности виртуальной машины Azure](http://www.microsoft.com/download/details.aspx?id=40898), чтобы убедиться в их совместимости с Azure и Site Recovery.

## <a name="set-up-active-directory"></a>настроить Active Directory;

Для правильной работы SQL Server на дополнительном сайте восстановления вам потребуется настроить службу Active Directory.

* **Для малых предприятий.** Если у вас имеется небольшое количество приложений и один контроллер домена для локального сайта, то для выполнения отработки отказа всего сайта мы рекомендуем использовать репликацию Site Recovery для репликации контроллера домена в дополнительный центр обработки данных или в Azure.
* **Для средних и крупных предприятий**. Если у вас имеется большое количество приложений и лес Active Directory, то для выполнения отработки отказа для каждого отдельного приложения или рабочей нагрузки мы рекомендуем настроить дополнительный контроллер домена в дополнительном центре обработки данных или в Azure. При использовании групп доступности AlwaysOn для восстановления удаленного сайта мы рекомендуем настроить другой дополнительный контроллер домена на дополнительном сайте или в Azure, который будет использоваться для восстановленного экземпляра SQL Server.

При выполнении инструкций, описанных в этой статье, предполагается, что в дополнительном расположении доступен контроллер домена. [Узнайте больше](site-recovery-active-directory.md) о защите Active Directory с помощью Site Recovery.

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-classic-portal-with-a-vmmconfiguration-server"></a>Интеграция с SQL Server AlwaysOn для репликации в Azure (классический портал с сервером VMM и сервером конфигурации)


Site Recovery изначально поддерживает SQL AlwaysOn. Если вы создали группу доступности SQL и настроили виртуальную машину Azure в качестве дополнительного расположения, то для управления отработкой отказа в группах доступности вы можете использовать Site Recovery.

> [!NOTE]
> Сейчас эта функция предоставляется в предварительной версии. Она доступна, если на основном сайте имеются серверы узлов Hyper-V, управляемые в облаках VMM System Center, или если настроена [репликация VMware](site-recovery-vmware-to-azure.md). На новом портале Azure эта функция в настоящее время недоступна. При использовании нового портала Azure следуйте указаниям в [этом разделе](site-recovery-sql.md#integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server).
>
>


#### <a name="before-you-start"></a>Перед началом работы

Ниже приведены условия, которые требуется выполнить для интеграции SQL AlwaysOn с Site Recovery.

* Локальный SQL Server (на автономном сервере или в отказоустойчивом кластере).
* Одна или несколько виртуальных машин Azure с установленным SQL Server.
* Группа доступности SQL Server, настроенная между локальным SQL Server и SQL Server, работающим в Azure.
* На локальном SQL Server необходимо включить удаленное взаимодействие PowerShell. Сервер VMM или сервер конфигурации должен иметь возможность осуществлять удаленные вызовы PowerShell к компьютеру с SQL Server.
* На локальный компьютер SQL Server должна быть добавлена учетная запись пользователя. Добавьте в группу SQL Server как минимум следующие разрешения:
  * ALTER AVAILABILITY GROUP — разрешения приведены [здесь](https://msdn.microsoft.com/library/hh231018.aspx) и [здесь](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3).
  * ИЗМЕНЕНИЕ БАЗЫ ДАННЫХ — разрешения приведены  [здесь](https://msdn.microsoft.com/library/ff877956.aspx#Security).
* Если вы используете VMM, создайте учетную запись запуска от имени на сервере VMM.
- Если вы используете VMware, создайте учетную запись с помощью CSPSConfigtool.exe на сервере конфигурации.
* Модуль SQL PS необходимо установить на серверы SQL Server, работающие локально, а также на виртуальных машинах Azure.
* На виртуальных машинах Azure необходимо установить агент виртуальной машины.
* Предоставьте NTAUTHORITY\System на SQL Server, запущенном на виртуальных машинах Azure, следующие разрешения:
  * ALTER AVAILABILITY GROUP — разрешения приведены [здесь](https://msdn.microsoft.com/library/hh231018.aspx) и [здесь](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3).
  * ИЗМЕНЕНИЕ БАЗЫ ДАННЫХ — разрешения приведены  [здесь](https://msdn.microsoft.com/library/ff877956.aspx#Security).

### <a name="add-a-sql-server"></a>Добавление SQL Server
1. Щелкните **Добавить SQL** , чтобы добавить новый SQL Server.

    ![Добавить SQL](./media/site-recovery-sql/add-sql.png)
2. Выберите **Configure SQL Settings (Настройка параметров SQL)** > **Имя** и укажите понятное имя для SQL Server.
3. В поле **SQL Server (полное доменное имя)** укажите полное доменное имя исходного SQL Server, который нужно добавить. Если SQL Server установлен в отказоустойчивом кластере, укажите полное доменное имя кластера, а не какого-либо его узла.  
4. Выберите **Экземпляр SQL Server** и укажите экземпляр по умолчанию или пользовательское имя.
5. В поле **Сервер управления** выберите сервер VMM или сервер конфигурации, зарегистрированный в хранилище. Site Recovery использует этот сервер для взаимодействия с SQL Server.
6. В поле **Учетная запись запуска от имени** введите имя учетной записи запуска от имени VMM или учетную запись сервера конфигурации. Эта учетная запись используется для доступа к SQL Server и должна иметь разрешения на чтение и отработку отказа в группах доступности на компьютерах с SQL Server.

    ![Диалоговое окно "Добавление SQL"](./media/site-recovery-sql/add-sql-dialog.png)

После добавления SQL Server он появится на вкладке **Серверы SQL Server**.

![Список серверов SQL Server](./media/site-recovery-sql/sql-server-list.png)

### <a name="add-a-sql-availability-group"></a>Добавление группы доступности SQL

1. Выберите SQL Server, который вы добавили, и щелкните **Add SQL Availability Group** (Добавление группы доступности SQL).

    ![Добавить SQL AG](./media/site-recovery-sql/add-sqlag.png)
2. Вы можете реплицировать группу доступности на одну или несколько виртуальных машин Azure. После добавления группы вам необходимо предоставить имя и подписку виртуальной машины Azure, в которую с помощью Site Recovery будет выполнена отработка отказа группы.

    ![Диалоговое окно "Добавление SQL AG"](./media/site-recovery-sql/add-sqlag-dialog.png)
3. В этом примере группа доступности DB1-AG станет основной группой доступности на виртуальной машине SQLAGVM2, которая запускается при отработке отказа в подписке DevTesting2.

> [!NOTE]
> В Site Recovery можно добавлять только основные группы доступности на добавленном ранее SQL Server. Если вы сделали группу доступности основной на SQL Server или если вы добавили на SQL Server другие группы доступности, обновите группу в SQL Server.
>
>

### <a name="create-a-recovery-plan"></a>Создайте план восстановления

Создайте план восстановления с помощью виртуальных машин и групп доступности. Выберите сервер VMM или сервер конфигурации в качестве источника, а Azure в качестве целевого объекта.

![Создание плана восстановления](./media/site-recovery-sql/create-rp1.png)

![Создание плана восстановления](./media/site-recovery-sql/create-rp2.png)

В примере приложение Sharepoint состоит из трех виртуальных машин, серверной частью которых является группа доступности SQL. В этом плане восстановления мы можем выбрать и группу доступности, и виртуальные машины приложения. Вы можете дополнительно настроить план восстановления, переместив виртуальные машины в другие группы отработки отказа для упорядочения отработки отказа. Группа доступности всегда подвергается отработке отказа в первую очередь, так как она служит серверной частью приложения.

![Настройка плана восстановления](./media/site-recovery-sql/customize-rp.png)

### <a name="failover"></a>Отработка отказа

После добавления группы доступности в план восстановления становятся доступными различные параметры отработки отказа.

**Тип отработки отказа** | **Дополнительные сведения**
--- | ---
**Плановая отработка отказа** | Плановая отработка отказа не предполагает потери данных. Для этого режим группы доступности SQL переводится в синхронный режим доступности, а затем активируется отработка отказа, чтобы эта группа доступности стала основной на виртуальной машине, указанной при добавлении данной группы в Site Recovery. После завершения отработки отказа устанавливается режим доступности, который использовался перед активацией плановой отработки отказа.
**Незапланированная отработка отказа** | Внеплановая отработка отказа может привести к потере данных. При активации внеплановой отработки отказа режим доступности группы остается без изменений, но этот режим становится основным на виртуальной машине, указанной при добавлении группы доступности в Site Recovery. После завершения внеплановой отработки отказа и восстановления доступа к локальному серверу с SQL Server необходимо выполнить в группе доступности обратную репликацию. Это действие недоступно в плане восстановления. К нему можно перейти, выбрав **Серверы SQL Server** > **SQL Availability Group** (Группы доступности SQL).
**Тестовая отработка отказа** |Тестовая отработка отказа для группы доступности SQL не поддерживается. При включении тестовой отработки отказа она пропускается для группы доступности.

Попробуйте использовать следующие варианты отработки отказа.

**Параметр** | **Дополнительные сведения**
--- | ---
**Вариант 1** | 1. Выполните тестовую отработку отказа на уровне приложений и интерфейсном уровне.<br/><br/>2) Обновите уровень приложений для доступа к копии реплики в режиме только для чтения и выполните проверку приложения в режиме только для чтения.
**Вариант 2** | 1. Создайте копию экземпляра виртуальной машины с репликой SQL Server (с помощью клона VMM типа "сеть — сеть" или функции резервного копирования Azure) и добавьте ее в тестовую сеть.<br/><br/> 2) Выполните тестовую отработку отказа с помощью плана восстановления.

### <a name="fail-back"></a>Восстановление размещения

Если вы хотите снова сделать группу доступности основной на локальном SQL Server, активируйте плановую отработку отказа для плана восстановления и выберите направление с Microsoft Azure на локальный сервер.

> [!NOTE]
> Для восстановления репликации после внеплановой отработки отказа необходимо активировать в группе доступности обратную репликацию.  До выполнения этого действия репликация будет приостановлена.
>
>

## <a name="integrate-with-sql-server-alwayson-for-replication-to-azure-azure-portalclassic-portal-with-no-vmmconfiguration-server"></a>Интеграция с SQL Server Always On для репликации в Azure (портал Azure или классический портал без сервера VMM и сервера конфигурации)

Приведенные здесь инструкции применяются для интеграции с группами доступности SQL Server на новом портале Azure и на классическом портале без использования сервера VMM или сервера конфигурации. В этом примере для настройки сценариев отработки отказа в группах доступности SQL вы можете использовать модули Runbook службы автоматизации Azure.

Необходимо сделать следующее:

1. Создайте локальный файл сценария для отработки отказа группы доступности. В этом примере сценария указывается путь к группе доступности в реплике Azure и выполняется отработка отказа в этот экземпляр реплики. Этот сценарий выполняется на реплике виртуальной машины SQL Server путем его передачи с помощью расширения пользовательского сценария.

        ``Param(
           [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``

2. Передайте этот сценарий в большой двоичный объект в учетной записи хранения Azure. Используйте пример ниже:

        ``$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
        Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context``

3. Создайте модуль Runbook службы автоматизации Azure для запуска сценариев на реплике виртуальной машины SQL Server в Azure. Для этого используйте указанный ниже образец сценария. [Узнайте подробнее](site-recovery-runbook-automation.md) об использовании модулей Runbook службы автоматизации в планах восстановления.

4. При создании плана восстановления для приложения добавьте в сценарий шаг "pre-Group 1 boot", который запускает модуль Runbook службы автоматизации для отработки отказа группы доступности.


5. В SQL AlwaysOn нет встроенной поддержки тестовой отработки отказа. Таким образом, мы рекомендуем следующее.
    1. Установите [службу архивации Azure](../backup/backup-azure-vms.md) на виртуальной машине, где размещена реплика группы доступности в Azure.
    1. Прежде чем активировать тестовую отработку отказа для плана восстановления, восстановите виртуальную машину из резервной копии, полученной на предыдущем шаге.
    1. Выполните тестовую отработку отказа для плана восстановления.


> [!NOTE]
> Для приведенного ниже сценария предполагается, что группа доступности SQL размещена на классической виртуальной машине Azure и что на шаге 2 имя восстановленной виртуальной машины — SQLAzureVM-Test. Измените этот сценарий, указав имя восстановленной виртуальной машины.
>
>


     ``workflow SQLAvailabilityGroupFailover
     {

         param (
             [Object]$RecoveryPlanContext
         )

         $Cred = Get-AutomationPSCredential -name 'AzureCredential'

         #Connect to Azure
         $AzureAccount = Add-AzureAccount -Credential $Cred
         $AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
         Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

         InLineScript
         {
          #Update the script with name of your storage account, key and blob name
          $context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
          $sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;

          Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;

          if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
                {
                    Write-output "tfo"

                    Write-Output "Creating ILB"
                    Add-AzureInternalLoadBalancer -InternalLoadBalancerName SQLAGILB -SubnetName Subnet-1 -ServiceName SQLAzureVM-Test -StaticVNetIPAddress #IP
                    Write-Output "ILB Created"

                    #Update the script with name of the virtual machine recovered using Azure Backup
                    Write-Output "Adding SQL AG Endpoint"
                    Get-AzureVM -ServiceName "SQLAzureVM-Test" -Name "SQLAzureVM-Test"| Add-AzureEndpoint -Name sqlag -LBSetName sqlagset -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName SQLAGILB | Update-AzureVM

                    Write-Output "Added Endpoint"

                    $VM = Get-AzureVM -Name "SQLAzureVM-Test" -ServiceName "SQLAzureVM-Test"

                    Write-Output "UnInstalling custom script extension"
                    Set-AzureVMCustomScriptExtension -Uninstall -ReferenceName CustomScriptExtension -VM $VM |Update-AzureVM
                    Write-Output "Installing custom script extension"
                    Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $vm -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM   

                    Write-output "Starting AG Failover"
                    Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument "-Path sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag"  | Update-AzureVM
                    Write-output "Completed AG Failover"
                }
          else
                {
                Write-output "pfo/ufo";
                #Get the SQL Azure Replica VM.
                #Update the script to use the name of your VM and Cloud Service
                $VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     

                Write-Output "Installing custom script extension"
                #Install the Custom Script Extension on teh SQL Replica VM
                Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.*| Update-AzureVM;

                Write-output "Starting AG Failover";
                #Execute the SQL Failover script
                #Pass the SQL AG path as the argument.

                $AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";

                Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;

                Write-output "Completed AG Failover";

                }

         }
     }``

## <a name="integrate-with-sql-server-alwayson-for-replication-to-a-secondary-on-premises-site"></a>Интеграция с SQL Server AlwaysOn для репликации на дополнительный локальный сайт

Если SQL Server использует группы доступности для обеспечения высокой доступности (или экземпляр отказоустойчивого кластера), то на сайте восстановления также рекомендуется использовать группы доступности. Обратите внимание, что это актуально для приложений, не использующих распределенные транзакции.

1. [Настройте базы данных](https://msdn.microsoft.com/library/hh213078.aspx) для их добавления в группы доступности.
2. Создайте на дополнительном сайте виртуальную сеть.
3. Настройте подключение VPN типа "сеть — сеть" между виртуальной сетью и основным сайтом.
4. Создайте виртуальную машину на сайте восстановления и установите на нее SQL Server.
5. Расширьте существующие группы доступности AlwaysOn для новой виртуальной машины SQL Server. Настройте этот экземпляр SQL Server в качестве копии асинхронной реплики.
6. Создайте прослушиватель группы доступности или обновите существующий прослушиватель, включив в него виртуальную машину асинхронной реплики.
7. Убедитесь в том, что ферма приложений настроена с помощью прослушивателя. Если настройка выполнена с использованием имени сервера базы данных, обновите его, чтобы использовать прослушиватель и избежать повторной настройки после отработки отказа.

Для приложений, использующих распределенные транзакции, мы рекомендуем развернуть Site Recovery с помощью [репликации сети SAN](site-recovery-vmm-san.md) либо [репликации VMware или физического сервера типа "сеть — сеть"](site-recovery-vmware-to-vmware.md).

### <a name="recovery-plan-considerations"></a>Рекомендации по плану восстановления
1. Добавьте этот образец сценария в библиотеку VMM на основном и дополнительном сайтах.

        ``Param(
        [string]$SQLAvailabilityGroupPath
        )
        import-module sqlps
        Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force``
2. При создании плана восстановления для приложения добавьте шаг "pre-Group 1 boot" из сценария, который запускает сценарий для отработки отказа групп доступности.

## <a name="protect-a-standalone-sql-server"></a>Защита автономного сервера SQL Server

В этом примере для защиты компьютера SQL Server мы рекомендуем использовать репликацию Site Recovery. Конкретные шаги зависят от того, настроен ли SQL Server в качестве виртуальной машины или физического сервера, а также от необходимости реплицировать в Azure или в дополнительный локальный сайт. Дополнительные сведения см. в статье [Что такое Site Recovery?](site-recovery-overview.md)

## <a name="protect-a-sql-server-cluster-standard-editionwindows-server-2008-r2"></a>Защита кластера SQL Server (версия Standard для Windows Server 2008 R2)

Для кластера под управлением выпуска SQL Server Standard или SQL Server 2008 R2 рекомендуется использовать репликацию Site Recovery для защиты SQL Server.

### <a name="on-premises-to-on-premises"></a>Из локальной среды в локальную среду

* Если приложение использует распределенные транзакции, мы рекомендуем развернуть [репликацию Site Recovery с SAN](site-recovery-vmm-san.md) для среды Hyper-V или [репликацию VMware или физического сервера в VMware](site-recovery-vmware-to-vmware.md) для среды VMware.
* Для приложений без DTC описанный выше метод позволяет восстановить кластер в качестве автономного сервера, используя локальное зеркало баз данных с высоким уровнем безопасности.

### <a name="on-premises-to-azure"></a>Из локальной среды в Azure

Site Recovery не поддерживает гостевой кластер при репликации в Azure. SQL Server также не предоставляет экономичное решение для аварийного восстановления для выпуска Standard. В этом примере мы рекомендуем защитить локальный кластер SQL Server в автономный сервер SQL Server и восстановить его в Azure.

1. Настройте дополнительный автономный экземпляр SQL Server в локальном сайте.
2. Настройте этот экземпляр в качестве зеркальной копии баз данных, которые требуется защитить. Настройте зеркальное отображение в режиме высокого уровня безопасности.
3. Настройте Site Recovery на локальном сайте для [Hyper-V](site-recovery-hyper-v-site-to-azure.md) или [виртуальных машин VMware либо физических серверов](site-recovery-vmware-to-azure-classic.md).
4. Воспользуйтесь репликацией Site Recovery для репликации нового экземпляра SQL Server в Azure. Так как это зеркальная копия с высоким уровнем безопасности, она будет синхронизирована с основным кластером, однако в Azure она будет реплицирована с помощью репликации Site Recovery.


![Стандартный кластер](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)

### <a name="failback-considerations"></a>Рекомендации относительно восстановления размещения

В случае стандартных кластеров SQL Server восстановление размещения после внеплановой отработки отказа требует выполнения резервного копирования SQL Server и его восстановления из экземпляра зеркала в исходный кластер с повторной установкой зеркала.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об архитектуре Site Recovery см. [здесь](site-recovery-components.md).

