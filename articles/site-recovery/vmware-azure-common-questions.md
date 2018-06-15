---
title: Часто задаваемые вопросы. Репликация VMware в Azure с помощью Azure Site Recovery | Документация Майкрософт
description: В этой статье перечислены часто задаваемые вопросы о репликации локальных виртуальных машин VMware в Azure с помощью Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/15/2018
ms.author: raynew
ms.openlocfilehash: 345b73db423c6e12b56bb3308f7700917a372dda
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30185226"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Часто задаваемые вопросы о репликации из VMware в Azure

В этой статье приведены ответы на часто задаваемые вопросы, которые возникают при репликации локальных виртуальных машин VMware в Azure. Если после прочтения статьи у вас возникли какие-либо вопросы, вы можете задать их на [форуме, посвященном службам восстановления Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Общие сведения
### <a name="how-is-site-recovery-priced"></a>Как образуются цены на Site Recovery?
Ознакомьтесь со сведениями о [расценках на Azure Site Recovery](https://azure.microsoft.com/en-in/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Как оплачивается использование виртуальных машин Azure?
Во время репликации данные реплицируются в хранилище Azure, и вы не платите за любые изменения виртуальной машины. При выполнении отработки отказа в Azure служба Site Recovery автоматически создаст виртуальные машины IaaS Azure. После этого вам будет выставлен счет за вычислительные ресурсы, использованные в Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>Что можно сделать с помощью репликации VMware в Azure?
- **Аварийное восстановление.** Можно настроить полное аварийное восстановление. В этом сценарии можно реплицировать локальные виртуальные машины VMware в службу хранилища Azure. Затем, если ваша локальная инфраструктура недоступна, можно выполнить отработку отказа в Azure. При выполнении отработки отказа создаются виртуальные машины Azure с реплицируемыми данными. Вы можете получать доступ к приложениям и рабочим нагрузкам на виртуальных машинах Azure, пока ваш локальный центр обработки данных не станет снова доступен. Затем можно восстановить размещение в локальной среде из Azure.
- **Миграция.** Site Recovery можно использовать для миграции локальных виртуальных машин в Azure. В этом сценарии выполняется репликация локальных виртуальных машин в хранилище Azure. Затем выполняется отработка отказа из локальной среды в Azure. После отработки отказа приложения и рабочие нагрузки доступны и выполняются на виртуальных машинах Azure.



## <a name="azure"></a>Таблицы Azure
### <a name="what-do-i-need-in-azure"></a>Что нужно в Azure?
Требуется подписка Azure, хранилище служб восстановления, учетная запись хранения и виртуальная сеть. Хранилище, учетная запись хранения и сеть должны находиться в одном регионе.

### <a name="what-azure-storage-account-do-i-need"></a>Какая учетная запись хранения Azure требуется?
Требуется учетная запись хранения LRS или GRS. Рекомендуется использовать учетную запись хранения GRS, чтобы обеспечить устойчивость данных в случае отключения электричества в регионе или при отсутствии возможности восстановления основного региона. Поддерживается хранилище класса Premium.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Требуются ли учетной записи Azure разрешения на создание виртуальных машин?
Если вы являетесь администратором подписки, у вас имеются необходимые разрешения на репликацию. Если нет, вам нужны разрешения на создание виртуальной машины Azure в группе ресурсов и виртуальной сети, которые указываются при настройке Site Recovery, и разрешения на запись в выбранную учетную запись хранения. [Узнайте больше](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).



## <a name="on-premises"></a>Локальная система 

### <a name="what-do-i-need-on-premises"></a>Что требуется в локальной среде?
В локальной среде вам требуются компоненты Site Recovery, установленные на одной виртуальной машине VMware. Также нужна инфраструктура VMware с как минимум одним узлом ESXi и (рекомендуется) сервер vCenter. Кроме того, требуется одна или несколько виртуальных машин VMware для репликации. [Дополнительные сведения](vmware-azure-architecture.md) об репликации VMware в архитектуру Azure.

Развернуть локальный сервер конфигурации можно одним из двух следующих способов:

1. Развертывание при помощи шаблона виртуальной машины с предварительно установленным сервером конфигурации. [Дополнительные сведения см. здесь](vmware-azure-tutorial.md#download-the-vm-template).
2. Развертывание при помощи программы установки на выбранном компьютере с Windows Server 2016. [Дополнительные сведения см. здесь](physical-azure-disaster-recovery.md#set-up-the-source-environment).

Чтобы приступить к развертыванию сервера конфигурации на собственном компьютере Windows Server с включением защиты, выполните инструкции в разделе **To Azure (В Azure) > Без виртуализации или иное**.

### <a name="where-do-on-premises-vms-replicate-to"></a>Куда реплицируются локальные виртуальные машины?
Данные реплицируются в хранилище Azure. При выполнении отработки Site Recovery автоматически создаст виртуальные машины Azure из учетной записи хранения.

### <a name="what-apps-can-i-replicate"></a>Какие приложения можно реплицировать?
Можно реплицировать любые приложения или рабочие нагрузки, выполняемые в виртуальной машине VMware, которые соответствуют [требованиям к репликации](vmware-physical-azure-support-matrix.md##replicated-machines). Служба Site Recovery также поддерживает репликацию уровня приложения, позволяя выполнять отработку отказа приложений и восстанавливать их размещение до программируемого состояния. Site Recovery интегрируется с приложениями Майкрософт, включая SharePoint, Exchange, Dynamics, SQL Server и Active Directory, а также тесно взаимодействует с решениями ведущих производителей, в том числе Oracle, SAP, IBM и Red Hat. [Подробнее](site-recovery-workload.md) о защите рабочей нагрузки.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>Можно выполнить репликацию через VPN-подключение "сеть — сеть" в Azure?
Site Recovery реплицирует данные из локальной среды в хранилище Azure через общедоступную конечную точку или с помощью общедоступного пиринга ExpressRoute. Репликация через VPN-подключение "сеть — сеть" не поддерживается.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>Можно ли реплицировать в Azure с помощью ExpressRoute?
Да, ExpressRoute можно использовать для репликации виртуальных машин в Azure. Site Recovery реплицирует данные в учетную запись хранения Azure через общедоступную конечную точку. Для этого вам требуется настроить [общедоступный пиринг](../expressroute/expressroute-circuit-peerings.md#azure-public-peering). После отработки отказа виртуальных машин в виртуальную сеть Azure к ним можно получить доступ с помощью [частного пиринга](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).


### <a name="why-cant-i-replicate-over-vpn"></a>Почему я не могу выполнить репликацию через VPN?

При репликации в Azure трафик репликации достигает общедоступных конечных точек учетной записи хранения Azure. Таким образом репликацию можно выполнять только через общедоступный Интернет с помощью ExpressRoute (общедоступный пиринг), а VPN не будет работать. 



## <a name="what-are-the-replicated-vm-requirements"></a>Каковы требования к реплицируемой виртуальной машине?

Для репликации виртуальная машина VMware должна работать под управлением поддерживаемой операционной системы. Кроме того, она должна соответствовать требованиям к виртуальным машинам Azure. [Дополнительные сведения см. в матрице поддержки.](vmware-physical-azure-support-matrix.md##replicated-machines)

## <a name="how-often-can-i-replicate-to-azure"></a>Как часто можно выполнять репликацию в Azure?
Этот процесс является непрерывным при репликации виртуальных машин VMware в Azure.

## <a name="can-i-extend-replication"></a>Можно ли расширить репликацию?
Расширенная репликация и цепочка репликации не поддерживаются. Запросите эту функцию на [форуме отзывов и предложений](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

## <a name="can-i-do-an-offline-initial-replication"></a>Можно ли выполнить начальную репликацию в автономном режиме?
Эта возможность не поддерживается. Запросите эту функцию на [форуме отзывов и предложений](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>Можно ли исключать диски?
Да, из репликации можно исключать диски. 

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>Можно ли реплицировать виртуальные машины с динамическими дисками?
Динамические диски могут быть реплицированы. Диск операционной системы должен представлять собой базовый диск.

### <a name="can-i-add-a-new-vm-to-an-existing-replication-group"></a>Можно ли добавить новую виртуальную машину в имеющуюся группу репликации?
Да.

## <a name="configuration-server"></a>Сервер конфигурации

### <a name="what-does-the-configuration-server-do"></a>Что делает сервер конфигурации?
На сервере конфигурации выполняются локальные компоненты Site Recovery, включая: 
- Сервер конфигурации, используемый для управления обменом данными между локальным источником и Azure, а также репликацией данных.
- Сервер обработки, который действует как шлюз репликации. Он получает данные репликации, оптимизирует их с помощью кэширования, сжатия и шифрования, а потом отправляет в хранилище Azure. Сервер обработки также устанавливает службу Mobility Service на виртуальных машинах, которые требуется реплицировать, и выполняет автоматическое обнаружение виртуальных машин VMware.
- Главный целевой сервер, который обрабатывает данные репликации при восстановлении размещения с переносом из Azure.

### <a name="where-do-i-set-up-the-configuration-server"></a>Где настраивается сервер конфигурации?
Для сервера конфигурации требуется одна высокодоступная локальная виртуальная машина VMware.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Каковы требования к серверу конфигурации?

Ознакомьтесь с [предварительными требованиями.](vmware-azure-deploy-configuration-server.md#prerequisites)

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>Можно ли вручную настроить сервер конфигурации, не используя шаблон?
Мы рекомендуем использовать последнюю версию шаблона OVF для [создания виртуальной машины сервера конфигурации.](vmware-azure-deploy-configuration-server.md) Если по какой-то причине вы не можете этого сделать, например, у вас нет доступа к серверу VMware, можно загрузить [файл единой установки](physical-azure-set-up-source.md) с портала и запустить его на виртуальной машине. 

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Может ли сервер конфигурации выполнять репликацию больше, чем в один регион?
Нет. Чтобы сделать это, необходимо настроить сервер конфигурации в каждом регионе.

### <a name="can-i-host-a-configuration-server-in-azure"></a>Можно ли разместить сервер конфигурации в Azure?
Это возможно, однако на виртуальной машине Azure, где запущен сервер конфигурации, необходимо настроить обмен данными с локальными инфраструктурой и виртуальными машинами VMware. Накладные расходы, вероятно, не приемлемы.


### <a name="where-can-i-get-the-latest-version-of-the-configuration-server-template"></a>Где можно получить последнюю версию шаблона сервера конфигурации?
Последнюю версию можно скачать в [Центре загрузки Майкрософт](https://aka.ms/asrconfigurationserver).

### <a name="how-do-i-update-the-configuration-server"></a>Как обновить сервер конфигурации?
Необходимо установить накопительные пакеты обновления. Последние сведения об обновлении можно найти на [вики-странице обновлений](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

## <a name="mobility-service"></a>Служба Mobility Service

### <a name="where-can-i-find-the-mobility-service-installers"></a>Где можно найти установщики Mobility Service?
Установщики хранятся в папке **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** на сервере конфигурации.

## <a name="how-do-i-install-the-mobility-service"></a>Как установить службу Mobility Service?
Установите ее на каждой виртуальной машине, которую требуется реплицировать, используя [принудительную установку](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) или установку вручную с помощью [пользовательского интерфейса](vmware-azure-install-mobility-service.md#install-mobility-service-manually-by-using-the-gui) или [PowerShell](vmware-azure-install-mobility-service.md#install-mobility-service-manually-at-a-command-prompt). Кроме того, можно выполнить развертывание с помощью средства развертывания, например [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md), или с помощью [DSC и службы автоматизации Azure](vmware-azure-mobility-deploy-automation-dsc.md).



## <a name="security"></a>Безопасность

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Какой доступ к серверам VMware требуется Site Recovery?
Site Recovery требуется доступ к серверам VMware, чтобы:

- настроить виртуальную машину VMware, выполняющуюся на сервере конфигурации, и другие локальные компоненты Site Recovery; [Подробнее](vmware-azure-deploy-configuration-server.md)
- автоматически обнаруживать виртуальные машины для репликации. Узнайте, как подготовить учетную запись для автоматического обнаружения. [Подробнее](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Какой доступ к виртуальным машинам VMware требуется Site Recovery?

- На реплицируемой виртуальной машине VMware должна быть установлена и запущена служба Site Recovery Mobility Service. Можно развернуть средство вручную или указать, что служба Site Recovery должна принудительно установить Mobility Service при включении репликации для виртуальной машины. Для принудительной установки Site Recovery требуется учетная запись, которую можно использовать для установки компонента службы. [Узнайте больше](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). Сервер обработки (по умолчанию запущен на сервере конфигурации) выполняет установку. [Дополнительные сведения об установке службы Mobility Service](vmware-azure-install-mobility-service.md).
- Во время репликации виртуальные машины обмениваются данными с Site Recovery следующим образом:
    - виртуальные машины обмениваются данными с сервером конфигурации через порт HTTPS 443, чтобы управлять репликацией;
    - виртуальные машины отправляют данные репликации на сервер обработки через порт HTTPS 9443 (можно изменить);
    - если включить согласованность между виртуальными машинами, они будут обмениваться данными друг с другом через порт 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Отправляются ли данные репликации в Site Recovery?
Нет, служба Site Recovery не перехватывает реплицируемые данные и не получает сведения о компонентах, запущенных на ваших виртуальных машинах. Данные репликации передаются между гипервизорами VMware и хранилищем Azure. Служба Site Recovery не обладает способностью перехватывать эти данные. В службу Site Recovery передаются только метаданные, необходимые для управления репликацией и отработкой отказа.  

Site Recovery имеет сертификаты ISO 27001:2013, 27018, HIPAA, DPA и сейчас проходит проверки на соответствие SOC2 и FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>Можно ли хранить локальные метаданные в пределах географических регионов?
Да. При создании хранилища в регионе мы гарантируем, что все метаданные, используемые Site Recovery, остаются в пределах географических границ региона.

### <a name="does-site-recovery-encrypt-replication"></a>Выполняет ли служба Site Recovery шифрование репликации?
Да, поддерживается и шифрование в процессе передачи, и [шифрование в Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption).


## <a name="failover-and-failback"></a>Отработка отказа и восстановление размещения
### <a name="how-far-back-can-i-recover"></a>До какой точки во времени можно выполнить восстановление?
Для репликации VMware в Azure самая давняя точка восстановления — 72 часа.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Как получить доступ к виртуальным машина Azure после отработки отказа?
После отработки отказа доступ к виртуальным машинам Azure можно получить через безопасное подключение к Интернету, VPN-подключение "сеть — сеть" или с помощью Azure ExpressRoute. Для подключения потребуется выполнить несколько подготовительных действий. [Подробнее](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Устойчивы ли данные, для которых была выполнена отработка отказа?
Среда Azure разработана для обеспечения отказоустойчивости. Служба Site Recovery рассчитана на отработку отказа в дополнительный центр обработки данных Azure в соответствии с соглашением об уровне обслуживания Azure. Когда происходит отработка отказа, Azure обеспечивает хранение метаданных и хранилищ в географическом регионе, выбранном для хранилища.

### <a name="is-failover-automatic"></a>Отработка отказа выполняется автоматически?
[Отработка отказа](site-recovery-failover.md) не выполняется автоматически. Чтобы активировать ее, необходимо щелкнуть в соответствующем месте на портале или воспользоваться [PowerShell](/powershell/module/azurerm.siterecovery).

### <a name="can-i-fail-back-to-a-different-location"></a>Можно ли выполнить восстановление размещения в другое расположение?
Да, если отработка отказа выполнена в Azure, можно восстановить размещение в другом расположении, если изначальное расположение недоступно. [Узнайте больше](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Почему для восстановления размещения требуется VPN или ExpressRoute?

При восстановлении размещения из Azure данные из Azure копируются обратно на локальную виртуальную машину и требуется закрытый доступ. 



## <a name="automation-and-scripting"></a>Автоматизация и использование скриптов

### <a name="can-i-set-up-replication-with-scripting"></a>Можно ли настроить репликацию с использованием скриптов?
Да. Рабочие процессы службы Site Recovery можно автоматизировать с помощью интерфейса REST API, PowerShell или пакета SDK для Azure. [Дополнительные сведения.](vmware-azure-disaster-recovery-powershell.md)

## <a name="performance-and-capacity"></a>Производительность и емкость
### <a name="can-i-throttle-replication-bandwidth"></a>Можно ли отрегулировать пропускную способность репликации?
Да. [Узнайте больше](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Дополнительная информация
* [Ознакомьтесь с требованиями к поддержке.](vmware-physical-azure-support-matrix.md)
* [Настройте репликацию из VMware в Azure.](vmware-azure-tutorial.md)
