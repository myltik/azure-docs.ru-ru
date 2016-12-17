---
title: "Перенос виртуальных машин из Amazon Web Services (AWS) в Azure с помощью Azure Site Recovery | Документация Майкрософт"
description: "В этой статье описано, как перенести виртуальные машины, запущенные в Amazon Web Services (AWS), в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/01/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: c7238d677b315390bc5f53e54ab7dbded2871c5a


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Перенос виртуальных машин из Amazon Web Services (AWS) в Azure с помощью Azure Site Recovery
## <a name="overview"></a>Обзор
Вас приветствует служба Azure Site Recovery! Воспользуйтесь этой статьей, чтобы выполнить с помощью Site Recovery перенос экземпляров EC2, работающих в AWS, в Azure. Перед началом работы обратите внимание на следующее:

* В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: модель Azure Resource Manager и классическая модель. Кроме того, Azure предоставляет два портала — классический портал Azure, поддерживающий классическую модель развертывания, и портал Azure, поддерживающий обе модели развертывания. Основные шаги, необходимые для выполнения переноса, одинаковы при настройке Site Recovery как с помощью модели Resource Manager, так и с помощью классической модели. Приводимые в этой статье элементы пользовательского интерфейса и снимки экрана относятся к порталу Azure.
* **В настоящее время поддерживается только перенос из AWS в Azure. То есть вы можете выполнить отработку отказа виртуальных машин из AWS в Azure, но не сможете выполнить обратную отработку отказа. Текущая репликация не поддерживается.**
* Инструкции по переносу, приводимые в этой статье, основаны на инструкциях по репликации физического компьютера в Azure. Здесь содержатся ссылки на шаги из статьи [Репликация виртуальных машин VMware и физических компьютеров в Azure с помощью службы Azure Site Recovery и портала Azure](site-recovery-vmware-to-azure.md), в которой описывается процесс репликации физического сервера на портале Azure.
* Если вы настраиваете Site Recovery на классическом портале, то следуйте подробным инструкциям, приведенным в [этой статье](site-recovery-vmware-to-azure-classic.md). **больше не следует использовать** инструкции в этой [устаревшей статье](site-recovery-vmware-to-azure-classic-legacy.md).

Все комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы
Site Recovery можно использовать для переноса экземпляров EC2, работающих на любой из следующих операционных систем.

### <a name="windows64-bit-only"></a>Windows (только 64-разрядная версия)
* Windows Server 2008 R2 с пакетом обновления 1 или более поздней версии (только драйверы Citrix PV или AWS PV). **Экземпляры с драйверами RedHat PV не поддерживаются.**
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (только 64-разрядная версия)
* Red Hat Enterprise Linux 6.7 (только виртуализированные экземпляры HVM).

## <a name="prerequisites"></a>Предварительные требования
Вот что необходимо для этого развертывания:

* **Сервер конфигурации.** Локальная виртуальная машина под управлением Windows Server 2012 R2, которая выступает в качестве сервера конфигурации. Также установите на этой виртуальной машине другие компоненты Site Recovery (включая сервер обработки и главный целевой сервер). Дополнительные сведения см. в разделах [Архитектура сценария](site-recovery-vmware-to-azure.md#scenario-architecture) и [Предварительные требования к серверу конфигурации и к серверу обработки масштабирования](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).
* **Экземпляры виртуальной машины EC2** — экземпляры, которые требуется перенести.

## <a name="deployment-steps"></a>Шаги по развертыванию
В этом разделе описаны шаги по развертыванию на новом портале Azure. Инструкции по развертыванию Site Recovery на классическом портале см. в [этой статье](site-recovery-vmware-to-azure-classic.md).

1. [Создайте хранилище](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Выполните развертывание сервера конфигурации](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. После развертывания сервера конфигурации убедитесь, что он может обмениваться данными с виртуальными машинами, которые требуется перенести.
4. [Настройте параметры репликации](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Создайте политику репликации и назначьте ее серверу конфигурации.
5. [Установите службу Mobility Service.](site-recovery-vmware-to-azure.md#step-6-replicate-applications) На каждую виртуальную машину, которую вы планируете защитить, требуется установить службу Mobility Service. Эта служба отправляет данные на сервер обработки. Службу Mobility Service можно установить вручную или передать и установить автоматически с помощью сервера обработки после включения защиты для виртуальной машины. Правила брандмауэра на экземплярах EC2, которые требуется перенести, необходимо настроить таким образом, чтобы разрешить принудительную установку этой службы. Группа безопасности для экземпляров EC2 должна содержать следующие правила:

    ![Правила брандмауэра](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. [Включите репликацию](site-recovery-vmware-to-azure.md#enable-replication). Включите репликацию для виртуальных машин, которые требуется перенести. Вы можете просмотреть экземпляры EC2, используя их частные IP-адреса, сведения о которых доступны в консоли EC2.
7. [ Запустите внеплановую отработку отказа](site-recovery-failover.md#run-an-unplanned-failover). После завершения первоначальной репликации вы можете запустить внеплановую отработку отказа из AWS в Azure для каждой виртуальной машины. При необходимости вы можете создать план восстановления и запустить внеплановую отработку отказа, чтобы перенести несколько виртуальных машин из AWS в Azure. [Узнайте подробнее](site-recovery-create-recovery-plans.md) о планах восстановления.

## <a name="next-steps"></a>Дальнейшие действия
Сведения о других сценариях репликации см. в статье [Что такое Site Recovery?](site-recovery-overview.md)



<!--HONumber=Nov16_HO3-->


