---
title: Эффективное управление затратами на SQL Server в Виртуальных машинах Azure | Документация Майкрософт
description: Рекомендации по выбору подходящей модели ценообразования для виртуальны машин SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2018
ms.author: jroth
ms.openlocfilehash: 71c86af9d4dcdf1026b4f539574b9932ef1cfc89
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32767806"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Руководство по выбору ценовой категории для виртуальных машин SQL Server в Azure

В этой статье приводятся рекомендации по выбору ценовой категории для [виртуальных машин SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) в Azure. На затраты влияет несколько параметров, поэтому важно выбрать соответствующий образ, чтобы затраты были соизмеримы с потребностями организации.

> [!TIP]
> Если необходимо узнать примерную стоимость конкретного сочетания выпуска SQL Server и размера виртуальной машины, см. страницу со сведениями о ценах для [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) или [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Выберите платформу и выпуск SQL Server из списка **ОС или программное обеспечение**.
>
> ![Пользовательский интерфейс на странице цен на виртуальные машины](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> Или же используйте [калькулятор цен](https://azure.microsoft.com/pricing/#explore-cost), чтобы добавить и настроить виртуальною машину. 

## <a name="free-licensed-sql-server-editions"></a>Выпуски SQL Server с бесплатными лицензиями

Если требуется разработать, протестировать или создать подтверждение концепции, используйте выпуск **SQL Server Developer** с бесплатными лицензиями. Этот выпуск содержит все компоненты выпуска SQL Server Enterprise, поэтому его можно использовать для создания и тестирования любого приложения. Однако его нельзя запускать в рабочей среде. При использовании виртуальной машины SQL Server Developer плата взимается только за виртуальную машину. Затраты на лицензии SQL Server отсутствуют.

Если вы хотите выполнять упрощенную рабочую нагрузку в рабочей среде (меньше 4 ядер, 1 ГБ памяти и 10 ГБ на базу данных), используйте **выпуск SQL Server Express** с бесплатными лицензиями. При использовании виртуальной машины SQL Server Express плата также взимается только за виртуальную машину.

Кроме того, вы можете сэкономить средства на этих рабочих нагрузках разработки и тестирования или упрощенных рабочих нагрузках, выбрав меньший размер виртуальной машины, который для них подходит. Хорошим выбором в некоторых случаях может быть DS1v2.

Чтобы создать виртуальную машину Azure SQL Server 2017 на основе одного из этих образов, перейдите по следующим ссылкам:

| платформа | Образы с бесплатными лицензиями |
|---|---|
| Windows Server 2016 | [Виртуальная машина SQL Server 2017 Developer в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Виртуальная машина SQL Server 2017 Express в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux. | [Виртуальная машина SQL Server 2017 Developer в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Виртуальная машина SQL Server 2017 Express в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Виртуальная машина SQL Server 2017 Developer в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Виртуальная машина SQL Server 2017 Express в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Виртуальная машина SQL Server 2017 Developer в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Виртуальная машина SQL Server 2017 Express в Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Платные выпуски SQL Server

Для обычной производственной рабочей нагрузки используйте один из следующих выпусков SQL Server.

| Выпуск SQL Server | Рабочая нагрузка |
|-----|-----|
| Web | Небольшие веб-сайты |
| Стандартная | Небольшие и средние рабочие нагрузки |
| Enterprise | Большие и критически важные рабочие нагрузки|

Существуют два варианта оплаты лицензий для этих выпусков SQL Server: *оплата за использование* или *использование собственных лицензий (BYOL)*.

## <a name="pay-per-usage"></a>Оплата за использование

**Оплата за использование лицензий SQL Server** означает, что стоимость секунды выполнения виртуальной машины Azure включает в себя стоимость лицензии SQL Server. Цены на разные выпуски SQL Server (Web, Standard, Enterprise) можно узнать на странице со сведениями о ценах на виртуальные машины Azure для [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) или [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Стоимость одинакова для всех версий SQL Server (от версии 2012 SP3 до 2017). Стоимость секунды лицензии зависит от числа ядер виртуальной машины, как и в случае с любыми лицензиями SQL Server.

Оплата за использование лицензий SQL Server рекомендуется в следующих случаях.

- **Временные или периодические рабочие нагрузки**. Например, это может быть приложение, которое должно поддерживать какое-либо событие в течение пары месяцев каждый год или выполнять бизнес-анализ по понедельникам.

- **Рабочие нагрузки с неизвестным временем существования или масштабом**. Например, это может быть приложение, не востребованное в течение нескольких месяцев, или приложение, которое может требовать больше или меньше вычислительной мощности в зависимости от потребностей.

Чтобы создать виртуальную машину SQL Server 2017 в Azure на основе одного из этих образов с оплатой за использование, перейдите по следующим ссылкам:

| платформа | Лицензированные образы |
|---|---|
| Windows Server 2016 | [Виртуальная машина SQL Server 2017 Web в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Виртуальная машина SQL Server 2017 Standard в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Виртуальная машина SQL Server 2017 Enterprise в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux. | [Виртуальная машина SQL Server 2017 Web в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Виртуальная машина SQL Server 2017 Standard в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Виртуальная машина SQL Server 2017 Enterprise в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Виртуальная машина SQL Server 2017 Web в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Виртуальная машина SQL Server 2017 Standard в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Виртуальная машина SQL Server 2017 Enterprise в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Виртуальная машина SQL Server 2017 Web в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Виртуальная машина SQL Server 2017 Standard в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Виртуальная машина SQL Server 2017 Enterprise в Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> При создании виртуальной машины с SQL Server на портале в окне **Выбор размера** отображается расчетная стоимость. Следует отметить, что эта стоимость включает в себя только плату за вычислительные ресурсы, необходимые для выполнения виртуальной машины, а также применяемые расходы на лицензирование ОС (Windows или сторонних операционных систем Linux).
>
> ![Колонка выбора размера виртуальной машины](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Она не включает в себя дополнительные расходы на лицензирование SQL Server для выпусков Web, Standard и Enterprise. Чтобы получить самую точную расчетную стоимость, выберите свою операционную систему и выпуск SQL Server на странице расценок для [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) или [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="bring-your-own-license-byol"></a>Использование собственных лицензий (BYOL)

**Использование собственной лицензии SQL Server посредством License Mobility** (**BYOL**) означает использование на виртуальной машине Azure существующей корпоративной лицензии SQL Server в рамках программы Software Assurance. При использовании виртуальной машины SQL Server с BYOL взимается только плата за выполнение виртуальной машины, но не за лицензию SQL Server, так как вы уже приобрели лицензии и участвуете в программе Software Assurance в рамках программы корпоративного лицензирования.

> [!NOTE]
> Сейчас образы BYOL доступны только для виртуальных машин Windows. Но вы можете вручную установить SQL Server на виртуальной машине только с ОС Linux. Дополнительные сведения см. в статье [Часто задаваемые вопросы об SQL Server на виртуальных машинах Linux в Azure](../../linux/sql/sql-server-linux-faq.md).

Использование собственных лицензий SQL посредством License Mobility рекомендуется для следующих ситуаций.

- **Непрерывные рабочие нагрузки**. Например, это может быть приложение, которое требуется для круглосуточной поддержки бизнес-операций.

- **Рабочие нагрузки с известным временем существования и масштабом**. Например, это может быть приложение, которое востребовано на протяжении всего года и потребности которого спрогнозированы.

Чтобы использовать BYOL с виртуальной машиной SQL Server, необходимо иметь лицензию на SQL Server Standard или SQL Server Enterprise и участвовать в программе [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1). Это обязательный компонент в некоторых программах корпоративного лицензирования и необязательная покупка в других программах. Уровень ценообразования, предоставляемый в рамках программ корпоративного лицензирования, может отличаться в зависимости от типа соглашения и количества лицензий и/или обязательств по SQL Server. Как правило, использование собственных лицензий для непрерывных рабочих нагрузок дает следующие преимущества.

| Преимущество BYOL | ОПИСАНИЕ |
|-----|-----|
| **Уменьшение затрат** | Использовать собственную лицензию SQL Server выгоднее, чем платить за ее использование, если рабочая нагрузка на SQL Server Standard или SQL Server Enterprise выполняется непрерывно *более 10 месяцев*. |
| **Долгосрочная экономия** | В течение первых 3 лет приобрести или обновить лицензию SQL Server в среднем *дешевле на 30 % в год*. Более того, по прошествии 3 лет лицензию больше не нужно обновлять, нужно лишь платить за участие в программе Software Assurance. Это *удешевляет использование на 200 %*. |
| **Бесплатная пассивная вторичная реплика** | Еще одно преимущество использования собственной лицензии — [бесплатная лицензия на одну пассивную вторичную реплику](https://azure.microsoft.com/pricing/licensing-faq/) на каждый сервер SQL Server, предназначенную обеспечить высокий уровень доступности. Это наполовину снижает стоимость лицензии на высокодоступное развертывание SQL Server (например, с использованием групп доступности AlwaysOn). Права на запуск пассивной вторичной реплики предоставляются посредством серверов отработки отказа (преимущество Software Assurance). |

Чтобы создать виртуальную машину SQL Server 2017 в Azure с одним из этих образов с использованием собственной лицензии, ищите виртуальные машины с префиксом "{BYOL}":

- [Виртуальная машина SQL Server 2017 Enterprise в Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Виртуальная машина SQL Server 2017 Standard в Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Сообщите нам в течение 10 дней количество лицензий на SQL Server, которые вы используете в Azure. По ссылкам на предыдущие образы доступны инструкции о том, как это сделать.

> [!NOTE]
> Невозможно изменить модель лицензирования с посекундной оплатой для виртуальной машины SQL Server для использования собственной лицензии. В этом случае необходимо создать виртуальную машину BYOL и перенести в нее базы данных.

## <a name="reduce-costs"></a>Сокращение затрат

Чтобы избежать ненужных затрат, выберите оптимальный размер виртуальной машины и периодически выключайте непостоянные рабочие нагрузки.

### <a id="machinesize"></a>Выбор оптимального размера виртуальной машины

Стоимость лицензирования SQL Server напрямую связана с количеством ядер. Выберите размер виртуальной машины, который соответствует требованиям к ожидаемому использованию ЦП, памяти, хранилища и пропускной способности ввода-вывода. Полный список размеров виртуальных машин см. в статьях [Размеры виртуальных машин Windows в Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) и [Размеры виртуальных машин Linux в Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Введены новые размеры, которые подходят для определенных типов рабочих нагрузок SQL Server. Они поддерживают высокий уровень памяти, хранилища и пропускной способности ввода-вывода, но имеют меньшее количество виртуальных ядер. Рассмотрим следующий пример.

| Размер виртуальной машины | Число виртуальных ЦП | Память | Максимальное количество дисков | Максимальная пропускная способность ввода-вывода | Стоимость лицензии SQL | Общая себестоимость (вычисление + лицензирование) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 ГБ | 32 | 51 200 операций ввода-вывода в секунду (IOPS) или 768 МБ/с | | |
| **Standard_DS14-4v2** | 4. | 112 ГБ | 32 | 51 200 операций ввода-вывода в секунду (IOPS) или 768 МБ/с | на 75 % ниже | на 57 % ниже |

> [!IMPORTANT]
> Это пример "на момент времени". Самые последние спецификации можно найти в статьях о размерах виртуальных машин и на странице цен Azure для [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) и [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

В предыдущем примере показано, что спецификации для **Standard_DS14v2** и **Standard_DS14 4v2** идентичны, за исключением количества виртуальных ЦП. Суффикс **-4v2** в конце размера машины **Standard_DS14 4v2** указывает количество активных виртуальных ЦП. Так как затраты на лицензирование SQL Server связаны с количеством ядер, это значительно снижает стоимость виртуальной машины в сценариях, где не требуются дополнительные виртуальные ЦП. Это всего лишь один из примеров. Есть и много других размеров виртуальных машин с ограниченным числом виртуальных ЦП, которые идентифицируются по такому суффиксу. Дополнительные сведения см. в записи блога [Announcing new Azure VM sizes for more cost-effective database workloads](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/) (Объявление новых размеров виртуальных машин Azure для более экономичных рабочих нагрузок базы данных).

### <a name="shut-down-your-vm-when-possible"></a>Завершение работы виртуальной машины (если возможно)

При наличии любых непостоянных рабочих нагрузок рассмотрите возможность завершения работы виртуальной машины в периоды простоя. Вы платите только за то, что используете.

Например, если вы просто изучаете работу SQL Server на виртуальной машине Azure, то не захотите получить приличный счет, случайно оставив ее работать в течение нескольких недель. Одним из решений является использование [функции автоматического завершения работы](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Автоматическое завершение работы виртуальной машины SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Автоматическое завершение работы — лишь одна функция из большого набора аналогичных функциональных возможностей, предоставляемых [DevTest Azure Labs](https://azure.microsoft.com/services/devtest-lab).

Для других рабочих процессов рассмотрите возможность автоматического завершения работы и перезапуска виртуальных машин Azure с помощью решения на основе сценариев, например [службы автоматизации Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Завершение работы и освобождение виртуальной машины — единственный способ избежать ненужных расходов. Если просто остановить или завершить работу виртуальной машины с помощью параметров включения, то за ее использование все равно будет взиматься плата.

## <a name="next-steps"></a>Дополнительная информация

Общие рекомендации по ценам Azure приведены в статье [Предотвращение непредвиденных расходов с помощью функции выставления счетов и управления затратами в Azure](../../../billing/billing-getting-started.md). Последние цены на виртуальные машины, включая лицензии SQL Server, доступны на странице со сведениями о ценах на виртуальные машины Azure для [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) и [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Общие сведения о запуске SQL Server в виртуальных машинах Azure см. в следующих статьях:

- [Обзор SQL Server на виртуальных машинах Windows](virtual-machines-windows-sql-server-iaas-overview.md)
- [Обзор SQL Server на виртуальных машинах Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)