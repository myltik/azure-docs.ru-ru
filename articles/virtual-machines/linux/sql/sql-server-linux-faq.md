---
title: Часто задаваемые вопросы об SQL Server на виртуальных машинах Linux в Azure | Документация Майкрософт
description: В этой статье содержатся ответы на часто задаваемые вопросы о выполнении SQL Server на виртуальных машинах Linux в Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: jroth
ms.openlocfilehash: 56a0629249cdb5f0f098d2b7b6d36b3fbb215009
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190877"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Часто задаваемые вопросы об SQL Server на виртуальных машинах Linux в Azure

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

В этой статье содержатся ответы на некоторые наиболее распространенные вопросы о выполнении [SQL Server на виртуальных машинах Linux в Azure](sql-server-linux-virtual-machines-overview.md).

> [!NOTE]
> Эта статья посвящена проблемам с SQL Server на виртуальных машинах Linux. Если вы используете SQL Server на виртуальных машинах Windows, изучите [аналогичную статью для Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a> Образы

1. **Какие образы доступны в коллекции виртуальных машин SQL Server?**

   В Azure доступны образы виртуальных машин для всех поддерживаемых основных выпусков SQL Server во всех выпусках Windows и Linux. Дополнительные сведения см. в полном списке образов виртуальных машин [Linux](sql-server-linux-virtual-machines-overview.md#create) и [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).

1. **Обновляются ли доступные образы в коллекции виртуальных машин SQL Server?**

   Каждые два месяца образы SQL Server в коллекции виртуальных машин обновляются до последних версий Windows и Linux. Образы Linux содержат последние обновления системы. Образы Windows содержат все обновления, отмеченные как важные в Центре обновления Windows, в частности важные обновления системы безопасности и пакеты обновления SQL Server. Накопительные пакеты обновления SQL Server в Linux и Windows обрабатываются по-разному. Для Linux накопительные пакеты обновления SQL Server также включены в обновление. Но виртуальные машины Windows сейчас не поддерживают накопительные пакеты обновления для SQL Server или Windows Server.

1. **Какие связанные пакеты SQL Server устанавливаются вместе с ним?**

   Список пакетов SQL Server, по умолчанию установленных на виртуальных машинах SQL Server на базе Linux, вы найдете [здесь](sql-server-linux-virtual-machines-overview.md#packages).

1. **Можно ли удалить образы виртуальных машин SQL Server из коллекции?**

   Да. Azure поддерживает только один образ для основного номера версии и выпуска. Например, при выпуске нового пакета обновления SQL Server в коллекцию Azure для него добавляется новый образ. Образ SQL Server для предыдущего пакета обновления немедленно удаляется с портала Azure. Но он доступен для подготовки из PowerShell в течение следующих трех месяцев. После трех месяцев предыдущий образ пакета обновления становится недоступным. Эта политика удаления также применяется, если версия SQL Server перестает поддерживаться по окончании жизненного цикла.

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

   Нет. Возможность перейти с лицензирования с посекундной оплатой на собственную лицензию не предусмотрена. При такой необходимости вам потребуется создать новую виртуальную машину Linux, установить на ней SQL Server и перенести ваши данные. Дополнительные сведения об использовании собственной лицензии есть в ответе на предыдущий вопрос.

## <a name="administration"></a>Администрирование

1. **Можно ли управлять виртуальной машиной SQL Server на базе Linux с помощью SQL Server Management Studio (SSMS)?**

   Да. Но сейчас средства SSMS доступны только в ОС Windows. Чтобы использовать SSMS с виртуальными машинами SQL Server на базе Linux, необходимо установить удаленное подключение с компьютера с Windows. На компьютере с Linux большинство задач администрирования можно выполнять с помощью нового средства [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf). Сведения о кроссплатформенном средстве управления базой данных см. в статье о [SQL Server Operations Studio (предварительная версия)](https://docs.microsoft.com/sql/sql-operations-studio/what-is).

1. **Можно ли полностью удалить SQL Server с виртуальной машины SQL?**

   Да, но с вас будет по-прежнему взиматься плата за виртуальную машину SQL, как описано в статье [Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Если вам больше не нужен SQL Server, можно развернуть новую виртуальную машину и перенести туда данные и приложения. Затем виртуальную машину SQL Server можно удалить.

## <a name="updating-and-patching"></a>Установка обновлений и исправлений

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