---
title: 'Azure Site Recovery: вопросы и ответы | Документация Майкрософт'
description: В этой статье приводятся ответы на распространенные вопросы об использовании Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: 0ec7f9e692dd4269ccb428d644c673e5c27b413a
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312684"
---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery: вопросы и ответы
Данная статья содержит часто задаваемые вопросы об Azure Site Recovery. Если после прочтения статьи у вас возникли какие-либо вопросы, вы можете задать их на [форуме, посвященном службам восстановления Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Общие сведения
### <a name="what-does-site-recovery-do"></a>Какие функции выполняет служба Site Recovery?
Служба Site Recovery помогает реализовать стратегии непрерывности бизнес-процессов и аварийного восстановления (BCDR), автоматизируя процессы репликации виртуальных машин Azure между регионами, локальных виртуальных машин и физических серверов в Azure, а также локальных виртуальных машин в дополнительный центр обработки данных и осуществляя управление ими. [Узнайте больше](site-recovery-overview.md).

### <a name="what-can-site-recovery-protect"></a>Что можно защитить с помощью службы Site Recovery?
* **Виртуальные машины Azure**. Служба Site Recovery может реплицировать любую нагрузку, выполняющуюся на поддерживаемой виртуальной машине Azure.
* **Виртуальные машины Hyper-V**. Служба Site Recovery позволяет защитить любую рабочую нагрузку, запущенную на виртуальной машине Hyper-V.
* **Физические серверы**. Служба Site Recovery позволяет защитить физические серверы под управлением Windows или Linux.
* **Виртуальные машины VMware**. Служба Site Recovery позволяет защитить любую рабочую нагрузку, запущенную на виртуальной машине VMware.



### <a name="can-i-replicate-azure-vms"></a>Можно ли выполнить репликацию виртуальных машин Azure?
Да, поддерживаемые виртуальные машины Azure можно реплицировать между регионами Azure. [Узнайте больше](site-recovery-azure-to-azure.md).

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Что нужно в Hyper-V для управления репликацией с помощью Site Recovery?
Требования для сервера узла Hyper-V зависят от сценария развертывания. Ознакомьтесь с предварительными требованиями к Hyper-V:

* [Репликация виртуальных машин Hyper-V в Azure (без использования VMM)](site-recovery-hyper-v-site-to-azure.md)
* [Репликация виртуальных машин Hyper-V в Azure (с помощью VMM)](site-recovery-vmm-to-azure.md)
* [Репликация виртуальных машин Hyper-V в дополнительный центр обработки данных](site-recovery-vmm-to-vmm.md)
* Если вы выполняете репликацию на вторичный центр обработки данных, прочтите о [поддерживаемых гостевых операционных системах для виртуальных машин Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* При репликации в Azure служба Site Recovery поддерживает все гостевые операционные системы на виртуальной машине, [поддерживаемые Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Можно ли защитить виртуальные машины при запуске Hyper-V на клиентской операционной системе?
Нет, виртуальные машины должны быть расположены на сервере узла Hyper-V, который работает на компьютере под управлением поддерживаемой версии Windows Server. Если вам нужно защитить клиентский компьютер, то вы можете реплицировать его как физический компьютер в [Azure](site-recovery-vmware-to-azure.md) или [дополнительный центр обработки данных](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Какие рабочие нагрузки можно защитить с помощью службы Site Recovery?
Службу Site Recovery можно использовать для защиты большинства рабочих нагрузок, запущенных на поддерживаемой виртуальной машине или физическом сервере. Служба Site Recovery также поддерживает репликацию уровня приложения, позволяя восстанавливать приложения до программируемого состояния. Она интегрируется с приложениями Майкрософт, включая SharePoint, Exchange, Dynamics, SQL Server и Active Directory, а также тесно взаимодействует с решениями ведущих производителей, в том числе Oracle, SAP, IBM и Red Hat. [Подробнее](site-recovery-workload.md) о защите рабочей нагрузки.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Должны ли узлы Hyper-V размещаться в облаках VMM?
Если требуется репликация во вторичный центр обработки данных, то виртуальные машины Hyper-V должны быть расположены на серверах узлов Hyper-V в облаке VMM. Но если предполагается репликация в Azure, ее можно выполнить, реплицировав виртуальные машины как с использованием облаков VMM, так и без них. [Узнайте больше](tutorial-hyper-v-to-azure.md) о репликации Hyper-V в Azure.

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Можно ли развернуть службу Site Recovery с помощью VMM при наличии только одного сервера VMM?

Да. Вы можете реплицировать виртуальные машины на серверах Hyper-V в облаке VMM в Azure или между облаками VMM на том же сервере. Для репликации между локальными средами мы рекомендуем использовать сервер VMM как на первичном, так и на вторичном сайте.  

### <a name="what-physical-servers-can-i-protect"></a>Какие физические серверы можно защитить?
Можно реплицировать физические серверы под управлением Windows и Linux в Azure или на вторичный сайт. Узнайте больше о требованиях к [репликация в Azure](vmware-physical-azure-support-matrix.md#replicated-machines) и [репликации на дополнительный сайт](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


Обратите внимание, что если локальный сервер выйдет из строя, физические серверы запустятся в качестве виртуальных машин в Azure. Сейчас восстановление размещения на локальный физический сервер не поддерживается, но возможно восстановление размещения компьютера, защищенного как физический, на виртуальную машину VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Какие виртуальные машины VMware можно защитить?

Для защиты виртуальных машин VMware вам понадобится низкоуровневая оболочка vSphere и виртуальные машины с запущенными инструментами VMware. Мы также рекомендуем использовать сервер VMware vCenter для управления низкоуровневыми оболочками. Узнайте больше о требованиях к [репликация в Azure](vmware-physical-azure-support-matrix.md#replicated-machines) или [репликации на дополнительный сайт](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Можно ли управлять аварийным восстановлением в офисах филиалов с помощью службы Site Recovery?
Да. Используя службу Site Recovery для управления репликацией и отработкой отказа в офисах филиалов, вы получаете объединенные возможности управления и представление для всех рабочих нагрузок офисов филиалов в центральном расположении. Таким образом, можно легко выполнить отработку отказа и управлять аварийным восстановлением для всех филиалов, не выходя из главного офиса.

## <a name="pricing"></a>Цены
Если у вас возникли вопросы о ценах, см. раздел вопросов и ответов на [странице цен на Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

## <a name="security"></a>Безопасность
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Отправляются ли данные репликации в службу Site Recovery?
Нет, служба Site Recovery не перехватывает реплицируемые данные и не получает сведения о компонентах, запущенных на ваших виртуальных машинах или физических серверах.
Обмен данными репликации осуществляется между локальными узлами Hyper-V, низкоуровневыми оболочками VMware или физическими серверами и хранилищем Azure или дополнительным сайтом. Служба Site Recovery не обладает способностью перехватывать эти данные. В службу Site Recovery передаются только метаданные, необходимые для управления репликацией и отработкой отказа.  

Site Recovery имеет сертификаты ISO 27001:2013, 27018, HIPAA, DPA и сейчас проходит проверки на соответствие SOC2 и FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Для соответствия требованиям даже метаданные из локальных сред должны оставаться в одном географическом регионе. Может ли служба Site Recovery помочь нам?
Да. При создании хранилища Site Recovery в регионе мы обеспечиваем хранение всех метаданных, необходимых для управления репликацией и отработкой отказа, в пределах этого региона.

### <a name="does-site-recovery-encrypt-replication"></a>Выполняет ли служба Site Recovery шифрование репликации?
При репликации виртуальных машин и физических серверов между локальными сайтами поддерживается шифрование в процессе передачи. При репликации виртуальных машин и физических серверов в Azure поддерживается как шифрование при передаче, так и [шифрование на месте (в Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption).

## <a name="replication"></a>Репликация

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Можно выполнить репликацию через VPN-подключение типа "сеть — сеть" в Azure?
Azure Site Recovery реплицирует данные в учетную запись хранения Azure через общедоступную конечную точку. Репликация не выполняется через VPN-подключение типа "сеть — сеть". Вы можете создать такое подключение с помощью виртуальной сети Azure. Это никак не повлияет на репликацию Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Можно ли использовать ExpressRoute для репликации виртуальных машин в Azure?
Да, [ExpressRoute можно использовать](concepts-expressroute-with-site-recovery.md) для репликации локальных виртуальных машин в Azure. Azure Site Recovery реплицирует данные в учетную запись хранения Azure через общедоступную конечную точку. Чтобы использовать ExpressRoute для репликации Site Recovery, необходимо настроить [общедоступный пиринг](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) или [пиринг Майкрософт](../expressroute/expressroute-circuit-peerings.md#microsoft-peering). Пиринг Майкрософт является рекомендуемым доменом маршрутизации для репликации. После отработки отказа виртуальных машин в виртуальную сеть Azure доступ к можно получать с помощью [частного пиринга](../expressroute/expressroute-circuit-peerings.md#azure-private-peering), настроенного в виртуальной сети Azure. Репликация через частный пиринг не поддерживается.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Существуют ли предварительные требования для репликации виртуальных машин в Azure?
Виртуальные машины [VMware](vmware-physical-azure-support-matrix.md#replicated-machines) и [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms), которые необходимо реплицировать в Azure, должны соответствовать требованиям Azure.

Учетная запись пользователя Azure должна содержать определенные [разрешения](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) для включения репликации новой виртуальной машины в Azure.

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Можно ли реплицировать виртуальные машины Hyper-V второго поколения в Azure?
Да. Во время отработки отказа Site Recovery преобразует машины второго поколения в первое. При восстановлении размещения машина преобразуется обратно во второе поколение. [Подробная информация](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Как осуществляется оплата за виртуальные машины при репликации в Azure?
При регулярной репликации данные реплицируются в геоизбыточное хранилище Azure, поэтому нет необходимости платить за использование виртуальных машин Azure IaaS, что является значительным преимуществом. При отработке отказа в Azure служба Site Recovery автоматически создает виртуальные машины Azure IaaS, после чего вам будут выставляться счета за вычислительные ресурсы, используемые в Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Можно ли автоматизировать сценарии Site Recovery с помощью пакета SDK?
Да. Рабочие процессы службы Site Recovery можно автоматизировать с помощью интерфейса REST API, PowerShell или пакета SDK для Azure. Ниже перечислены сценарии развертывания Site Recovery с помощью PowerShell, которые поддерживаются в настоящее время.

* [Репликация виртуальных машин Hyper-V из облаков VMM в Azure с помощью PowerShell (модель Resource Manager)](hyper-v-vmm-powershell-resource-manager.md)
* [Репликация виртуальных машин Hyper-V (без VMM) в Azure с помощью PowerShell (модель Resource Manager)](hyper-v-azure-powershell-resource-manager.md)
* [Репликация из VMware в Azure с помощью PowerShell для Resource Manager](vmware-azure-disaster-recovery-powershell.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Какая учетная запись хранения требуется при репликации в Azure?
Требуется учетная запись хранения LRS или GRS. Рекомендуется использовать учетную запись хранения GRS, чтобы обеспечить устойчивость данных в случае отключения электричества в регионе или при отсутствии возможности восстановления основного региона. Учетная запись должна находиться в том же регионе, что и хранилище служб восстановления. При развертывании Site Recovery на портале Azure хранилище класса Premium поддерживается для репликации виртуальных машин VMware, Hyper-V и физических серверов.

### <a name="how-often-can-i-replicate-data"></a>Как часто можно реплицировать данные?
* **Hyper-V.** Виртуальные машины Hyper-V можно реплицировать каждые 30 секунд (но не для хранилища класса Premium), 5 или 15 минут. При настройке репликации сети SAN репликация является синхронной.
* **VMware и физические серверы.** Здесь частота репликации не имеет значения. Репликация является непрерывной.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Можно ли расширить репликацию с существующего сайта восстановления до сайта третьего уровня?
Расширенная репликация и цепочка репликации не поддерживаются. Запросите эту функцию на [форуме отзывов и предложений](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Можно ли выполнить первую процедуру репликации в Azure в автономном режиме?
Эта возможность не поддерживается. Запросите эту функцию на [форуме отзывов и предложений](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Можно ли исключить из репликации отдельные диски?
Эта возможность поддерживается при репликации виртуальных машин VMware и Hyper-V в Azure с помощью портала Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Можно ли реплицировать виртуальные машины с динамическими дисками?
Динамические диски поддерживаются при репликации виртуальных машин Hyper-V. Они также поддерживаются при репликации виртуальных машин VMware и физических компьютеров в Azure. Диск операционной системы должен представлять собой базовый диск.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Можно ли добавить новый компьютер в существующую группу репликации?
Добавление новых компьютеров в существующие группы репликации поддерживается. Для этого выберите группу репликации (в колонке "Реплицированные элементы") и щелкните правой кнопкой мыши (или вызовите контекстное меню) группу репликации и выберите соответствующий пункт.

![Добавление в группу репликации](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Можно ли регулировать пропускную способность, выделенную для трафика репликации Hyper-V?
Да. Дополнительные сведения о регулировании пропускной способности можно прочитать в приведенных ниже статьях о развертывании.

* [Планирование ресурсов для репликации виртуальных машин VMware и физических серверов](site-recovery-plan-capacity-vmware.md)
* [Запуск планировщика ресурсов Hyper-V для Site Recovery](site-recovery-capacity-planning-for-hyper-v-replication.md)

## <a name="failover"></a>Отработка отказа
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Как получить доступ к виртуальным машинам Azure после отработки отказа в Azure?
Доступ к виртуальным машинам Azure можно получить через безопасное подключение к Интернету, подключение VPN типа "сеть — сеть" или с помощью Azure ExpressRoute. Для подключения потребуется выполнить несколько подготовительных действий. [Подробнее](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Каким образом среда Azure обеспечивает отказоустойчивость данных при отработке отказа?
Среда Azure разработана для обеспечения отказоустойчивости. Служба Site Recovery изначально рассчитана на отработку отказа в дополнительный центр обработки данных Azure в соответствии с соглашением об уровне обслуживания Azure (при необходимости). При обработке отказа Azure обеспечивает хранение метаданных и хранилищ в географическом регионе, выбранном для хранилища.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Что произойдет, если при выполнении репликации между двумя центрами обработки данных в основном центре обработки данных неожиданно возникнет сбой?
Можно активировать незапланированную отработку отказа на дополнительном сайте. Для отработки отказа службе Site Recovery не требуется подключение на основном сайте.

### <a name="is-failover-automatic"></a>Отработка отказа выполняется автоматически?
Отработка отказа не выполняется автоматически. Чтобы активировать ее, необходимо щелкнуть в соответствующем месте на портале или воспользоваться [Site Recovery PowerShell](/powershell/module/azurerm.siterecovery) . Восстановление размещения — простое действие, которое выполняется на портале Site Recovery.

Чтобы автоматизировать выполнение этих процедур, можно использовать локальное решение Orchestrator или Operations Manager для обнаружения сбоя виртуальной машины, а затем активировать отработку отказа, используя пакет SDK.

* Дополнительные сведения о планах восстановления см. [здесь](site-recovery-create-recovery-plans.md).
* [Узнайте больше](site-recovery-failover.md) об отработке отказа.
* [Узнайте больше](site-recovery-failback-azure-to-vmware.md) о восстановлении размещения виртуальных машин VMware и физических серверов.

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-failover-back-to-a-different-host"></a>Если локальный узел не отвечает или поврежден, можно ли выполнить отработку отказа на другой узел?
Да. Вы можете выполнить восстановление на другой узел из Azure. Дополнительные сведения о доступных вариантах см. в приведенных ниже ресурсах для виртуальных машин VMware и Hyper-V.

* [Для виртуальных машин VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Для виртуальных машин Hyper-V](hyper-v-azure-failback.md#perform-failback)

## <a name="service-providers"></a>Поставщики услуг
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Я являюсь поставщиком услуг. Работает ли служба Site Recovery для моделей выделенной и общей инфраструктуры?
Да, Site Recovery поддерживает как выделенную, так и общую модели инфраструктуры.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Предоставляется ли удостоверение клиента поставщика услуг службе Site Recovery?
Нет. Клиент остается анонимным. Клиентам не требуется доступ к порталу Site Recovery. Только администратор поставщика служб взаимодействует с порталом.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Будут ли данные приложения клиента передаваться в Azure?
При репликации между сайтами, принадлежащими поставщику служб, данные приложения никогда не передаются в Azure. Данные шифруются при передаче и реплицируются непосредственно между сайтами поставщика услуг.

При репликации в Azure данные приложения передаются в службу хранилища Azure, а не в службу Site Recovery. Данные шифруются в процессе передачи и остаются зашифрованными в Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Будут ли клиенты получать счета за какие-либо службы Azure?
Нет. Azure выставляет счета только поставщику службы. Поставщики служб отвечают за создание специальных счетов для своих клиентов.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Нужно ли всегда запускать виртуальные машины при репликации в Azure?
Нет, данные реплицируются в учетную запись хранения Azure в вашей подписке. При тестовой отработке отказа (отработке аварийного восстановления) или фактической отработке отказа Site Recovery автоматически создает виртуальные машины в вашей подписке.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Обеспечивается ли изоляция на уровне клиента при репликации в Azure?
Да.

### <a name="what-platforms-do-you-currently-support"></a>Какие платформы поддерживаются на данный момент?
Поддерживаются развертывания на основе Azure Pack, системы облачной платформы и System Center 2012 и более поздних версий. [Узнайте больше](https://technet.microsoft.com/library/dn850370.aspx) об интеграции Azure Pack и Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Поддерживаются ли отдельные развертывания Azure Pack или односерверные развертывания VMM?
Да, можно выполнять репликацию виртуальных машин Hyper-V в Azure или между сайтами поставщиков служб.  Обратите внимание, что при репликации между сайтами поставщика услуг интеграция Runbook Azure недоступна.

## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь со статьей [Обзор Site Recovery](site-recovery-overview.md)
* Ознакомьтесь с [архитектурой Site Recovery](site-recovery-components.md).  
