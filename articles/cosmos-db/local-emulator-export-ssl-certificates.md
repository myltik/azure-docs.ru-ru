---
title: "Экспорт сертификатов эмулятора Azure Cosmos DB | Документация Майкрософт"
description: "При разработке на языках и в средах выполнения, не использующих хранилище сертификатов Windows, вам потребуется экспортировать SSL-сертификаты и управлять ими. В этой статье приведены пошаговые инструкции."
services: cosmos-db
documentationcenter: 
keywords: "Эмулятор Azure Cosmos DB"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: ef14df93b7870c7bc4be0f644d32cc45d15b7856
ms.contentlocale: ru-ru
ms.lasthandoff: 05/31/2017


---

# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Экспорт сертификатов эмулятора Azure Cosmos DB для использования с Java, Python и Node.js

[**Скачать эмулятор**](https://aka.ms/documentdb-emulator)

Эмулятор Azure Cosmos DB предоставляет локальную среду, которая имитирует службу Azure Cosmos DB, в том числе использование SSL-подключений, в целях разработки. В этой статье рассматривается, как экспортировать SSL-сертификаты для разработки на языках и в средах выполнения, не поддерживающих интеграцию с хранилищем сертификатов Windows, например в среде Java с собственным [хранилищем сертификатов](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), в среде Python, использующей [оболочки сокетов](https://docs.python.org/2/library/ssl.html), и в среде Node.js, использующей [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Дополнительные сведения об эмуляторе см. в статье об [использовании эмулятора Azure Cosmos DB для разработки и тестирования](./local-emulator.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * ротация сертификатов;
> * экспорт сертификатов SSL;
> * использование сертификатов на Java, Python и Node.js.

## <a name="certification-rotation"></a>Смена сертификатов

Сертификаты в локальном эмуляторе Azure Cosmos DB создаются при первом его запуске. Создается два сертификата: один используется для подключения к локальному эмулятору, а второй — для управления секретами в нем. Вам нужно экспортировать сертификат подключения с именем Azure Cosmos DBEmulatorCertificate.

Оба сертификата можно повторно создать, щелкнув **команду сброса данных** в эмуляторе Azure Cosmos DB, запущенном на панели задач Windows, как показано ниже. Если вы повторно создали сертификаты и установили их в хранилище сертификатов Java или использовали их в другом месте, их необходимо обновить. В противном случае приложение не сможет подключиться к локальному эмулятору.

![Сброс данных в локальном эмуляторе Azure Cosmos DB](./media/local-emulator/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Как экспортировать SSL-сертификаты эмулятора Azure Cosmos DB

1. Запустите диспетчер сертификатов Windows. Для этого запустите файл certlm.msc и перейдите в папку Personal -> Certificates, а затем откройте сертификат с понятным именем **CosmosDBEmulatorCertificate**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 1)](./media/local-emulator/database-local-emulator-export-step-1.png)

2. Щелкните **Сведения**, а затем нажмите кнопку **ОК**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 2)](./media/local-emulator/database-local-emulator-export-step-2.png)

3. Щелкните **Копировать в файл...**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 3)](./media/local-emulator/database-local-emulator-export-step-3.png)

4. Щелкните **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 4)](./media/local-emulator/database-local-emulator-export-step-4.png)

5. Щелкните **No, do not export private key** (Не экспортировать закрытый ключ), а затем — **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 5)](./media/local-emulator/database-local-emulator-export-step-5.png)

6. Щелкните **Файлы X.509 (.CER) в кодировке Base-64**, а затем — **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 6)](./media/local-emulator/database-local-emulator-export-step-6.png)

7. Укажите имя сертификата. В этом случае — **documentdbemulatorcert**. Щелкните **Далее**.

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 7)](./media/local-emulator/database-local-emulator-export-step-7.png)

8. Нажмите кнопку **Готово**

    ![Экспорт данных в локальном эмуляторе Azure Cosmos DB (шаг 8)](./media/local-emulator/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Как использовать сертификат в Java

При запуске приложений Java или MongoDB, использующих клиент Java, удобнее установить сертификат в хранилище сертификатов Java по умолчанию, нежели передавать флаги -Djavax.net.ssl.trustStore=<keystore> и -Djavax.net.ssl.trustStorePassword=<password>. Например, включенное [демонстрационное приложение Java](https://localhost:8081/_explorer/index.html) зависит от хранилища сертификатов по умолчанию.

Следуйте инструкциям в статье [Добавление сертификата в хранилище сертификатов ЦС Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store), чтобы импортировать сертификат X.509 в хранилище сертификатов Java по умолчанию. Имейте в виду, что при использовании keytool вы будете работать в каталоге %JAVA_HOME%

После установки SSL-сертификата CosmosDBEmulatorCertificate приложение сможет подключиться к локальному эмулятору Azure Cosmos DB и использовать его. Если проблемы не исчезли, выполните указания по [отладке подключений SSL и TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Вероятно, сертификат не был установлен в хранилище %JAVA_HOME%/jre/lib/security/cacerts. Например, если вы установили несколько версий Java, приложение может использовать другое хранилище сертификатов, а не обновленное.

## <a name="how-to-use-the-certificate-in-python"></a>Как использовать сертификат в Python

По умолчанию при подключении к локальному эмулятору [пакет SDK Python (версии 2.0.0 или выше)](documentdb-sdk-python.md) для DocumentDB не использует SSL-сертификат. Однако если вы хотите реализовать проверку SSL, используйте примеры, приведенные в документации по [оболочкам сокетов Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Как использовать сертификат в Node.js

По умолчанию при подключении к локальному эмулятору [пакет SDK Node.js (версии 1.10.1 или выше)](documentdb-sdk-node.md) для DocumentDB не использует SSL-сертификат. Однако если вы хотите реализовать проверку SSL, используйте примеры, приведенные в [документации по Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * изменили сертификаты;
> * экспортировали SSL-сертификаты;
> * узнали, как использовать сертификаты на Java, Python и Node.js.

Теперь вы можете перейти к разделу ключевых концепций, чтобы получить дополнительные сведения о службе Cosmos DB.

> [!div class="nextstepaction"]
> [Глобальное распределение](distribute-data-globally.md) 

