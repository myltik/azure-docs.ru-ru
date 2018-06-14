---
title: Пользовательские параметры для сред службы приложений
description: Настраиваемые параметры конфигурации для сред службы приложений
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129521"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Настраиваемые параметры конфигурации для сред службы приложений
## <a name="overview"></a>Обзор
Поскольку среды службы приложений изолированы для одного клиента, существуют определенные параметры конфигурации, которые можно применить только для сред службы приложений. В этой статье описываются различные настройки, доступные для сред службы приложений.

Сведения о том, как создать среду службы приложений, см. в статье [Создание среды службы приложений](app-service-web-how-to-create-an-app-service-environment.md).

Настройки среды службы приложений можно сохранить с помощью массива в новом атрибуте **clusterSettings** . Этот атрибут можно найти в словаре "Свойства" сущности *hostingEnvironments* Azure Resource Manager.

В следующем сокращенном фрагменте шаблона Resource Manager показан атрибут **clusterSettings** .

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

Атрибут **clusterSettings** можно включить в шаблон Resource Manager для обновления среды службы приложений.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Обновление среды службы приложений с помощью обозревателя ресурсов Azure
Среду службы приложений можно также обновлять с помощью [обозревателя ресурсов Azure](https://resources.azure.com).  

1. В обозревателе ресурсов перейдите к узлу для среды службы приложений (**subscriptions** > **resourceGroups** > **providers** > **Microsoft.Web** > **hostingEnvironments**). Затем щелкните ту среду службы приложений, которую хотите обновить.
2. В области справа, в верхней панели инструментов щелкните **Чтение/запись** , чтобы разрешить интерактивное редактирование в обозревателе ресурсов.  
3. Нажмите синюю кнопку **Изменить** , чтобы сделать шаблон Resource Manager доступным для редактирования.
4. Прокрутите область справа вниз. В самом низу области находится атрибут **clusterSettings**. Здесь можно ввести или обновить его значение.
5. Введите (или скопируйте и вставьте) массив значений параметров конфигурации в атрибут **clusterSettings** .  
6. Нажмите зеленую кнопку **РАЗМЕСТИТЬ** в верхней части области справа, чтобы зафиксировать изменение среды службы приложений.

Однако после отправки изменения для его вступления в силу потребуется время, равное количеству внешних интерфейсов в среде службы приложений, умноженному на 30 минут.
Например, если среда службы приложений имеет четыре внешних интерфейса, для завершения обновления конфигурации потребуется примерно два часа. Во время развертывания изменения конфигурации в среде службы приложений не может выполняться никаких других операций масштабирования или изменения конфигурации.

## <a name="disable-tls-10"></a>Отключение TLS 1.0
Клиенты, особенно те, которые имеют дело с аудитом соответствия требованиям PCI, часто спрашивают, как явно отключить TLS 1.0 для своих приложений.

TLS 1.0 можно отключить с помощью следующего атрибута **clusterSettings** :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Порядок изменения комплекта шифров TLS 
Пользователи также спрашивают о том, можно ли изменить список шифров, согласованный их сервер, изменив атрибут **clusterSettings** , как показано ниже. Список доступных наборов шифров можно получить в этой [статье MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Если для набора шрифтов указаны недопустимые значения, шифров (которые не распознаются в SChannel), подключение к серверу по протоколу TLS может быть разорвано. В этом случае необходимо удалить запись *FrontEndSSLCipherSuiteOrder* из **clusterSettings** и отправить обновленный шаблон Resource Manager, чтобы восстановить параметры комплекта шифров по умолчанию.  Используйте эту возможность с осторожностью.
> 
> 

## <a name="get-started"></a>Начало работы
На сайте шаблонов быстрого запуска Azure Resource Manager есть шаблон с базовым определением для [создания среды службы приложений](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
