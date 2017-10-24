---
title: "Часто задаваемые вопросы об SQL Server на виртуальных машинах Linux в Azure | Документация Майкрософт"
description: "В этой статье содержатся ответы на часто задаваемые вопросы о выполнении SQL Server на виртуальных машинах Linux в Azure."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 10/05/2017
ms.author: jroth
ms.openlocfilehash: a001ae116e33e0b7be4431b0bc4c8bb319f4e801
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Часто задаваемые вопросы об SQL Server на виртуальных машинах Linux в Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

В этой статье содержатся ответы на некоторые наиболее распространенные вопросы о выполнении [SQL Server на виртуальных машинах Linux в Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Эта статья посвящена проблемам, относящимся к SQL Server на виртуальных машинах Linux. Если вы используете SQL Server на виртуальных машинах Windows, изучите [аналогичную статью для Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

1. **Как создать виртуальную машину Linux Azure с SQL Server?**

   Проще всего создать виртуальную машину Linux, которая уже включает SQL Server. Руководство по регистрации в Azure и созданию виртуальной машины SQL на портале см. в статье [Подготовка виртуальной машины SQL Server на базе Linux на портале Azure](provision-sql-server-linux-virtual-machine.md). Кроме того, вы можете вручную установить SQL Server на виртуальной машине, используя выпуск с бесплатной лицензией (Developer или Express) или предоставив существующую локальную лицензию. При использовании собственной лицензии у вас должно быть преимущество [Перемещение лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Как выполнить обновление до новой версии или выпуска SQL Server в виртуальной машине Azure?**

   В настоящее время выполнить обновление на месте для SQL Server в виртуальной машине Azure невозможно. Создайте виртуальную машину Azure с нужной версией или выпуском SQL Server, а затем перенесите базы данных на новый сервер с помощью стандартных [методов миграции данных](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

1. **Как установить лицензированную копию SQL Server в виртуальной машине Azure?**

   Сначала создайте виртуальную машину, на которой установлена только операционная система Linux. Затем выполните [действия по установке SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) для своего дистрибутива Linux. Если устанавливаемый выпуск SQL Server не относится к свободно лицензируемым, вам потребуется лицензия на SQL Server и возможность [перемещения лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Существуют ли образы виртуальных машин SQL Server на базе Linux, позволяющие использовать собственную лицензию (BYOL)?**

   В настоящее время мы не предоставляем образы виртуальных машин SQL Server на базе Linux с поддержкой BYOL. Но вы можете вручную установить SQL Server на виртуальной машине только с ОС Linux, как мы уже упомянули в ответе на предыдущий вопрос.

1. **Можно ли настроить виртуальную машину для использования собственной лицензии SQL Server, если она была создана из одного из образов коллекции с оплатой по мере использования?**

   Нет. Возможность перейти с лицензирования с поминутной оплатой на свою собственную лицензию не предусмотрена. При такой необходимости вам потребуется создать новую виртуальную машину Linux, установить на ней SQL Server и перенести ваши данные. Дополнительные сведения об использовании собственной лицензии есть в ответе на предыдущий вопрос.

1. **Какие связанные пакеты SQL Server устанавливаются вместе с ним?**

   Список пакетов SQL Server, по умолчанию установленных на виртуальных машинах SQL Server на базе Linux, вы найдете [здесь](sql-server-linux-virtual-machines-overview.md#packages).

1. **Поддерживаются ли на виртуальных машинах Azure решения SQL Server для высокого уровня доступности?**

   На данный момент нет. Группы доступности AlwaysOn и отказоустойчивая кластеризация работают только в кластерных решениях на базе Linux, таких как Pacemaker. Доступные дистрибутивы Linux для SQL Server не поддерживают надстройки высокого уровня доступности в облаке.

1. **Почему не удается подготовить виртуальную машину SQL Server RHEL или SLES с подпиской Azure, для которой установлена предельная сумма расходов?**

   Для виртуальных машин RHEL и SLES нужна подписка без предельной суммы расходов и подтвержденный метод оплаты (желательно кредитная карта), связанный с подпиской. Если вы развернете виртуальную машину RHEL или SLES, не отменяя предельной суммы расходов, ваша подписка будет отключена, а все виртуальные машины и службы будут остановлены. В такой ситуации [удалите предельную сумму расходов](https://account.windowsazure.com/subscriptions), чтобы восстановить работоспособность подписки. Оставшиеся деньги на счете будут использованы в текущем цикле выставления счетов, но все дополнительные расходы по образам виртуальных машин RHEL или SLES (если вы продолжите их использовать после восстановления подписки) будут возмещаться только с кредитной карты.

## <a name="resources"></a>Ресурсы

**Для виртуальных машин Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](sql-server-linux-virtual-machines-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Linux](provision-sql-server-linux-virtual-machine.md).
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).

**Для виртуальных машин Windows**:

* [Обзор SQL Server на виртуальных машинах Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
* [Часто задаваемые вопросы (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).