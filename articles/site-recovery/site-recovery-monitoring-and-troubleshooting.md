---
title: "Мониторинг и устранение неполадок защиты виртуальных машин и физических серверов | Документация Майкрософт"
description: "Служба Azure Site Recovery управляет репликацией, отработкой отказа и восстановлением виртуальных машин, размещенных на локальных серверах, с использованием Azure или дополнительного центра данных. Эта статья поможет вам отслеживать и устранять неполадки при защите сайтов Virtual Machine Manager или Hyper-V."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 0fc8e368-0c0e-4bb1-9d50-cffd5ad0853f
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: rajanaki
ms.translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 664a1bd9df9ace4993a8389dbeb049e721932082
ms.contentlocale: ru-ru
ms.lasthandoff: 03/06/2017


---
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Мониторинг и устранение неполадок защиты виртуальных машин и физических серверов
Это руководство по мониторингу и устранению неполадок поможет вам научиться отслеживать работоспособность репликации и устранять неполадки в Azure Site Recovery.

## <a name="understand-the-components"></a>Общие сведения о компонентах
### <a name="vmware-virtual-machine-or-physical-server-site-deployment-for-replication-between-on-premises-and-azure"></a>Развертывание сайта на виртуальной машине VMware или физическом сервере для репликации между локальными средами и Azure
Для настройки восстановления базы данных между локальной виртуальной машиной VMware или физическим сервером и Azure на виртуальной машине или сервере необходимо настроить компоненты сервера конфигурации, главного целевого сервера и сервера обработки. При включении защиты исходного сервера служба Azure Site Recovery устанавливает компонент мобильных приложений службы приложений Microsoft Azure. После локального отключения и перехода исходного сервера в Azure, клиентам необходимо настроить сервер обработки в Azure и главный целевой сервер в локальной среде для восстановления исходного сервера в локальной среде.

![Развертывание сайта VMware или физического сайта для репликации между локальной средой и Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-sites"></a>Развертывание сайта Virtual Machine Manager для репликации между локальными сайтами
Для настройки восстановления базы данных между двумя локальными расположениями необходимо скачать поставщик Azure Site Recovery и установить его на сервере Virtual Machine Manager. Чтобы обеспечить преобразование всех операций, запускаемых с портала Azure, в локальные операции, следует подключить поставщик к Интернету.

![Развертывание сайта Virtual Machine Manager для репликации между локальными сайтами](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="virtual-machine-manager-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Развертывание сайта Virtual Machine Manager для репликации между локальными расположениями и Azure
Во время настройки восстановления базы данных между локальными расположениями и Azure необходимо скачать поставщик Azure Site Recovery и установить его на сервере Virtual Machine Manager. Необходимо также на каждом узле Hyper-V установить агент служб восстановления Azure. Дополнительные сведения см. [здесь](site-recovery-hyper-v-azure-architecture.md).

![Развертывание сайта Virtual Machine Manager для репликации между локальными расположениями и Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises-locations-and-azure"></a>Развертывание сайта Hyper-V для репликации между локальными расположениями и Azure
Этот процесс схож с развертыванием Virtual Machine Manager. Единственное отличие заключается в том, что поставщик Azure Site Recovery и агент служб восстановления Azure устанавливаются на самом узле Hyper-V. [Подробнее](site-recovery-hyper-v-azure-architecture.md). .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Мониторинг операций настройки, защиты и восстановления
Каждая операция в Azure Site Recovery контролируется и отслеживается на вкладке **Задания**. Чтобы обнаружить ошибки настройки, защиты или восстановления, просмотрите сбои на вкладке **Задания**.

![Фильтр "Сбой" на вкладке "Задания"](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Если на вкладке **Задания** обнаружены сбои, выберите соответствующее задание и нажмите кнопку **Сведения об ошибке** для этого задания.

![Кнопка "Сведения об ошибке"](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Сведения об ошибке позволяют определить возможные причины проблемы и содержат рекомендации по ее устранению.

![Диалоговое окно со сведениями об ошибке для задания](media/site-recovery-monitoring-and-troubleshooting/image5.png)

В предыдущем примере сбой настройки защиты, вероятно, вызван другой выполняемой операцией. Устраните проблему, как указано в рекомендациях, и нажмите кнопку **Перезагрузить**, чтобы снова запустить операцию.

![Кнопка "Перезагрузить" на вкладке "Задания"](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Параметр **Перезагрузить** доступен не для всех операций. Если для операции нет параметра **Перезагрузить**, вернитесь к объекту и повторите операцию снова. Любое выполняемое задание можно отменить с помощью кнопки **Отмена**.

![Кнопка "Отмена"](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machines"></a>Мониторинг работоспособности репликации для виртуальных машин
На портале Azure можно осуществлять удаленный мониторинг поставщиков Azure Site Recovery для всех защищенных объектов. Перейдите на вкладку **Защищенные элементы** и выберите вкладку **Облака VMM** или **Группы защиты**. Вкладка **Облака VMM** доступна только для развертываний на основе Virtual Machine Manager. В других случаях защищенные объекты находятся на вкладке **Группы защиты**.

![Параметры "Облака VMM" и "Группы защиты"](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Выберите защищенный объект в соответствующем облаке или группе защиты, чтобы просмотреть все доступные операции в области внизу.

![Параметры, доступные для выбранного защищенного объекта](media/site-recovery-monitoring-and-troubleshooting/image9.png)

На предыдущем снимке экрана показано, что состояние виртуальной машины **критическое**. Чтобы просмотреть сведения об ошибке, нажмите кнопку **Сведения об ошибке** внизу. Устраните проблему, руководствуясь сведениями в разделах **Возможные причины** и **Рекомендации**.

![Кнопка "Сведения об ошибке"](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Ошибки и рекомендации в диалоговом окне "Сведения об ошибке"](media/site-recovery-monitoring-and-troubleshooting/image11.png)

> [!NOTE]
> Если имеются какие-либо выполняющиеся или неудачно завершенные активные операции, перейдите на вкладку **Задания**, как упоминалось выше, чтобы просмотреть ошибку конкретного задания.
>
>

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Устранение неполадок локальной среды Hyper-V
Подключитесь к локальной консоли диспетчера Hyper-V, выберите виртуальную машину и проверьте работоспособность репликации.

![Команда для просмотра работоспособности репликации в консоли диспетчера Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image12.png)

В этом случае параметр **Работоспособность репликации** имеет значение **Критическое**. Чтобы просмотреть подробные сведения, щелкните виртуальную машину правой кнопкой мыши и выберите пункты **Репликация** > **Просмотреть состояние работоспособности репликации**.

![Работоспособность репликации для конкретной виртуальной машины](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Если репликация виртуальной машины приостановлена, щелкните ее правой кнопкой мыши и выберите параметры **Репликация** > **Возобновить репликацию**.

![Команда для возобновления репликации в консоли диспетчера Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image19.png)

Перенос виртуальной машины на новый узел Hyper-V, настроенный с помощью Azure Site Recovery, в пределах одного и того же кластера или отдельного компьютера не повлияет на репликацию. Убедитесь, что новый узел Hyper-V соответствует всем предварительным требованиям и настроен с помощью Azure Site Recovery.

### <a name="event-log"></a>Журнал событий
| Источники событий | Сведения |
| --- |:--- |
| **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (сервер Virtual Machine Manager) |Здесь представлены журналы, с помощью которых можно устранять различные проблемы Virtual Machine Manager. |
| **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (узел Hyper-V) |Здесь представлены журналы, с помощью которых можно устранять различные проблемы агента служб восстановления Microsoft Azure. <br/> ![Расположение источника события репликации для узла Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (узел Hyper-V) |Здесь представлены журналы, с помощью которых можно устранять различные проблемы службы Microsoft Azure Site Recovery. <br/> ![Расположение источника операционного события для узла Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (узел Hyper-V) |Здесь представлены журналы, с помощью которых можно устранять различные проблемы, связанные с управлением виртуальной машины Hyper-V. <br/> ![Расположение источника событий Virtual Machine Manager для узла Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |

### <a name="hyper-v-replication-logging-options"></a>Параметры ведения журнала репликации Hyper-V
Все события, относящиеся к репликации Hyper-V, записываются в журнал Hyper-V-VMMS\\Admin log, расположенный в папке Applications and Services Logs\\Microsoft\\Windows. Кроме того, для службы управления виртуальными машинами Hyper-V можно включить аналитический журнал. Чтобы включить этот журнал, сначала убедитесь, что аналитический журнал и журнал отладки видны в средстве просмотра событий. Откройте средство просмотра событий и выберите элементы **Представление** > **Отобразить аналитический и отладочный журналы**.

![Параметр "Отобразить аналитический и отладочный журналы"](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Аналитический журнал отображается в группе **Hyper-V-VMMS**.

![Аналитический журнал в дереве средства просмотра событий](media/site-recovery-monitoring-and-troubleshooting/image15.png)

В области **Действия** выберите **Включить журнал**. После включения журнал отображается в **системном мониторе** как **сеанс трассировки событий** в области **Группы сборщиков данных**.

![Сеансы трассировки событий в дереве системного монитора](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Для просмотра собранных сведений сначала остановите сеанс трассировки, отключив журнал. Сохраните журнал и откройте его снова в средстве просмотра событий или используйте другое средство для преобразования журнала в необходимый формат.

## <a name="reach-out-for-microsoft-support"></a>Служба технической поддержки Майкрософт
### <a name="log-collection"></a>Сбор журналов
Для защиты сайтов Virtual Machine Manager см. статью [Azure Site Recovery log collection using Support Diagnostics Platform (SDP) Tool](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) (Сбор журналов Azure Site Recovery с помощью средства Support Diagnostics Platform (SDP)). Она содержит сведения о сборе необходимых журналов.

Для защиты сайтов Hyper-V скачайте [это средство](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) и запустите его на узле Hyper-V для сбора журналов.

Для защиты VMware или физических серверов см. статью [Azure Site Recovery log collection for VMware and physical site protection](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) (Сбор журналов Azure Site Recovery для защиты VMware и физических компьютеров). Она содержит сведения о сборе необходимых журналов.

Средство собирает журналы локально и сохраняет их в подпапке каталога %LocalAppData%\ElevatedDiagnostics со случайным именем.

![В примере показана защита сайта Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="open-a-support-ticket"></a>Открытие запроса в службу поддержки
Чтобы создать запрос в службу поддержки по Azure Site Recovery, перейдите на страницу службы поддержки Azure по адресу <http://aka.ms/getazuresupport>.

## <a name="kb-articles"></a>Статьи базы знаний
* [Сохранение буквы диска для защищенных виртуальных машин, для которых выполнена отработка отказа или перенос в Azure](http://support.microsoft.com/kb/3031135)
* [Управление локальными ресурсами для использования пропускной способности защищенной сети Azure](https://support.microsoft.com/kb/3056159)
* [ASR: ошибка "Не удается найти ресурс кластера" при попытке включить защиту для виртуальной машины](http://support.microsoft.com/kb/3010979)
* [Общие сведения и руководство по устранению неполадок репликации Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/21948.hyper-v-replica-troubleshooting-guide.aspx)

## <a name="common-azure-site-recovery-errors-and-their-resolutions"></a>Распространенные ошибки ASR и способы их устранения
Ниже приведены распространенные ошибки и способы их устранения. Каждая ошибка описана на отдельной вики-странице.

### <a name="general"></a>Общие сведения
* <span style="color:green;">НОВОЕ</span> [Задания завершаются с ошибкой "Выполняется операция". Ошибка 505, 514, 532.](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
* <span style="color:green;">НОВОЕ</span> [Задания завершаются с ошибкой "Сервер не подключен к Интернету". Ошибка 25018.](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Настройка
* [Не удается зарегистрировать сервер Virtual Machine Manager из-за внутренней ошибки. Для получения дополнительных сведений об этой ошибке просмотрите представление заданий на портале Site Recovery. Запустите программу установки еще раз, чтобы зарегистрировать сервер.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
* [Не удается установить подключение к хранилищу диспетчера восстановления Hyper-V. Проверьте параметры прокси-сервера или повторите попытку позже.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Конфигурация
* [Невозможно создать группу защиты: при получении списка серверов произошла ошибка.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
* [Кластер узлов Hyper-V содержит по крайней мере один статический сетевой адаптер, или ни на одном из подключенных адаптеров не настроено использование DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
* [Virtual Machine Manager не имеет разрешений для выполнения действия.](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
* [Невозможно выбрать учетную запись хранения в подписке при настройке защиты.](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Защита
* <span style="color:green;">НОВОЕ</span> [Ошибка включения защиты с сообщением "Не удалось настроить защиту для виртуальной машины". Ошибка 60007, 40003.](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
* <span style="color:green;">НОВОЕ</span> [Ошибка включения защиты с сообщением "Не удалось включить защиту для виртуальной машины". Ошибка 70094.](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
* <span style="color:green;">НОВОЕ</span> [Ошибка динамической миграции 23848 — виртуальная машина будет перемещена с использованием типа Live. Это может нарушить состояние защиты восстановления у виртуальной машины.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
* [Включение защиты не выполнено, так как на компьютере не установлен агент.](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
* [Не удается найти подходящий узел для реплики виртуальной машины из-за недостатка вычислительных ресурсов.](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
* [Не удается найти подходящий узел для реплики виртуальной машины из-за того, что не подключена ни одна логическая сеть.](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
* [Не удается подключиться к узлу реплики — невозможно установить соединение.](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)

### <a name="recovery"></a>Восстановление
* Virtual Machine Manager не удалось завершить операцию с узлом.
  * [Отработка отказа на выбранную точку восстановления для виртуальной машины: общая ошибка доступа.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
  * [Hyper-V не удалось выполнить отработку отказа с использованием выбранной точки восстановления для виртуальной машины: операция прервана.  Укажите более новую точку восстановления. (0x80004004).](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
  * Не удается установить подключение к серверу (0x00002EFD).
    * [Hyper-V не удалось включить обратную репликацию для виртуальной машины.](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
    * [Hyper-V не удалось включить репликацию для виртуальной машины.](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
  * [Не удалось выполнить отработку отказа для виртуальной машины.](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
* [План восстановления содержит виртуальные машины, которые еще не готовы для запланированной отработки отказа.](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
* [Виртуальная машина не готова для запланированной отработки отказа.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* [Виртуальная машина не запущена и не отключена.](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
* [На виртуальной машине произошла незапланированная операция, и не удалось выполнить отработку отказа.](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
* Тестовая отработка отказа
  * [Не удалось начать отработку отказа, так как выполняется тестовая отработка отказа.](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
* <span style="color:green;">Новая информация.</span> Время ожидания отработки отказа заканчивается с сообщением "Завершилось время ожидания задачи PreFailoverWorkflow (PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout)" из-за настроек конфигурации в группе безопасности сети, связанной с виртуальной машиной или подсетью, которой принадлежит виртуальная машина. Подробности см. в статье [Завершилось время ожидания задачи PreFailoverWorkflow (PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout)](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery).

### <a name="configuration-server-process-server-master-target"></a>Сервер конфигурации, сервер обработки, главный целевой сервер
* [На узле ESXi, на котором работает PS/CS в виде виртуальной машины, произошел сбой с фиолетовым экраном смерти.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Устранение неполадок после отработки отказа удаленного рабочего стола
* Многие пользователи испытывают проблемы при подключении к виртуальной машине Azure после отработки отказа. [Используйте документ по устранению неполадок для подключения к виртуальной машине по протоколу удаленного рабочего стола](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Добавление общедоступного IP-адреса в виртуальную машину диспетчера ресурсов
Если кнопка **Подключить** на портале неактивна и вы не используете канал Express Route или VPN-подключение типа "сеть — сеть" для обмена данными с Azure, вам сначала нужно создать общедоступный IP-адрес и назначить его виртуальной машине. Только после этого вы сможете использовать удаленный рабочий стол или Shared Shell. Затем можно добавить общедоступный IP-адрес в сетевой интерфейс виртуальной машины.  

![Не удалось добавить общедоступный IP-адрес в сетевой интерфейс на виртуальной машине](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)

