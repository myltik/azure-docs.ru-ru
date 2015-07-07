<properties
	pageTitle="Руководство по мониторингу и устранению неполадок для защиты сайтов VMM и Hyper-V" 
	description="Служба Azure Site Recovery управляет репликацией, отработкой отказа и восстановлением виртуальных машин, размещенных на локальных серверах, с использованием Azure или дополнительного центра обработки данных. Эта статья поможет вам отслеживать и устранять неполадки при защите сайтов VMM или Hyper-V." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="06/16/2015" 
	ms.author="anbacker"/>
	
# Руководство по мониторингу и устранению неполадок для защиты сайтов VMM и Hyper-V

Это руководство по мониторингу и устранению неполадок позволяет узнать об отслеживании работоспособности репликации и способах устранения неполадок для Azure Site Recovery.

## Общие сведения о компонентах

### Развертывание сайта VMM для репликации между локальными сайтами

В ходе настройки аварийного восстановления между двумя локальными средами необходимо загрузить поставщика Azure Site Recovery и установить на сервере VMM. Поставщику требуется подключение к Интернету, чтобы все операции с портала Azure преобразовались в локальные операции, такие как включение защиты, завершение работы виртуальных машин на основном сервере в рамках отработки отказа и т. д.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image1.png)

### Развертывание сайта VMM для репликации между локальными средами и Azure

В ходе настройки аварийного восстановления между локальными средами и Azure необходимо загрузить поставщика Azure Site Recovery и установить на сервере VMM вместе с агентом служб восстановления Azure, который должен быть установлен на каждом узле Hyper-V.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image2.png)

### Развертывание сайта Hyper-V для репликации между локальными средами и Azure

Это то же, что и развертывание VMM, только на самом узле Hyper-V устанавливается другой поставщик и агент.

## Мониторинг операций настройки, защиты и восстановления

Каждая операция в ASR контролируется и отслеживается на вкладке «Задания». В случае возникновения какой-либо ошибки конфигурации, защиты или восстановления перейдите на вкладку «Задания» и проверьте, отображаются ли там сбои.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image3.png)

После обнаружения сбоя на вкладке «Задания» выберите задание и щелкните описание ошибки для этого задания.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image4.png)

Сведения об ошибке позволяют выявить возможные причины и рекомендации о проблеме.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image5.png)

В приведенном выше случае есть другая операция, которая выполняется в данный момент и из-за которой происходит сбой настройки защиты. Решите проблему рекомендуемым способом, а затем нажмите кнопку «Перезапуск» для повторного запуска операции.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image6.png)

Возможность перезапуска доступна не для всех операций. В случае операций без возможности перезапуска перейдите обратно к объекту и повторить операцию еще раз. Каждое задание можно отменить в любой момент времени во время выполнения, нажав кнопку «Отмена».

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image7.png)

## Мониторинг работоспособности репликации для виртуальной машины

ASR предоставляет возможность центрального и удаленного мониторинга каждой защищенной сущности через портал Azure. Перейдите на вкладку «Защищенные элементы» и выберите «Облака VMM» или «Группы защиты». Вкладка «Облака VMM» предназначена только для развертывания на основе VMM, а для всех других сценариев защищенные сущности находятся на вкладке «Группы защиты».

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image8.png)

Затем выберите защищенную сущность в соответствующем облаке или группе защиты. После выбора защищенной сущности все допустимые операции отображаются в нижней области.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image9.png)

Как показано выше, в случае виртуальной машины работоспособность крайне важна. Чтобы просмотреть ошибку, можно нажать кнопку «Сведения об ошибке», расположенную внизу. Решите проблему, руководствуясь указанными возможными причинами и рекомендациями. В этом случае требуется повторно синхронизировать виртуальную машину, что можно выполнить на самом портале, нажав кнопку «Синхронизировать».

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image10.png)

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image11.png)

Примечание. Если имеются какие-либо выполняющиеся или неудачно завершенные активные операции, перейдите на вкладку «Задания», как упоминалось ранее, чтобы просмотреть ошибку задания.

## Устранение неполадок локальной среды

Подключитесь к консоли диспетчера Hyper-V локальных сред, выберите виртуальную машину и проверьте работоспособность репликации.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image12.png)

В этом случае *работоспособность репликации* обозначена как критическая. Для просмотра дополнительных сведений нажмите *Просмотреть состояние работоспособности репликации*.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image13.png)

#### Средство просмотра событий

| Сценарии | Источники событий |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Защита сайтов VMM | VMM Server <ul><li> **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Узел Hyper-V <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Для Azure как целевого объекта)</li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Защита сайтов Hyper-V | <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** </li><li> **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|

#### Параметры ведения журнала репликации Hyper-V

Все события, относящиеся к реплике Hyper-V, записываются в журнал Hyper-V-VMMS\\Admin log, расположенный в папке **Applications and Services Logs\\Microsoft\\Windows**. Кроме того можно также включить аналитический журнал для Hyper-V-VMMS. Чтобы включить этот журнал, сначала убедитесь, что аналитический журнал и журнал отладки видны в средстве просмотра событий. Откройте средство просмотра событий, в **меню «Вид»** выберите **Отобразить аналитический и отладочный журналы**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image14.png)

Аналитический журнал отображается в группе Hyper-V-VMMS.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image15.png)

В области **Действия** выберите **Включить журнал**. После включения журнала он отображается в **системном мониторе ** как сеанс трассировки событий в области **Группы сборщиков данных**.

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image16.png)

Чтобы просмотреть собранные сведения, сначала остановите сеанс трассировки, отключив журнал. Затем сохраните журнал и снова откройте его в средстве просмотра событий или при необходимости используйте другие средства для его преобразования.


## Основные сведения о жизненном цикле виртуальной машины

![](media/site-recovery-monitoring-and-troubleshooting-for-vmm-and-hyperv-site/image17.png)


## Служба технической поддержки Майкрософт

### Сбор журналов

Защита сайтов VMM: см. [сбор журналов ASR с помощью средства Support Diagnostics Platform (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx), предназначенного для сбора необходимых журналов.

Защита филиалов Hyper-V и сайтов SMB: загрузите [средство](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) и запустите его на узле Hyper-V для сбора журналов.

Сценарии для VMware или физических сайтов: см. [Сбор журналов Azure Site Recovery для защиты VMware и физических сайтов](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) для сбора необходимых журналов.

Средство SDP собирает локальный файл журнала, который также может быть расположен в папке с любым именем в подпапке **%LocalAppData%\\ElevatedDiagnostics**

### Открытие запроса в службу поддержки

Чтобы создать запрос в службу поддержки по ASR, перейдите на страницу службы поддержки Azure по URL-адресу <http://aka.ms/getazuresupport>

## Статьи базы знаний

-   [Как сохранить букву диска для защищенных виртуальных машин
    > http://support.microsoft.com/kb/3031135

-   [Способы устранения неполадок в службах восстановления
    > Azure](http://support.microsoft.com/kb/3005185)

-   [Как включить ведение журнала отладки для Azure Site Recovery при защите
    > сайта Hyper-V(http://support.microsoft.com/kb/3033922)

-   [ASR: Ошибка «Не удалось найти ресурс кластера» при попытке
    > включить защиту виртуальной машины](http://support.microsoft.com/kb/3010979)
    
-   [Общие сведения и руководство по устранению неполадок реплики
    > Hyper-V](http://www.microsoft.com/ru-ru/download/details.aspx?id=29016)

<!---HONumber=62-->