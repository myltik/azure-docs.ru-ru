---
title: "Обзор Microsoft Azure IoT Suite | Документация Майкрософт"
description: "Общие сведения о том, как Azure IoT Suite предоставляет готовые решения IoT для сбора, анализа и хранения данных, а также средства визуализации и интеграции с другими системами."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 2d38d08a-4133-4e5c-8b28-f93cadb5df05
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: bfa8dbbd0b1d943a9eb7a042df0bac25189d9ac9
ms.contentlocale: ru-ru
ms.lasthandoff: 07/24/2017

---
# <a name="overview-of-azure-iot-suite"></a>Общие сведения о наборе Azure IoT Suite

Службы Интернета вещей (IoT) в Azure предоставляют широкий спектр возможностей. Это службы корпоративного уровня, которые обеспечивают:

* сбор данных с устройств;
* анализ движения потоков данных;
* хранение больших наборов данных и создание запросов к ним;
* визуализацию данных, получаемых в реальном времени, и архивных данных;
* интеграцию с системами операционных отделов организации.
* управление устройствами.

Для реализации этих возможностей в Azure IoT Suite часто используемые службы Azure сочетаются с пользовательскими расширениями, образуя *предварительно настроенные решения*. Эти предварительно настроенные решения являются базовой реализацией стандартных шаблонов решений IoT, позволяющих сократить время разработки решений IoT. С помощью [наборов средств для разработки программного обеспечения IoT][lnk-sdks] можно настроить и расширить эти решения в соответствии с вашими требованиями. Эти решения также можно использовать как примеры и шаблоны при разработке новых решений IoT.

Следующий видеоролик содержит обзорные сведения об Azure IoT Suite.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Службы Azure IoT в наборе Azure IoT Suite
Предварительно настроенные решения обычно используют следующие службы:

* Основой пакета Azure IoT Suite является служба [Центр Интернета вещей Azure][lnk-iot-hub]. Эта служба предоставляет возможности обмена сообщениями между устройствами и облаком, а также выступает в качестве шлюза для облака и других ключевых служб IoT Suite. Служба позволяет получать сообщения от устройств в реальном времени и отправлять команды на устройства. Также служба позволяет [управлять устройствами][lnk-device-management]. Например, вы можете настроить, перезагрузить или восстановить заводские настройки на одном или нескольких устройствах, подключенных к центру.
* [Azure Stream Analytics][lnk-asa] обеспечивает оперативный анализ данных. Пакет IoT Suite использует эту службу для обработки входящих данных телеметрии, агрегирования и обнаружения событий. Предварительно настроенные решения также используют Stream Analytics для обработки информационных сообщений, содержащих, например, метаданные или ответы на команды от устройств. Эти решения используют Stream Analytics для обработки сообщений от устройств и доставки этих сообщений другим службам.
* Возможности хранения данных предоставляются [службой хранилища Azure][lnk-azure-storage] и службой [Azure Cosmos DB][lnk-document-db]. Для хранения данных телеметрии и их подготовки к анализу предварительно настроенные решения используют хранилище BLOB-объектов. Эти решения используют Cosmos DB для хранения метаданных устройства и включают возможности управления устройствами для решений.
* Возможности визуализации данных предоставляются [веб-приложениями Azure][lnk-web-apps] и [Microsoft Power BI][lnk-power-bi]. Гибкость Power BI позволит вам быстро создавать собственные интерактивные панели мониторинга на основе данных из пакета IoT Suite.

Обзор архитектуры стандартного решения IoT см. в статье [Azure и "Интернет вещей"][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>предварительно настроенные решения

Пакет IoT Suite содержит предварительно настроенные решения, которые позволяют быстро приступить к работе и изучить наиболее распространенные сценарии, связанные с Интернетом вещей, в частности:

* удаленный мониторинг;
* диагностическое обслуживание;
* подключенная фабрика.

Вы можете развернуть эти решения в своей подписке Azure, а затем запустить готовый к использованию комплексный сценарий IoT.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы ознакомились с основной информацией о функциях, возможностях и основных компонентах IoT Suite, вы можете узнать больше о предварительно настроенных решениях в IoT Suite. Дополнительные сведения см. в статье [Что такое предварительно настроенные решения Azure IoT Suite?][lnk-what-are-preconfig]

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-device-management]: ../iot-hub/iot-hub-device-management-overview.md

