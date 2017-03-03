---
title: "Миграция виртуальных машин из AWS в Azure | Документация Майкрософт"
description: "В этой статье описано, как перенести виртуальные машины, запущенные в Amazon Web Services (AWS), в Azure с помощью Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 3396818cd177330b7123f3a346b1591a4bcb1e4e
ms.openlocfilehash: 14cc104bb755a0893c00963636e210b656b270b5
ms.lasthandoff: 02/22/2017


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Перенос виртуальных машин из Amazon Web Services (AWS) в Azure с помощью Azure Site Recovery

В этой статье описано, как перенести экземпляры Windows из Amazon Web Services (AWS) в Виртуальные машины Azure с помощью службы [Azure Site Recovery](site-recovery-overview.md).

Миграция фактически является отработкой отказа из AWS в Azure. Однако восстановить размещение машин в AWS невозможно, и репликация данных не выполняется. Эта статья содержит инструкции по миграции с помощью портала Azure, которые основаны на [инструкциях по репликации физического компьютера в Azure](site-recovery-vmware-to-azure.md).

Все комментарии или вопросы можно добавить в конце этой статьи или на [форуме по службам восстановления Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Site Recovery можно использовать для переноса экземпляров EC2, работающих на любой из следующих операционных систем.

- Windows (только&64;-разрядная версия)
    - Windows Server 2008 R2 с пакетом обновления 1 или более поздняя версия (Поддерживаются только драйверы Citrix PV и драйверы AWS PV. **Экземпляры с драйверами RedHat PV не поддерживаются**.) Windows Server 2012, Windows Server 2012 R2.
- Linux (только&64;-разрядная версия)
    - Red Hat Enterprise Linux 6.7 (только виртуализированные экземпляры HVM).

## <a name="prerequisites"></a>Предварительные требования

Вот что необходимо для этого развертывания:

* **Сервер конфигурации.** Виртуальная машина Amazon EC2 под управлением Windows Server 2012 EC2, развернутая в качестве сервера конфигурации. По умолчанию при развертывании сервера конфигурации устанавливаются другие компоненты Azure Site Recovery (сервер обработки и главный целевой сервер). Эта статья содержит инструкции по миграции с помощью портала Azure, которые основаны на сведениях, приведенных в [этом разделе](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure).

* **Экземпляры EC2.** Экземпляры виртуальных машин Amazon EC2, которые требуется перенести.

## <a name="deployment-steps"></a>Шаги по развертыванию

1. Создание хранилища служб восстановления

2. В группе безопасности для экземпляров EC2 должны быть настроены следующие правила: ![Правила](./media/site-recovery-migrate-aws-to-azure/migration_pic1.png)

3. Разверните сервер конфигурации ASR на том же виртуальном частном облаке Amazon, что и экземпляры EC2. Требования для развертывания сервера конфигурации см. в предварительных требованиях для развертывания виртуальной машины VMware или физического компьютера в Azure. ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.    После того как сервер конфигурации будет развернут в AWS и зарегистрирован в хранилище служб восстановления, сервер конфигурации и сервер обработки отобразятся в инфраструктуре Site Recovery, как показано ниже. ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)
  >[!NOTE]
  >Для отображения сервера конфигурации и сервера обработки может потребоваться около 15 минут.
  >

5. После развертывания сервера конфигурации убедитесь, что он может обмениваться данными с виртуальными машинами, которые требуется перенести.

6. [Настройте параметры репликации](site-recovery-setup-replication-settings-vmware.md).

7. Включите репликацию для виртуальных машин, которые требуется перенести. Вы можете просмотреть экземпляры EC2, используя их частные IP-адреса, сведения о которых доступны в консоли EC2.
![Выбор виртуальной машины](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)
8. Как только экземпляры EC2 будут защищены, а репликация в Azure завершена, [запустите тестовою отработку отказа](site-recovery-test-failover-to-azure.md) для проверки производительности приложения в Azure. ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Запустите отработку отказа из AWS в Azure для каждой виртуальной машины. При необходимости вы можете создать план восстановления и запустить отработку отказа, чтобы перенести несколько виртуальных машин из AWS в Azure. [Узнайте подробнее](site-recovery-create-recovery-plans.md) о планах восстановления.

10. Чтобы перенести машины, не нужно выполнять отработку отказа. Для каждой машины, которую нужно перенести, выберите параметр "Завершение миграции". При завершении миграции выполняется перенос виртуальной машины, удаляется репликация и прекращается выставление счетов за использование Site Recovery для этой машины.![Миграция](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

