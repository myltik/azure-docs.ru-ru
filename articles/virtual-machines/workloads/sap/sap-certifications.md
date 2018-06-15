---
title: Сертификация Microsoft Azure для SAP | Документация Майкрософт
description: Обновленный список текущих конфигураций и сертификатов SAP на платформе Azure.
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: rclaus
ms.custom: ''
ms.openlocfilehash: f2d342558e83c54e101e0ff9128611da9bec1e77
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656964"
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>Сертификаты и конфигурации SAP на платформе Microsoft Azure

Компания SAP и корпорация Майкрософт имеют долгую историю плодотворного сотрудничества, которое обеспечивает взаимные преимущества для их клиентов. Корпорация Майкрософт регулярно обновляет свою платформу и отправляет компании SAP сведения о новых сертификатах. Тем самым гарантируется, что Microsoft Azure остается лучшей платформой для выполнения рабочих нагрузок SAP. В следующих таблицах представлены поддерживаемые конфигурации Azure и список сертификатов SAP, который регулярно пополняется. 

## <a name="sap-hana-certifications"></a>Сертификаты SAP HANA
Справочные материалы:

- Сведения о поддержке SAP HANA для собственных виртуальных машин Azure и SAP HANA (крупные экземпляры) приведены в [списке сертифицированных платформ IaaS для SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

| Продукт SAP | Поддерживаемая ОС | Предложения Azure |
| --- | --- | --- |
| SAP HANA, версия для разработчиков (включая клиентское ПО HANA с драйверами SQLODBC, ODBO (только для ОС Windows), ODBC и JDBC, среду разработки HANA Studio и базу данных HANA) | Red Hat Enterprise Linux, SUSE Linux Enterprise | Семейство виртуальных машин серии D |
| Business One в HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | Управляемая доступность для GS5, M64s, M64ms, M128s, M128ms, SAP HANA на крупных экземплярах Azure |
| Suite on HANA, OLTP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5 для непроизводственных сценариев, M64s, M64ms, M128s, M128ms, SAP HANA на крупных экземплярах Azure |
| HANA Enterprise для BW, OLAP | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA на крупных экземплярах Azure |
| SAP BW/4 HANA | Red Hat Enterprise Linux, SUSE Linux Enterprise | GS5, M64s, M64ms, M128s, M128ms, SAP HANA на крупных экземплярах Azure |

Все виртуальные машины Azure на данный момент сертифицированы для масштабирования SAP HANA.

## <a name="sap-netweaver-certifications"></a>Сертификация SAP NetWeaver
Платформа Microsoft Azure сертифицирована для следующих продуктов SAP при полной поддержке Майкрософт и SAP.
Справочные материалы:

- Список всех приложений на основе SAP NetWeaver, включая SAP TREX, SAP LiveCache и сервер содержимого SAP, приведен в [примечании SAP № 1928533 о поддерживаемых продуктах и типах виртуальных машин Azure](https://launchpad.support.sap.com/#/notes/1928533). Здесь также содержится список всех баз данных, кроме SAP HANA.


| Продукт SAP | Гостевая ОС | Реляционная СУБД | Типы виртуальных машин |
| --- | --- | --- | --- |
| ПО SAP Business Suite | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (только Windows и Oracle Linux), DB2, SAP ASE |От A5 до A11, от D11 до D14, от DS11 до DS14, от DS11_v2 до DS15_v2, от GS1 до GS5, от D2s_v3 до D64s_v3, от E2s_v3 до E64s_v3, от M64s до M128ms |
| SAP Business All-in-One | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (только Windows и Oracle Linux), DB2, SAP ASE |От A5 до A11, от D11 до D14, от DS11 до DS14, от DS11_v2 до DS15_v2, от GS1 до GS5, от D2s_v3 до D64s_v3, от E2s_v3 до E64s_v3, от M64s до M128ms |
| Бизнес-аналитика SAP BusinessObjects | Windows |Недоступно |От A5 до A11, от D11 до D14, от DS11 до DS14, от DS11_v2 до DS15_v2, от GS1 до GS5, от D2s_v3 до D64s_v3, от E2s_v3 до E64s_v3, от M64s до M128ms |
| SAP NetWeaver | Windows, SUSE Linux Enterprise, Red Hat Enterprise Linux, Oracle Linux |SQL Server, Oracle (только Windows и Oracle Linux), DB2, SAP ASE |От A5 до A11, от D11 до D14, от DS11 до DS14, от DS11_v2 до DS15_v2, от GS1 до GS5, от D2s_v3 до D64s_v3, от E2s_v3 до E64s_v3, от M64s до M128ms |

## <a name="other-sap-workload-supported-on-azure"></a>Другие рабочие нагрузки SAP, поддерживаемые в Azure

| Продукт SAP | Гостевая ОС | Реляционная СУБД | Типы виртуальных машин |
| --- | --- | --- | --- |
| SAP Business One на SQL Server | Windows  | SQL Server; | Все типы виртуальных машин, сертифицированные NetWeaver |
| SAP BPC 10.01 MS SP08 | Windows и Linux | | Все типы виртуальных машин, сертифицированные NetWeaver<br /> Примечание к SAP № 2451795 |
| Платформа SAP BusinessObjects BI | Windows и Linux | | Примечание к SAP № 2145537 |
| SAP Data Services 4.2 | | | Примечание к SAP № 2288344 |
| SAP Hybris Commerce Platform 5.x и 6.x | Windows | SQL Server, Oracle | Все типы виртуальных машин, сертифицированные NetWeaver<br /> [Вики-сайт Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
