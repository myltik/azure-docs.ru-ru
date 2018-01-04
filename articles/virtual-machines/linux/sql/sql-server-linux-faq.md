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
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 8b556b01aa47aeb3588138dfa61e517c00dc44dc
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/21/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Часто задаваемые вопросы об SQL Server на виртуальных машинах Linux в Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

В этой статье содержатся ответы на некоторые распространенные вопросы о запуске [SQL Server в Linux виртуальных машинах Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Это статье рассматриваются проблемы, относящиеся к SQL Server на виртуальных машинах Linux. Если вы используете SQL Server на виртуальных машинах Windows, изучите [аналогичную статью для Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>Изображения

1. **Какие образах коллекции виртуальных машин SQL Server доступны?**

   Azure использует образы виртуальных машин для всех поддерживаемых основные выпуски SQL Server во всех выпусках для Linux и Windows. Дополнительные сведения см. Полный список [образов виртуальных Машин Linux](sql-server-linux-virtual-machines-overview.md#create) и [образов виртуальных Машин Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Обновляются существующие образах коллекции виртуальных машин SQL Server**

   Каждые два месяца обновления образов SQL Server в коллекции виртуальных машин с помощью последней Linux и обновлений Windows. Для образов Linux Сюда входят последние обновления системы. Для образов Windows Сюда входят все обновления, помеченные как важные обновления Windows, включая важные обновления для системы безопасности SQL Server и пакетов обновления. Накопительные пакеты обновления SQL Server, обрабатываются по-разному для Linux и Windows. Для Linux накопительные пакеты обновления SQL Server также включается в обновление. Однако в данный момент виртуальные машины Windows не обновляются с накопительными пакетами обновления для SQL Server или Windows Server.

1. **Какие связанные пакеты SQL Server устанавливаются вместе с ним?**

   Список пакетов SQL Server, по умолчанию установленных на виртуальных машинах SQL Server на базе Linux, вы найдете [здесь](sql-server-linux-virtual-machines-overview.md#packages).

1. **Образы виртуальных машин SQL Server можно получить удален из каталога?**

   Да. Azure поддерживает только одно изображение в основной номер версии и выпуска. Например при выпуске нового пакета обновления SQL Server, Azure добавляет новый образ в коллекцию для этого пакета обновления. Образ SQL Server для предыдущего пакета обновления немедленно удаляется из портала Azure. Тем не менее он доступен для подготовки из PowerShell для следующих трех месяцев. После трех месяцев на предыдущем рисунке пакет службы больше не доступны. Эта политика удаления также можно применить в случае версии SQL Server не поддерживается при достижении конца своего жизненного цикла.

## <a name="creation"></a>Создание

1. **Как создать виртуальную машину Linux Azure с SQL Server?**

   Проще всего создать виртуальную машину Linux, которая уже включает SQL Server. Руководство по регистрации в Azure и созданию виртуальной машины SQL на портале см. в статье [Подготовка виртуальной машины SQL Server на базе Linux на портале Azure](provision-sql-server-linux-virtual-machine.md). Кроме того, вы можете вручную установить SQL Server на виртуальной машине, используя выпуск с бесплатной лицензией (Developer или Express) или предоставив существующую локальную лицензию. При использовании собственной лицензии у вас должно быть преимущество [Перемещение лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Почему не удается подготовить виртуальную машину SQL Server RHEL или SLES с подпиской Azure, для которой установлена предельная сумма расходов?**

   Для виртуальных машин RHEL и SLES нужна подписка без предельной суммы расходов и подтвержденный метод оплаты (желательно кредитная карта), связанный с подпиской. Если вы развернете виртуальную машину RHEL или SLES, не отменяя предельной суммы расходов, ваша подписка будет отключена, а все виртуальные машины и службы будут остановлены. В такой ситуации [удалите предельную сумму расходов](https://account.windowsazure.com/subscriptions), чтобы восстановить работоспособность подписки. Оставшиеся деньги на счете будут использованы в текущем цикле выставления счетов, но все дополнительные расходы по образам виртуальных машин RHEL или SLES (если вы продолжите их использовать после восстановления подписки) будут возмещаться только с кредитной карты.

## <a name="licensing"></a>Лицензирование

1. **Как установить лицензированную копию SQL Server в виртуальной машине Azure?**

   Сначала создайте виртуальную машину, на которой установлена только операционная система Linux. Затем выполните [действия по установке SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms) для своего дистрибутива Linux. Если устанавливаемый выпуск SQL Server не относится к свободно лицензируемым, вам потребуется лицензия на SQL Server и возможность [перемещения лицензий в рамках программы Software Assurance в Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Существуют ли образы виртуальных машин SQL Server на базе Linux, позволяющие использовать собственную лицензию (BYOL)?**

   В настоящее время мы не предоставляем образы виртуальных машин SQL Server на базе Linux с поддержкой BYOL. Но вы можете вручную установить SQL Server на виртуальной машине только с ОС Linux, как мы уже упомянули в ответе на предыдущий вопрос.

1. **Можно ли настроить виртуальную машину для использования собственной лицензии SQL Server, если она была создана из одного из образов коллекции с оплатой по мере использования?**

   Нет. Возможность перейти с лицензирования с поминутной оплатой на свою собственную лицензию не предусмотрена. При такой необходимости вам потребуется создать новую виртуальную машину Linux, установить на ней SQL Server и перенести ваши данные. Дополнительные сведения об использовании собственной лицензии есть в ответе на предыдущий вопрос.

## <a name="administration"></a>Администрирование

1. **Можно управлять виртуальной машины с Linux SQL Server с SQL Server Management Studio (SSMS)**

   Да, но SSMS в настоящее время это средство только для Windows. Необходимо подключиться удаленно с компьютера Windows для использования SSMS с виртуальных машин Linux SQL Server. Локально в Linux новый [mssql conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) средство может выполнять многие задачи администрирования. Для предварительного просмотра средство управления базой данных между различными платформами, в разделе [Studio операций SQL Server (Предварительная версия)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

## <a name="updating-and-patching"></a>Обновления и исправления

1. **Как выполнить обновление до новой версии или выпуска SQL Server в виртуальной машине Azure?**

   В настоящее время выполнить обновление на месте для SQL Server в виртуальной машине Azure невозможно. Создайте виртуальную машину Azure с нужной версией или выпуском SQL Server, а затем перенесите базы данных на новый сервер с помощью стандартных [методов миграции данных](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Общие сведения

1. **Поддерживаются ли на виртуальных машинах Azure решения SQL Server для высокого уровня доступности?**

   На данный момент нет. Группы доступности AlwaysOn и отказоустойчивая кластеризация работают только в кластерных решениях на базе Linux, таких как Pacemaker. Доступные дистрибутивы Linux для SQL Server не поддерживают надстройки высокого уровня доступности в облаке.

## <a name="resources"></a>Ресурсы

**Для виртуальных машин Linux**:

* [Обзор SQL Server на виртуальных машинах Linux](sql-server-linux-virtual-machines-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Linux](provision-sql-server-linux-virtual-machine.md).
* [Документация по SQL Server в Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).

**Для виртуальных машин Windows**:

* [Обзор SQL Server на виртуальных машинах Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
* [Подготовка виртуальной машины SQL Server на базе Windows](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
* [Часто задаваемые вопросы (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).