---
title: "Экспорт сертификатов в эмуляторе DocumentDB | Документы Майкрософт"
description: "При разработке на языках и в средах выполнения, не использующих хранилище сертификатов Windows, вам потребуется экспортировать SSL-сертификаты и управлять ими. В этой статье приведены пошаговые инструкции."
services: documentdb
documentationcenter: 
keywords: "Эмулятор DocumentDB"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 5f2fd8810041d47be2ef0b21e1487426a643d541
ms.lasthandoff: 03/07/2017


---

# <a name="export-the-documentdb-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Экспорт сертификатов в эмуляторе DocumentDB для использования с Java, Python и Node.js

[**Скачать эмулятор**](https://aka.ms/documentdb-emulator)

Эмулятор Azure DocumentDB предоставляет локальную среду, которая имитирует службу Azure DocumentDB, в том числе использование SSL-подключений, в целях разработки. В этой статье рассматривается, как экспортировать SSL-сертификаты для разработки на языках и в средах выполнения, не поддерживающих интеграцию с хранилищем сертификатов Windows, например в среде Java с собственным [хранилищем сертификатов](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), в среде Python, использующей [оболочки сокетов](https://docs.python.org/2/library/ssl.html), и в среде Node.js, использующей [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Дополнительные сведения об эмуляторе см. в статье [Использование эмулятора Azure DocumentDB для разработки и тестирования](./documentdb-nosql-local-emulator.md).

## <a name="certification-rotation"></a>Смена сертификатов

Сертификаты в локальном эмуляторе DocumentDB создаются при первом его запуске. Создается два сертификата: один используется для подключения к локальному эмулятору, а второй — для управления секретами в нем. Вам нужно экспортировать сертификат подключения с именем DocumentDBEmulatorCertificate.

Оба сертификата можно повторно создать, щелкнув **Reset Data** (Сбросить данные) в эмуляторе DocumentDB, запущенном на панели задач Windows, как показано ниже. Если вы повторно создали сертификаты и установили их в хранилище сертификатов Java или использовали их в другом месте, их необходимо обновить. В противном случае приложение не сможет подключиться к локальному эмулятору.

![Сброс данных в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>Как экспортировать SSL-сертификаты DocumentDB

1. Запустите диспетчер сертификатов Windows. Для этого запустите файл certlm.msc и перейдите в папку Personal -> Certificates, а затем откройте сертификат с понятным именем **DocumentDBEmulatorCertificate**.

    ![Шаг 1. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. Щелкните **Сведения**, а затем нажмите кнопку **ОК**.

    ![Шаг 2. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. Щелкните **Копировать в файл...**.

    ![Шаг 3. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. Нажмите кнопку **Далее**.

    ![Шаг 4. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. Щелкните **No, do not export private key** (Не экспортировать закрытый ключ), а затем — **Далее**.

    ![Шаг 5. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. Щелкните **Файлы X.509 (.CER) в кодировке Base-64**, а затем — **Далее**.

    ![Шаг 6. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. Укажите имя сертификата. В этом случае — **documentdbemulatorcert**. Щелкните **Далее**.

    ![Шаг 7. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. Нажмите кнопку **Готово**

    ![Шаг 8. Экспорт в локальном эмуляторе DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Как использовать сертификат в Java

При запуске приложений Java или MongoDB, использующих клиент Java, удобнее установить сертификат в хранилище сертификатов Java по умолчанию, нежели передавать флаги -Djavax.net.ssl.trustStore=<keystore> и -Djavax.net.ssl.trustStorePassword=<password>. Например, включенное [демонстрационное приложение Java](https://localhost:8081/_explorer/index.html) зависит от хранилища сертификатов по умолчанию.

Следуйте инструкциям в статье [Добавление сертификата в хранилище сертификатов ЦС Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store), чтобы импортировать сертификат X.509 в хранилище сертификатов Java по умолчанию. Имейте в виду, что при использовании keytool вы будете работать в каталоге %JAVA_HOME%

После установки SSL-сертификата DocumentDBEmulatorCertificate приложение сможет подключиться к локальному эмулятору DocumentDB и использовать его. Если проблемы не исчезли, выполните указания по [отладке подключений SSL и TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Вероятно, сертификат не был установлен в хранилище %JAVA_HOME%/jre/lib/security/cacerts. Например, если вы установили несколько версий Java, приложение может использовать другое хранилище сертификатов, а не обновленное.

## <a name="how-to-use-the-certificate-in-python"></a>Как использовать сертификат в Python

По умолчанию при подключении к локальному эмулятору [пакет SDK Python (версии 2.0.0 или выше)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-python) для DocumentDB не использует SSL-сертификат. Однако если вы хотите реализовать проверку SSL, используйте примеры, приведенные в документации по [оболочкам сокетов Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Как использовать сертификат в Node.js

По умолчанию при подключении к локальному эмулятору [пакет SDK Node.js (версии 1.10.1 или выше)](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-sdk-node) для DocumentDB не использует SSL-сертификат. Однако если вы хотите реализовать проверку SSL, используйте примеры, приведенные в [документации по Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о DocumentDB можно найти в статье [Знакомство с DocumentDB: база данных NoSQL JSON](documentdb-introduction.md).
* Чтобы начать разработку с помощью эмулятора DocumentDB, загрузите один из [поддерживаемых пакетов SDK для DocumentDB](documentdb-sdk-dotnet.md).

