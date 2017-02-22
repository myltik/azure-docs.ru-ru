---
title: "Обзор примеров ретранслятора служебной шины Azure | Документация Майкрософт"
description: "Классификация и описание примеров ретранслятора служебной шины со ссылками."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9237a9a2-f126-4d3f-9f9b-604ee6b32153
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/13/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 6027c973f0104fff9705a70f7812f62562165c1e
ms.openlocfilehash: d830770c49caeacf762382ce1c6113d9196a5994


---
# <a name="service-bus-relay-samples"></a>Примеры ретранслятора служебной шины
В этих примерах демонстрируются ключевые возможности [ретранслятора служебной шины](https://azure.microsoft.com/services/service-bus/). В этой статье приведены категории примеров с описаниями и ссылками.

> [!NOTE]
> Примеры для служебной шины не устанавливаются вместе с пакетом SDK для Azure. Примеры доступны на [странице примеров пакета SDK для Azure](https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=5).
> 
> Кроме того, [здесь](https://github.com/Azure-Samples/azure-servicebus-relay-samples) доступен обновленный набор примеров ретранслятора служебной шины.  
> 
> 

Примеры обмена сообщениями через служебную шину представлен в статье [Примеры обмена сообщениями с помощью служебной шины](../service-bus-messaging/service-bus-samples.md).

## <a name="azure-service-bus-relay"></a>Ретрансляция служебной шины Azure
В следующем примере показывается, как создавать приложения, использующие службу ретранслятора Azure.

Обратите внимание на то, что для примеров ретранслятора необходима строка подключения для доступа к пространству имен ретранслятора.

### <a name="to-obtain-a-connection-string-for-azure-relay"></a>Получение строки подключения для ретранслятора Azure
1. Войдите на [портал Azure](http://portal.azure.com).
2. Щелкните, чтобы развернуть список пространств имен в области **Все ресурсы**.
3. В списке щелкните имя пространства имен ретранслятора.
4. В колонке пространства имен щелкните **Политики общего доступа**.
5. В колонке **Политики общего доступа** щелкните **RootManageSharedAccessKey**.
6. Скопируйте строку подключения в буфер обмена.

### <a name="to-obtain-a-connection-string-for-service-bus-for-windows-server"></a>Получение строки подключения для служебной шины Windows Server
1. Выполните такой командлет PowerShell:
   
    ```powershell
    get-sbClientConfiguration
    ```
2. Вставьте строку подключения в файл App.config примера.

## <a name="azure-relay"></a>Ретранслятор Azure
Примеры, демонстрирующие использование ретранслятора Azure.

### <a name="getting-started"></a>Приступая к работе
| Имя примера | Описание | Минимальная версия пакета SDK | Доступность |
| --- | --- | --- | --- |
| [Обмен сообщениями с ретрансляцией WCF: Azure](http://code.msdn.microsoft.com/Relayed-Messaging-Windows-0d2cede3) |Демонстрирует запуск клиента и службы служебной шины в Azure. В этом примере осуществляется программная настройка служебной шины. В файлах конфигурации хранятся только сведения о среде и безопасности. |1.8 |Служебная шина Microsoft Azure |
| [Проверка подлинности при обмене сообщениями с ретрансляцией WCF: общий секрет](http://code.msdn.microsoft.com/Relayed-Messaging-92b04c02) |Демонстрирует использование имени поставщика и секрет от поставщика для проверки подлинности в служебной шине. |1.8 |Служебная шина Microsoft Azure |
| [Проверка подлинности при обмене сообщениями с ретрансляцией WCF: WebNoAuth](http://code.msdn.microsoft.com/Relayed-Messaging-a4f0b831) |Демонстрирует способ предоставления HTTP-службы, которая не требует проверки подлинности клиентов. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: WebHttp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-a6477ba0) |Демонстрирует использование привязки **WebHttpRelayBinding** для возвращения двоичных данных при использовании модели веб-программирования. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: NetTcp с ретрансляцией](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-2dec7692) |Демонстрирует использование привязки **NetTcpRelayBinding** . |1.8 |Служебная шина Microsoft Azure |

### <a name="exploring-features"></a>Обзор функций
Примеры, демонстрирующие различные функции ретранслятора служебной шины.

| Имя примера | Описание | Минимальная версия пакета SDK | Доступность |
| --- | --- | --- | --- |
| [Проверка подлинности при обмене сообщениями с ретрансляцией WCF: простой веб-маркер (SWT)](http://code.msdn.microsoft.com/Relayed-Messaging-32c74392) |Демонстрируется использование учетных данных простого веб-маркера для проверки подлинности с помощью служебной шины. Этот пример аналогичен примеру Echo, но содержит ряд изменений. В частности, этот пример добавляет поведение в приложения ServiceHost (служба) и ChannelFactory (клиент). |1.8 |Служебная шина Microsoft Azure |
| [Обмен сообщениями с ретрансляцией WCF: балансировка нагрузки](http://code.msdn.microsoft.com/Relayed-Messaging-Load-bd76a9f8) |Демонстрирует использование служебной шины Microsoft Azure для перенаправления сообщений нескольким получателям. В нем показывается простой обмен данными между несколькими экземплярами службы и клиентом через привязку **NetTcpRelayBinding** . |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: сетевое событие](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-c0176977) |Демонстрирует использование привязки **NetEventRelayBinding** в служебной шине Microsoft Azure. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: сеанс WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ef1f1fcb) |Демонстрирует использование привязки **WS2007HttpRelayBinding** с включением надежных сеансов. Также в примере описывается, как указывать учетные данные служебной шины в файле конфигурации, не используя программный подход. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: WS2007Http MsgSecCertificate](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-f29c9da5) |Демонстрирует использование привязки **WS2007HttpRelayBinding** с использованием защищенного режима для сквозного шифрования сообщений и требованием проверки подлинности клиентов с помощью служебной шины. |1.8 |Служебная шина Microsoft Azure |
| [Обмен сообщениями с ретрансляцией WCF: обмен метаданными](http://code.msdn.microsoft.com/Relayed-Messaging-Metadata-f122312e) |Демонстрирует способ предоставления конечной точки метаданных, которая использует привязку ретрансляции. **MetadataExchange** поддерживается в следующих привязках ретрансляции: **NetTcpRelayBinding**, **NetOnewayRelayBinding**, **BasicHttpRelayBinding** и **WS2007HttpRelayBinding**. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: прямое соединение NetTcp](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-ca039161) |Демонстрирует настройку привязки **NetTcpRelayBinding** для поддержки **гибридного** подключения. В этом сценарии сначала устанавливается подключение с ретрансляцией, а затем, если это возможно, происходит автоматическое переключение на непосредственный обмен данными между клиентом и службой. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: NetTcp MsgSec UserName](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-30542392) |Демонстрирует использование привязки **NetTcpRelayBinding** с функцией защиты сообщений. |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: Net Oneway](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-bb5b813a) |Демонстрирует способы предоставления и использования конечной точки службы с помощью привязки **NetOnewayRelayBinding** . |1.8 |Служебная шина Microsoft Azure |
| [Привязки при обмене сообщениями с ретрансляцией WCF: WS2007Http](http://code.msdn.microsoft.com/Relayed-Messaging-Bindings-aa4b793a) |Демонстрирует использование привязки **WS2007HttpRelayBinding** . В примере показана простая служба, не использующая параметры безопасности и не требующая проверки подлинности клиентов. |1.8 |Служебная шина Microsoft Azure |

## <a name="next-steps"></a>Дальнейшие действия
Принципиальные сведения о служебной шине см. в следующих статьях:

* [Что такое ретранслятор Azure?](relay-what-is-it.md)
* [Архитектура служебной шины](../service-bus-messaging/service-bus-architecture.md)
* [Базовая информация о служебной шине](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)




<!--HONumber=Feb17_HO2-->


