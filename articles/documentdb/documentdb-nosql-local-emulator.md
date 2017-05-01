---
title: "Локальная разработка с помощью эмулятора DocumentDB | Документация Майкрософт"
description: "Используя эмулятор DocumentDB, можно разрабатывать и тестировать приложения локально, бесплатно и без создания подписки Azure."
services: documentdb
documentationcenter: 
keywords: "Эмулятор DocumentDB"
author: arramac
manager: jhubbard
editor: 
ms.assetid: 90b379a6-426b-4915-9635-822f1a138656
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/06/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 767698f12a5cb58fafc70c58e3d36a65194c2999
ms.lasthandoff: 04/07/2017


---
# <a name="use-the-azure-documentdb-emulator-for-development-and-testing"></a>Использование эмулятора Azure DocumentDB для разработки и тестирования

[**Скачать эмулятор**](https://aka.ms/documentdb-emulator)

Эмулятор Azure DocumentDB предоставляет для разработки локальную среду, которая имитирует службу Azure DocumentDB. Используя эмулятор DocumentDB, можно разрабатывать и тестировать приложения локально, без создания подписки Azure и без каких-либо затрат. Если приложение в эмуляторе DocumentDB работает правильно, вы можете использовать учетную запись Azure DocumentDB в облаке.

Рекомендуем просмотреть следующий видеоролик, в котором Кирилл Гаврилюк покажет, как начать работу с эмулятором DocumentDB.

> [!VIDEO https://channel9.msdn.com/Events/Connect/2016/192/player]
> 
> 

## <a name="system-requirements"></a>Требования к системе
Эмулятор DocumentDB имеет следующие требования к оборудованию и программному обеспечению.

* Требования к программному обеспечению
  * Windows Server 2012 R2, Windows Server 2016 или Windows 10.
*    Минимальные требования к оборудованию
  *    ОЗУ 2 ГБ.
  *    10 ГБ свободного дискового пространства.

## <a name="installation"></a>Установка
Эмулятор DocumentDB можно загрузить и установить из [центра загрузки Майкрософт](https://aka.ms/documentdb-emulator). 

> [!NOTE]
> Чтобы установить, настроить и запустить эмулятор DocumentDB, вам потребуются права администратора на локальном компьютере.

## <a name="running-on-docker-for-windows"></a>Выполнение в Docker для Windows

Эмулятор DocumentDB может выполняться в Docker для Windows. Этот эмулятор не работает в Docker для Oracle Linux.

Если установлен [Docker для Windows](https://www.docker.com/docker-windows), вы можете извлечь образ эмулятора из Docker Hub, выполнив следующую команду из любой оболочки по своему усмотрению (cmd.exe, PowerShell и т. д.).

```      
docker pull mominag/documentdb_emulator 
```
Чтобы запустить образ, выполните следующие команды:

``` 
md %LOCALAPPDATA%\DocumentDBEmulatorCert 2>nul
docker run -v %LOCALAPPDATA%\DocumentDBEmulatorCert:c:\DocumentDBEmulator\DocumentDBEmulatorCert -P -t -i mominag/documentdb_emulator
```

Ответ выглядит примерно так:

```
Starting Emulator
Emulator Endpoint: https://172.20.229.193:8081/
Master Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
Exporting SSL Certificate
You can import the SSL certificate from an administrator command prompt on the host by running:
cd /d %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
--------------------------------------------------------------------------------------------------
Starting interactive shell
``` 

Если закрыть интерактивную оболочку после запуска эмулятора, его контейнер завершит работу.

Воспользуйтесь конечной точкой и главным ключом из ответа в клиенте и импортируйте SSL-сертификат в узел. Чтобы импортировать SSL-сертификат, выполните следующие действия из командной строки с правами администратора.

```
cd %LOCALAPPDATA%\DocumentDBEmulatorCert
powershell .\importcert.ps1
```

## <a name="checking-for-updates"></a>Проверка наличия обновлений
Эмулятор DocumentDB включает в себя встроенный обозреватель данных Azure DocumentDB, который позволяет просматривать данные, хранящиеся в DocumentDB, создавать новые коллекции и получать информацию о доступности обновлений для загрузки. 

> [!NOTE]
> Данные, созданные в одной версии эмулятора DocumentDB, могут оказаться недоступными при использовании другой версии. Если нужно сохранить данные на длительный срок, мы рекомендуем хранить их в учетной записи хранения Azure, а не в эмуляторе DocumentDB. 

## <a name="how-the-emulator-works"></a>Принцип работы эмулятора
Эмулятор DocumentDB предоставляет высокоточную эмуляцию службы DocumentDB. Его функциональные возможности аналогичны Azure DocumentDB, включая поддержку создания документов JSON и выполнения запросов к ним, подготовку и масштабирование коллекций, а также выполнение хранимых процедур и триггеров. С помощью эмулятора DocumentDB можно разрабатывать и тестировать приложения. Чтобы развернуть эти приложения в глобальной среде Azure, потребуется лишь изменить один параметр конфигурации для конечной точки подключения к DocumentDB.

Хотя мы и создали высокоточную локальную эмуляцию настоящей службы DocumentDB, эмулятор DocumentDB имеет ряд отличий в реализации от этой службы. Например, эмулятор DocumentDB использует стандартные компоненты операционной системы: локальную файловую систему для сохранения данных и стек протокола HTTPS для подключений. Это означает, что некоторые возможности инфраструктуры Azure будут не доступны в эмуляторе DocumentDB. Среди таких возможностей глобальная репликация, задержка менее 10 миллисекунд для операций чтения и записи или настраиваемые уровни согласованности.


## <a name="authenticating-requests"></a>Выполнение проверки подлинности запросов
Как и настоящая облачная служба Azure, эмулятор DocumentDB требует проверки подлинности для каждого полученного запроса. Эмулятор DocumentDB поддерживает аутентификацию с помощью главного ключа, используя одну предопределенную учетную запись и хорошо известный ключ аутентификации. В качестве учетных данных для эмулятора DocumentDB можно использовать только эту учетную запись и только этот ключ. К ним относятся:

    Account name: localhost:<port>
    Account key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==

> [!NOTE]
> Главный ключ, поддерживаемый эмулятором DocumentDB, предназначен для использования только с этим эмулятором. Вы не сможете использовать для эмулятора DocumentDB рабочую учетную запись DocumentDB и ключ от нее. 

Кроме того, как и настоящая служба Azure DocumentDB, эмулятор DocumentDB поддерживает только безопасное подключение по протоколу SSL.

## <a name="start-and-initialize-the-emulator"></a>Запуск и инициализация эмулятора

Чтобы запустить эмулятор Azure DocumentDB, нажмите кнопку "Пуск" или клавишу Windows. Начните вводить текст **Эмулятор DocumentDB** и выберите эмулятор в списке приложений. 

![Нажмите кнопку "Пуск" или клавишу Windows, начните вводить **Эмулятор DocumentDB**, а затем выберите эмулятор в списке приложений](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-start.png)

При запуске эмулятора в области уведомлений панели задач Windows появится соответствующий значок. Эмулятор DocumentDB по умолчанию выполняется на локальном компьютере (localhost) и прослушивает порт 8081.

![Уведомление локального эмулятора DocumentDB на панели задач](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-taskbar.png)

Эмулятор DocumentDB по умолчанию устанавливается в каталог `C:\Program Files\DocumentDB Emulator`. Можно также запустить и остановить эмулятор из командной строки. Дополнительные сведения см. в [справочнике программы командной строки](#command-line).

## <a name="start-data-explorer"></a>Запуск обозревателя данных

При запуске эмулятор DocumentDB автоматически откроет в браузере страницу обозревателя данных DocumentDB. В адресной строке вы увидите адрес [https://localhost:8081/_explorer/index.html](https://localhost:8081/_explorer/index.html). Если вы захотите позднее открыть страницу обозревателя данных, вы можете ввести в браузере этот URL-адрес или запустить обозреватель из эмулятора DocumentDB, используя значок в области уведомлений Windows, как показано ниже.

![Запуск обозревателя данных из локального эмулятора DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-data-explorer-launcher.png)

## <a name="developing-with-the-emulator"></a>Разработка с помощью эмулятора
Когда эмулятор DocumentDB будет запущен на рабочем столе, вы можете работать с ним с помощью любых поддерживаемых [пакетов SDK для DocumentDB](documentdb-sdk-dotnet.md) или интерфейса [DocumentDB REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx). Эмулятор DocumentDB также включает встроенный обозреватель данных, который позволяет создавать коллекции, просматривать и редактировать документы без написания кода. 

    // Connect to the DocumentDB Emulator running locally
    DocumentClient client = new DocumentClient(
        new Uri("https://localhost:8081"), 
        "C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==");

Если вы используете [поддержку протокола DocumentDB для MongoDB](documentdb-protocol-mongodb.md), используйте следующую строку подключения:

    mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10250/admin?ssl=true&3t.sslSelfSignedCerts=true

Для подключения к эмулятору DocumentDB можно использовать любые существующие инструменты, например [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio). Можно также переносить данные между эмулятором DocumentDB и службой Azure DocumentDB с помощью [средства миграции данных DocumentDB](https://github.com/azure/azure-documentdb-datamigrationtool).

По умолчанию с помощью эмулятора DocumentDB можно создать до 25 односекционных коллекций или одну секционированную коллекцию. Дополнительные сведения об изменении этого значения см. в разделе [Изменение количества коллекций](#set-partitioncount).

## <a name="export-the-ssl-certificate"></a>Экспорт SSL-сертификата

Языки и среда выполнения .NET используют хранилище сертификатов Windows для безопасного подключения к локальному эмулятору DocumentDB. Другие языки используют собственные методы для управления сертификатами и использования сертификатов. Java поддерживает собственное [хранилище сертификатов](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), а Python применяет [оболочки сокетов](https://docs.python.org/2/library/ssl.html).

Чтобы использовать сертификат с языками и средами выполнения, которые не интегрируются с хранилищем сертификатов Windows, необходимо экспортировать сертификат с помощью диспетчера сертификатов Windows. Чтобы открыть его, запустите файл certlm.msc или выполните пошаговые инструкции из статьи [Экспорт сертификатов в эмуляторе DocumentDB](./documentdb-nosql-local-emulator-export-ssl-certificates.md). Когда откроется диспетчер сертификатов, откройте в нем "Личные сертификаты", как показано ниже, и экспортируйте сертификат с понятным именем DocumentDBEmulatorCertificate в формате X.509 с кодировкой BASE-64 (CER-файл).

![Сертификат SSL для эмулятора DocumentDB](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-ssl_certificate.png)

Чтобы импортировать сертификат X.509 в хранилище сертификатов Java, выполните инструкции из статьи [Добавление сертификата в хранилище сертификатов ЦС Java](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store). Когда сертификат будет импортирован в хранилище cacerts, приложения Java и MongoDB смогут подключаться к эмулятору DocumentDB.

При подключении к эмулятору с помощью пакетов SDK для Python и Node.js проверка SSL отключена.

## <a id="command-line"></a>Справочник по программе командной строки
С помощью командной строки из папки установки можно запускать и останавливать эмулятор, настраивать параметры и выполнять другие операции.

### <a name="command-line-syntax"></a>Синтаксис для командной строки

    DocumentDB.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/?]

Чтобы просмотреть список параметров, в командной строке введите `DocumentDB.Emulator.exe /?` .

<table>
<tr>
  <td><strong>Параметр</strong></td>
  <td><strong>Описание</strong></td>
  <td><strong>Команда</strong></td>
  <td><strong>Аргументы</strong></td>
</tr>
<tr>
  <td>[Нет аргументов]</td>
  <td>Запускает эмулятор DocumentDB с параметрами по умолчанию.</td>
  <td>DocumentDB.Emulator.exe</td>
  <td></td>
</tr>
<tr>
  <td>[Help]</td>
  <td>Отображает список поддерживаемых аргументов командной строки.</td>
  <td>DocumentDB.Emulator.exe /?</td>
  <td></td>
</tr>
<tr>
  <td>Shutdown</td>
  <td>Завершает работу эмулятора DocumentDB.</td>
  <td>DocumentDB.Emulator.exe /Shutdown</td>
  <td></td>
</tr>
<tr>
  <td>DataPath</td>
  <td>Указывает путь для сохранения файлов данных. По умолчанию используется папка %LocalAppdata%\DocumentDBEmulator.</td>
  <td>DocumentDB.Emulator.exe /DataPath=&lt;datapath&gt;</td>
  <td>&lt;datapath&gt;: любой доступный путь</td>
</tr>
<tr>
  <td>Порт</td>
  <td>Указывает номер порта, который должен использоваться эмулятором.  Значение по умолчанию — 8081.</td>
  <td>DocumentDB.Emulator.exe /Port=&lt;port&gt;</td>
  <td>&lt;port&gt;: один номер порта</td>
</tr>
<tr>
  <td>MongoPort</td>
  <td>Указывает номер порта для использования с интерфейсом совместимости с MongoDB. Значение по умолчанию — 10250.</td>
  <td>DocumentDB.Emulator.exe /MongoPort=&lt;mongoport&gt;</td>
  <td>&lt;mongoport&gt;: один номер порта</td>
</tr>
<tr>
  <td>DirectPorts</td>
  <td>Указывает порты, которые нужно использовать для прямого подключения. По умолчанию это порты 10251, 10252, 10253, 10254.</td>
  <td>DocumentDB.Emulator.exe /DirectPorts:&lt;directports&gt;</td>
  <td>&lt;directports&gt;: разделенный запятыми список из 4 портов</td>
</tr>
<tr>
  <td>Ключ</td>
  <td>Ключ проверки подлинности для эмулятора. Ключ должен иметь формат 64-разрядного вектора в кодировке Base-64.</td>
  <td>DocumentDB.Emulator.exe /Key:&lt;key&gt;</td>
  <td>&lt;key&gt;: ключ в формате 64-разрядного вектора в кодировке base-64</td>
</tr>
<tr>
  <td>EnableRateLimiting</td>
  <td>Указывает, что ограничение частоты запросов включено.</td>
  <td>DocumentDB.Emulator.exe /EnableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>DisableRateLimiting</td>
  <td>Указывает, что ограничение частоты запросов отключено.</td>
  <td>DocumentDB.Emulator.exe /DisableRateLimiting</td>
  <td></td>
</tr>
<tr>
  <td>NoUI</td>
  <td>Скрывает пользовательский интерфейс эмулятора.</td>
  <td>DocumentDB.Emulator.exe /NoUI</td>
  <td></td>
</tr>
<tr>
  <td>NoExplorer</td>
  <td>Скрывает обозреватель документов при запуске.</td>
  <td>DocumentDB.Emulator.exe /NoExplorer</td>
  <td></td>
</tr>
<tr>
  <td>PartitionCount</td>
  <td>Указывает максимальное количество секционированных коллекций. Дополнительные сведения см. в разделе [Изменение количества коллекций](#set-partitioncount).</td>
  <td>DocumentDB.Emulator.exe /PartitionCount=&lt;partitioncount&gt;</td>
  <td>&lt;partitioncount&gt;: максимально допустимое количество односекционных коллекций. Значение по умолчанию — 25. Максимально допустимое значение — 250.</td>
</tr>
</table>

## <a name="differences-between-the-documentdb-emulator-and-azure-documentdb"></a>Различия между эмулятором DocumentDB и службой Azure DocumentDB 
Эмулятор DocumentDB предоставляет эмулированную среду, выполняемую на локальном компьютере разработчика. Поэтому между эмулятором и облачной учетной записью Azure DocumentDB существуют функциональные различия.

* Эмулятор DocumentDB поддерживает только одну предопределенную учетную запись и известный главный ключ.  Повторное создание ключей в эмуляторе DocumentDB не поддерживается.
* Эмулятор DocumentDB не поддерживает масштабирование и не может работать с большим количеством коллекций.
* Эмулятор DocumentDB не поддерживает [уровни согласованности DocumentDB](documentdb-consistency-levels.md).
* Эмулятор DocumentDB не поддерживает [репликацию между несколькими регионами](documentdb-distribute-data-globally.md).
* Эмулятор DocumentDB не поддерживает переопределение квот для службы, которые возможны в службе Azure DocumentDB (например, ограничения на размер документа, увеличение хранилища для секционированных коллекций).
* Поскольку локальная версия эмулятора DocumentDB может не всегда отражать свежие изменения в службе Azure DocumentDB, для точной оценки необходимой пропускной способности (RU) для приложения используйте [планировщик ресурсов DocumentDB](https://www.documentdb.com/capacityplanner).

## <a id="set-partitioncount"></a>Изменение количества коллекций

По умолчанию с помощью эмулятора DocumentDB можно создать до 25 односекционных коллекций или одну секционированную коллекцию. Изменив значение **PartitionCount**, можно создать до 250 односекционных или 10 секционированных коллекций. А также можно комбинировать оба типа коллекций, при этом количество отдельных секций не должно превышать 250 (из расчета, что 1 секционированная коллекция = 25 односекционных коллекций).

При попытке создать коллекцию сверх этих ограничений на количество секций эмулятор порождает исключение ServiceUnavailable со следующим сообщением:

    Sorry, we are currently experiencing high demand in this region, 
    and cannot fulfill your request at this time. We work continuously 
    to bring more and more capacity online, and encourage you to try again. 
    Please do not hesitate to email docdbswat@microsoft.com at any time or 
    for any reason. ActivityId: 29da65cc-fba1-45f9-b82c-bf01d78a1f91

Чтобы изменить количество доступных для эмулятора DocumentDB коллекций, выполните следующие действия:

1. Удалите все локальные данные эмулятора DocumentDB, щелкнув правой кнопкой мыши значок **эмулятора DocumentDB** в области уведомлений, а затем щелкнув **Reset Data…** (Сброс данных…).
2. Удалите все данные эмулятора в этой папке на диске C:\Users\user_name\AppData\Local\DocumentDBEmulator.
3. Выйдите из всех открытых экземпляров, щелкнув правой кнопкой мыши значок **эмулятора DocumentDB** в области уведомлений, а затем щелкнув **Exit** (Выйти). Выход из всех экземпляров может занять около минуты.
4. Установите последнюю версию [эмулятора DocumentDB](https://aka.ms/documentdb-emulator).
5. Запустите эмулятор с флагом PartitionCount, задав значение <= 250. Например, `C:\Program Files\DocumentDB Emulator>DocumentDB.Emulator.exe /PartitionCount=100`.

## <a name="troubleshooting"></a>Устранение неполадок

Используйте следующие советы для устранения проблем, возникающих с эмулятором DocumentDB.

- В случае аварийного завершения эмулятора DocumentDB соберите файлы дампа из папки c:\Users\user_name\AppData\Local\CrashDumps, сожмите их, вложите в электронное сообщение и отправьте по адресу [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

- При возникновении сбоев в DocumentDB.StartupEntryPoint.exe выполните следующую команду из командной строки администратора: `lodctr /R` 

- Если возникли проблемы с подключением, [соберите файлы трассировки](#trace-files), сожмите их, вложите в электронное сообщение и отправьте по адресу [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com).

### <a id="trace-files"></a>Сбор файлов трассировки

Для сбора отладочных трассировок выполните следующие команды в командной строке с правами администратора.

1. `cd /d "%ProgramFiles%\DocumentDB Emulator"`
2. `DocumentDB.Emulator.exe /shutdown`. Проверьте область уведомлений, чтобы убедиться, что работа программы завершена, так как на это может потребоваться до минуты. Можно также просто щелкнуть **Exit** (Выйти) в пользовательском интерфейсе эмулятора DocumentDB.
3. `DocumentDB.Emulator.exe /starttraces`
4. `DocumentDB.Emulator.exe`
5. Воспроизведите проблему. Если обозреватель данных не работает, необходимо подождать несколько секунд, чтобы открылся браузер и ошибка возникла снова.
5. `DocumentDB.Emulator.exe /stoptraces`
6. Перейдите к `%ProgramFiles%\DocumentDB Emulator` и найдите файл docdbemulator_000001.etl.
7. Отправьте ETL-файл и описание действий, которые привели к ошибке, на электронный адрес [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com) для отладки.


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о DocumentDB можно найти в статье [Знакомство с DocumentDB: база данных NoSQL JSON](documentdb-introduction.md).
* Чтобы начать разработку с помощью эмулятора DocumentDB, загрузите один из [поддерживаемых пакетов SDK для DocumentDB](documentdb-sdk-dotnet.md).

