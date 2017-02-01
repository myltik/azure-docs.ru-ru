---
title: "Установка SAP HANA на сервере SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт"
description: "Инструкции по установке SAP HANA на сервере SAP HANA в Azure (крупные экземпляры)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 3827e74433a859e65071a1041c5ed6c8253db8df
ms.openlocfilehash: be17b2de227ef6d93f8d326ff060c3c4788eb622


---
# <a name="install-sap-hana-on-sap-hana-on-azure-large-instances"></a>Установка SAP HANA на сервере SAP HANA в Azure (крупные экземпляры)

Установку SAP HANA вы выполняете самостоятельно. Это можно сделать сразу после получения нового сервера SAP HANA в Azure (крупные экземпляры). Обратите внимание, что согласно политике SAP, установку SAP HANA должен выполнять сертифицированный установщик SAP HANA (сдавший экзамен на установку SAP HANA в рамках сертификации технологического партнера SAP) или системный интегратор с сертификатом SAP.

Необходимо учитывать ряд аспектов, связанных с серверной и клиентской частью SAP HANA. Во многих случаях сервер SAP HANA отправляет свой IP-адрес клиенту, который кэширует этот адрес и использует его при последующих попытках подключения. Так как SAP HANA в Azure (крупные экземпляры) работает через NAT (т. е. внутренний IP-адрес в сети клиента преобразуется в IP-адрес из диапазона, предоставленного для соответствующих виртуальных сетей Azure), сервер базы данных SAP HANA будет отправлять диапазон &quot;внутренних&quot; IP-адресов. Например, для разрешения имени узла используется кэшированный внутренний IP-адрес, а SAP HANA не предоставляет преобразованный через NAT IP-адрес. Поэтому приложения, использующие клиент SAP HANA (ODBC, JDBC и т. п.), не смогут установить подключение по этому IP-адресу. Чтобы сервер SAP HANA сообщал клиенту преобразованный через NAT IP-адрес, необходимо отредактировать глобальный файл конфигурации SAP HANA (global.ini).

Добавьте в файл global.ini следующий код (вручную или с помощью SAP HANA Studio):
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
Дополнительную информацию и примеры см. в разделе с описанием _сопоставления имен узлов для доступа клиента базы данных_ в [руководстве администратора по SAP HANA](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf).

На стороне клиента (для серверов приложений SAP в Azure) измените файл/etc/hosts (в Linux) или /system32/drivers/etc/hosts (в Windows Server), добавив в него запись, содержащую имя узла SAP HANA в клиенте Azure (крупные экземпляры) и соответствующий преобразованный через NAT IP-адрес.

>[!NOTE] 
>Если при установке клиента базы данных SAP HANA возникнут ошибки подключения к SAP HANA в Azure (крупные экземпляры) во время установки клиента базы данных SAP HANA, вместо имени узла неявным образом используйте внешний IP-адрес клиента крупных экземпляров HANA.

## <a name="storage"></a>Хранилище

Структура хранения для SAP HANA в Azure (крупные экземпляры) настраивается через управление службами SAP HANA в Azure с применением рекомендованных методик, как описано в техническом документе с описанием [требований к хранилищу для SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Точная конфигурация хранилища для разных решений HANA (крупные экземпляры) выглядит так:

| Размер памяти | HANA/data | HANA/log | HANA/shared | HANA/log/backups |
| --- | --- | --- | --- | --- |
| 768 ГБ | 1280 ГБ | 512 ГБ | 768 ГБ | 512 ГБ |
| 1536 ГБ | 3456 ГБ | 768 ГБ | 1024 ГБ | 768 ГБ |
| 3072 ГБ | 7680 ГБ | 1536 ГБ | 1024 ГБ | 1536 ГБ |

Кроме того, клиенты могут приобрести дополнительную емкость хранилища с шагом в 1 ТБ. Это дополнительное хранилище добавляется к HANA (крупные экземпляры) как новые тома.

Контроллер хранилища и узлы в стеках больших экземпляров синхронизируются с NTP-серверами. Если выполняется синхронизация с NTP-сервером для единиц SAP HANA в Azure (крупные экземпляры) и виртуальных машин Azure, не должно быть заметного смещения времени между инфраструктурой и вычислительными единицами в Azure или стеками крупных экземпляров.

## <a name="operating-system"></a>операционная система

Для SAP HANA в Azure (большие экземпляры) используется дистрибутив [SUSE Linux Enterprise Server 12 SP1 для приложений SAP](https://www.suse.com/products/sles-for-sap/hana). Этот дистрибутив предоставляет все необходимые для SAP функции &quot;без дополнительной настройки&quot; (в том числе предварительно настроенные параметры для эффективного запуска SAP на сервере SLES).

В разделе [библиотеки ресурсов и технической документации](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) на веб-сайте SUSE и на [странице SAP для SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) в сетевом сообществе SAP (SCN) доступно несколько полезных ресурсов, в которых описывается развертывание SAP HANA на SLES (в том числе настройки высокой доступности, методы обеспечения безопасности для работы SAP и многое другое).

Дополнительная информация и полезные ссылки, имеющие отношение к SLES:

- [SAP HANA на сайте SUSE Linux](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE);
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113) (Лучшие рекомендации для SAP: создание очереди репликации — SAP NetWeaver на SUSE Linux Enterprise 12);
- [ClamSAP – защита от вирусов на SLES для SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (включая SLES 12 для приложений SAP).

Примечания по поддержке SAP, применимые к внедрению SAP HANA на SLES 12 SP1:

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html) (Примечание по поддержке SAP № 1944799 — Рекомендации для установки SAP HANA на ОС SLES);
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E) (Примечание по поддержке SAP № 220591 — рекомендуемые параметры ОС для SLES 12 для приложений SAP);
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12: Installation Notes](https://launchpad.support.sap.com/#/notes/1984787) (Примечание по поддержке SAP № 1984787 — примечания по установке SUSE LINUX Enterprise Server 12);
- [SAP Support Note #171356 – SAP Software on Linux: General Information](https://launchpad.support.sap.com/#/notes/1984787) (Примечание по поддержке № 171356 — общие сведения о ПО SAP на платформе Linux);
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070) (Примечание по поддержке № 1391070 — решения UUID для Linux).

## <a name="time-synchronization"></a>Синхронизация времени

Системы SAP очень чувствительны к разнице системного времени на разных компонентах. Если вы уже давно работаете с базовыми системами SAP, вам должны быть знакомы короткие дампы ABAP SAP с ошибкой, озаглавленной ZDATE\_LARGE\_TIME\_DIFF. Они появляются именно в том случае, когда системное время на разных серверах или виртуальных машинах существенно различается.

Для SAP HANA в Azure (крупные экземпляры) синхронизации времени в Azure не применяется к вычислительным единицам в стеках больших экземпляров. Это не относится к приложениям SAP, выполняемым в Azure обычным образом (на виртуальных машинах), так как Azure гарантирует правильную синхронизацию системного времени. Соответственно, потребуется отдельный сервер времени, который будут использовать серверы приложений SAP, работающие на виртуальных машинах Azure, и экземпляры баз данных SAP HANA, работающие на HANA (крупные экземпляры). Для инфраструктуры хранения в стеках крупных экземпляров время синхронизируется с серверами NTP.





<!--HONumber=Dec16_HO2-->


