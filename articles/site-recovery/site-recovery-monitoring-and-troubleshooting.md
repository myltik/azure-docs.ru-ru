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
	ms.date="07/20/2015" 
	ms.author="anbacker"/>
	
# Отслеживание и устранение неполадок защиты для VMware, VMM, Hyper-V и физических расположений

Это руководство по мониторингу и устранению неполадок позволяет узнать об отслеживании работоспособности репликации и способах устранения неполадок для Azure Site Recovery.

## Общие сведения о компонентах

### Развертывание сайта Hyper-V или физического сайта для репликации между локальными средами и Azure.
Для настройки DR между локальным компьютером VMware или физическим компьютером необходимо настроить сервер конфигурации, главный целевой сервер и сервер обработки. При включении защиты ля исходного сервера служба Azure Site Recovery установит службу Mobility Service. После локального отключения, когда исходный сервер перейдет в Azure, клиентам потребуется настроить сервер обработки в Azure и главный целевой сервер в локальной среде для защиты исходного сервера в перестроенной локальной среде.

![Развертывание в VMware и на физической машине для репликации между локальными средами и Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### Развертывание сайта VMM для репликации между локальными сайтами

В ходе настройки аварийного восстановления между двумя локальными средами необходимо загрузить поставщика Azure Site Recovery и установить на сервере VMM. Поставщику требуется подключение к Интернету, чтобы все операции с портала Azure преобразовались в локальные операции, такие как включение защиты, завершение работы виртуальных машин на основном сервере в рамках отработки отказа и т. д.

![Развертывание с использованием VMM для репликации между локальными средами](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### Развертывание сайта VMM для репликации между локальными средами и Azure

В ходе настройки аварийного восстановления между локальными средами и Azure необходимо загрузить поставщика Azure Site Recovery и установить на сервере VMM вместе с агентом служб восстановления Azure, который должен быть установлен на каждом узле Hyper-V.

![Развертывание с использованием VMM для репликации между локальными средами и Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### Развертывание сайта Hyper-V для репликации между локальными средами и Azure

Это то же, что и развертывание VMM, только на самом узле Hyper-V устанавливается другой поставщик и агент.

## Мониторинг операций настройки, защиты и восстановления

Каждая операция в ASR контролируется и отслеживается на вкладке «Задания». В случае возникновения какой-либо ошибки конфигурации, защиты или восстановления перейдите на вкладку «Задания» и проверьте, отображаются ли там сбои.

![Мониторинг операций настройки, защиты и восстановления](media/site-recovery-monitoring-and-troubleshooting/image3.png)

После обнаружения сбоя на вкладке «Задания» выберите задание и щелкните описание ошибки для этого задания.

![Мониторинг операций настройки, защиты и восстановления](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Сведения об ошибке позволяют выявить возможные причины и рекомендации о проблеме.

![Мониторинг операций настройки, защиты и восстановления](media/site-recovery-monitoring-and-troubleshooting/image5.png)

В приведенном выше случае есть другая операция, которая выполняется в данный момент и из-за которой происходит сбой настройки защиты. Решите проблему рекомендуемым способом, а затем нажмите кнопку «Перезапуск» для повторного запуска операции.

![Мониторинг операций настройки, защиты и восстановления](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Возможность перезапуска доступна не для всех операций. В случае операций без возможности перезапуска перейдите обратно к объекту и повторить операцию еще раз. Каждое задание можно отменить в любой момент времени во время выполнения, нажав кнопку «Отмена».

![Мониторинг операций настройки, защиты и восстановления](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## Мониторинг работоспособности репликации для виртуальной машины

ASR предоставляет возможность центрального и удаленного мониторинга каждой защищенной сущности через портал Azure. Перейдите на вкладку «Защищенные элементы» и выберите «Облака VMM» или «Группы защиты». Вкладка «Облака VMM» предназначена только для развертывания на основе VMM, а для всех других сценариев защищенные сущности находятся на вкладке «Группы защиты».

![Мониторинг работоспособности репликации для виртуальной машины](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Затем выберите защищенную сущность в соответствующем облаке или группе защиты. После выбора защищенной сущности все допустимые операции отображаются в нижней области.

![Мониторинг работоспособности репликации для виртуальной машины](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Как показано выше, в случае виртуальной машины работоспособность крайне важна. Чтобы просмотреть ошибку, можно нажать кнопку «Сведения об ошибке», расположенную внизу. Решите проблему, руководствуясь указанными возможными причинами и рекомендациями. В этом случае требуется повторно синхронизировать виртуальную машину, что можно выполнить на самом портале, нажав кнопку «Синхронизировать».

![Мониторинг работоспособности репликации для виртуальной машины](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Мониторинг работоспособности репликации для виртуальной машины](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Примечание. Если имеются какие-либо выполняющиеся или неудачно завершенные активные операции, перейдите на вкладку «Задания», как упоминалось ранее, чтобы просмотреть ошибку задания.

## Средство просмотра событий

| Сценарии | Источники событий |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| Защита сайтов VMM | VMM Server <ul><li> **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** </li></ul> Узел Hyper-V <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Для Azure как целевого объекта)</li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li></ul> |
| Защита сайтов Hyper-V | <ul><li> **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** </li><li> **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** </li><li> **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** </li><ul>|


## Устранение неполадок локальной среды Hyper-V

Подключитесь к консоли диспетчера Hyper-V локальных сред, выберите виртуальную машину и проверьте работоспособность репликации.

![Устранение неполадок локальной среды Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image12.png)

В этом случае *работоспособность репликации* обозначена как критическая. Для просмотра дополнительных сведений нажмите *Просмотреть состояние работоспособности репликации*.

![Устранение неполадок локальной среды Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image13.png)

### Параметры ведения журнала репликации Hyper-V

Все события, относящиеся к реплике Hyper-V, записываются в журнал Hyper-V-VMMS\Admin log, расположенный в папке **Applications and Services Logs\Microsoft\Windows**. Кроме того можно также включить аналитический журнал для Hyper-V-VMMS. Чтобы включить этот журнал, сначала убедитесь, что аналитический журнал и журнал отладки видны в средстве просмотра событий. Откройте средство просмотра событий, в **меню «Вид»** выберите **Отобразить аналитический и отладочный журналы**.

![Устранение неполадок локальной среды Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Аналитический журнал отображается в группе Hyper-V-VMMS.

![Устранение неполадок локальной среды Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image15.png)

В области **Действия** выберите **Включить журнал**. После включения журнала он отображается в **системном мониторе ** как сеанс трассировки событий в области **Группы сборщиков данных**.

![Устранение неполадок локальной среды Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Чтобы просмотреть собранные сведения, сначала остановите сеанс трассировки, отключив журнал. Затем сохраните журнал и снова откройте его в средстве просмотра событий или при необходимости используйте другие средства для его преобразования.


## Основные сведения о жизненном цикле защиты и восстановления виртуальных машин Hyper-V

![Основные сведения о жизненном цикле защиты и восстановления виртуальных машин Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image17.png)


## Служба технической поддержки Майкрософт

### Сбор журналов

Защита сайтов VMM: см. [сбор журналов ASR с помощью средства Support Diagnostics Platform (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx), предназначенного для сбора необходимых журналов.

Защита филиалов Hyper-V и сайтов SMB: загрузите [средство](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) и запустите его на узле Hyper-V для сбора журналов.

Сценарии для VMware или физических сайтов: см. [Сбор журналов Azure Site Recovery для защиты VMware и физических сайтов](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) для сбора необходимых журналов.

Средство SDP собирает локальный файл журнала, который также может быть расположен в папке с любым именем в подпапке **%LocalAppData%\ElevatedDiagnostics**

### Открытие запроса в службу поддержки

Чтобы создать запрос в службу поддержки по ASR, перейдите на страницу службы поддержки Azure по URL-адресу <http://aka.ms/getazuresupport>

## Статьи базы знаний

-   [Как сохранить букву диска для защищенных виртуальных машин
    > http://support.microsoft.com/kb/3031135

-   [Способы устранения неполадок в службах восстановления
    > Azure](http://support.microsoft.com/kb/3005185)

-   [Как включить ведение журнала отладки для Azure Site Recovery при защите
    > сайтов Hyper-V](http://support.microsoft.com/kb/3033922)

-   [ASR. Ошибка «Не удалось найти ресурс кластера» при попытке
    > включить защиту виртуальной машины](http://support.microsoft.com/kb/3010979)
    
-   [Общие сведения и руководство по устранению неполадок реплики
    > Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016)
	
	
	
## Распространенные ошибки ASR и способы их устранения

Ниже приведены распространенные ошибки, с которыми вы можете столкнуться, и способы их устранения. Каждая ошибка описана на отдельной вики-странице.

### Настройка

-   [Не удается проверить выбранный сертификат. Пожалуйста, выберите другой сертификат.](http://social.technet.microsoft.com/wiki/contents/articles/25569.the-selected-certificate-cannot-be-validated-please-select-a-different-certificate.aspx)

-   [Не удается зарегистрировать сервер VMM из-за внутренней ошибки. Пожалуйста, просмотрите представление заданий на портале Site Recovery для получения дополнительных сведений об этой ошибке. Запустите программу установки еще раз, чтобы зарегистрировать сервер.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)

-   [Не удается установить подключение к хранилищу диспетчера восстановления Hyper-V. Проверьте параметры прокси-сервера или повторите попытку позже.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### Конфигурация

-   [Кластер узлов Hyper-V содержит по крайней мере один статический сетевой адаптер, или ни на одном из подключенных адаптеров не настроено использование DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)

-   [Профиль Hyper-V не включен в Профилях возможностей облака](http://social.technet.microsoft.com/wiki/contents/articles/25499.the-hyper-v-profile-isn-t-enabled-in-the-capability-profiles-for-cloud.aspx)

-   [Невозможно применить конфигурацию защиты для '%CloudName;'. Не удалось настроить вновь добавленный узел Hyper-V или кластер, так как защита облака не настроена.](http://social.technet.microsoft.com/wiki/contents/articles/25500.protection-configuration-for-cloudname-couldn-t-be-applied-a-newly-added-hyper-v-host-or-cluster-couldn-t-be-configured-because-cloud-protection-isn-t-configured.aspx)

-   [VMM не имеет разрешений для выполнения действия](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)

### Защита

-   [Операционная система не поддерживается](http://social.technet.microsoft.com/wiki/contents/articles/31103.the-operating-system-is-not-supported.aspx)

-   [Включение защиты не выполнено, поскольку на компьютере не установлен агент](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)

-   [Не удается найти подходящий узел для реплики виртуальной машины из-за недостатка вычислительных ресурсов](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)

-   [Не удается найти подходящий узел для реплики виртуальной машины из-за того, что не подключена ни одна логическая сеть](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)

-   [Не удается подключиться к узлу реплики — невозможно установить соединение](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### Восстановление

-   VMM не удалось завершить операцию узла

    -   [Отработка отказа на выбранную точку восстановления для виртуальной машины: общая ошибка доступа.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)

    -   [Не удалось выполнить отработку отказа на выбранную точку восстановления для виртуальной машины Hyper-V: операция прервана. Попробуйте использовать более свежую точку восстановления. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)

    -   Соединение с сервером не может быть установлено (0x00002EFD)

        -   [Hyper-V не удалось включить обратную репликацию для виртуальной машины](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)

        -   [Hyper-V не удалось включить репликацию для виртуальной машины](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)

    -   [Не удалось выполнить отработку отказа для виртуальной машины](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)

-   [План восстановления содержит виртуальные машины, которые еще не готовы для запланированной отработки отказа](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)

-   [Виртуальная машина не готова для запланированной отработки отказа](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)

-   [Виртуальная машина не запущена и не отключена](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)

-   [На виртуальной машине произошла незапланированная операция, и ](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx) не удалось выполнить отработку отказа

-   Тестирование отработки отказа

    -   [Не удалось начать отработку отказа, так как выполняется тестовая отработка отказа](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)

-   Особые сообщения об ошибках при восстановлении в Azure

    -   [Не удалось начать отработку отказа из-за неподдерживаемых символов в имени виртуальной машины](http://social.technet.microsoft.com/wiki/contents/articles/31107.failover-could-not-be-started-due-to-unsupported-characters-in-the-vm-name.aspx)

### Сервер конфигурации, Сервер обработки, Главный целевой сервер

Сервер конфигурации (СК), Сервер обработки (СО), Главный целевой сервер (ГЦС)

-   [На узле ESXi, на котором работает PS/CS в виде виртуальной машины, произошел сбой с фиолетовым экраном смерти.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

<!---HONumber=July15_HO4-->