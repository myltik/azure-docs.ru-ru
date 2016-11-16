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
ms.date: 08/09/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7b0df1fc79e82a99ea65296a4e09bdb48fde9451


---
# <a name="what-is-azure-iot-suite"></a>Что такое Azure IoT Suite?
Службы Интернета вещей (IoT) в Azure предоставляют широкий спектр возможностей. Это службы корпоративного уровня, которые обеспечивают:

* сбор данных с устройств;
* анализ движения потоков данных;
* хранение больших наборов данных и создание запросов к ним;
* визуализацию данных, получаемых в реальном времени, и архивных данных;
* интеграцию с системами операционных отделов организации.

Для реализации этих возможностей в Azure IoT Suite часто используемые службы Azure сочетаются с пользовательскими расширениями, образуя *предварительно настроенные решения*. Эти предварительно настроенные решения являются базовой реализацией стандартных шаблонов решений IoT, позволяющих сократить время разработки решений IoT. С помощью [наборов средств разработки программного обеспечения Интернета вещей][lnk-sdks] можно настроить и расширить эти решения в соответствии с вашими требованиями. Эти решения также можно использовать как примеры и шаблоны при разработке новых решений IoT.

Следующий видеоролик содержит обзорные сведения об Azure IoT Suite.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON309/player]
> 
> 

## <a name="azure-iot-services-in-azure-iot-suite"></a>Службы Azure IoT в наборе Azure IoT Suite
Предварительно настроенные решения обычно используют следующие службы:

* Основой Azure IoT Suite является служба [Центра Интернета вещей Azure][lnk-iot-hub]. Эта служба предоставляет возможности обмена сообщениями между устройствами и облаком, а также выступает в качестве шлюза для облака и других ключевых служб IoT Suite. Служба позволяет получать сообщения от устройств в реальном времени и отправлять команды на устройства.
* Модуль обработки событий [Azure Stream Analytics][lnk-asa] обеспечивает оперативный анализ данных. Пакет IoT Suite использует эту службу для обработки входящих данных телеметрии, агрегирования и обнаружения событий. Предварительно настроенные решения также используют Stream Analytics для обработки информационных сообщений, содержащих, например, метаданные или ответы на команды от устройств. Эти решения используют Stream Analytics для обработки сообщений от устройств и доставки этих сообщений другим службам.
* Возможности хранения данных предоставляются [службой хранилища Azure][lnk-azure-storage] и [службой Azure DocumentDB][lnk-document-db]. Для хранения данных телеметрии и их подготовки к анализу предварительно настроенные решения используют хранилище BLOB-объектов. Эти решения используют DocumentDB для хранения метаданных устройства и включают возможности управления устройствами для решений.
* Возможности визуализации данных предоставляются [веб-приложениями Azure][lnk-web-apps] и [Microsoft Power BI][lnk-power-bi]. Гибкость Power BI позволит вам быстро создавать собственные интерактивные панели мониторинга на основе данных из пакета IoT Suite.

Обзор архитектуры стандартного решения Интернета вещей см. в статье [Azure и "Интернет вещей"][iot-suite-what-is-azure-iot].

## <a name="preconfigured-solutions"></a>предварительно настроенные решения
Пакет IoT Suite содержит предварительно настроенные решения, которые позволяют быстро приступить к работе и изучить наиболее распространенные сценарии, связанные с Интернетом вещей, например *Удаленный мониторинг* и *Диагностическое обслуживание*, которые можно выполнять с помощью Azure IoT Suite. Вы можете развернуть эти решения в своей подписке Azure, а затем запустить готовый к использованию комплексный сценарий IoT.

## <a name="next-steps"></a>Дальнейшие действия
Теперь, когда вы ознакомились с основной информацией о функциях, возможностях и основных компонентах IoT Suite, вы можете узнать больше о предварительно настроенных решениях в IoT Suite. См. статью [Что такое предварительно настроенные решения Azure IoT Suite?][lnk-what-are-preconfig].

[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md



<!--HONumber=Nov16_HO2-->


