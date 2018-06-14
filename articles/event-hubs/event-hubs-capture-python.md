---
title: Пошаговое руководство. Запись концентраторов событий Azure | Документация Майкрософт
description: Пример приложения, использующего пакет SDK Azure Python для демонстрации функции записи концентраторов событий.
services: event-hubs
documentationcenter: ''
author: djrosanova
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 97cadbde2ddedade1a8688f1380b9ff9194613e7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2018
ms.locfileid: "28016463"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Пошаговое руководство. Использование записи концентраторов событий с Python

Запись — это функция концентраторов событий Azure. Она позволяет автоматически передавать потоковые данные из концентратора событий в выбранную учетную запись хранилища BLOB-объектов Azure. Эта возможность упрощает выполнение пакетной обработки данных потоковой передачи в режиме реального времени. В этой статье мы расскажем, как использовать запись концентраторов событий с Python. Дополнительные сведения о записи концентраторов событий см. в [этом обзоре](event-hubs-capture-overview.md).

В приведенном примере для демонстрации функции записи используется [пакет SDK Azure для Python](https://azure.microsoft.com/develop/python/). Программа sender.py отправляет имитацию телеметрии окружающей среды концентраторам событий в формате JSON. В настройках концентратора событий предусмотрено использование функции записи для пакетной записи этих данных в хранилище BLOB-объектов. Приложение capturereader.py считывает эти большие двоичные объекты и создает для каждого устройства файл для дозаписи. Затем приложение записывает данные в CSV-файлы.

## <a name="what-youll-accomplish"></a>Задачи, которые вы выполните

1. Создание учетной записи хранилища BLOB-объектов Azure, содержащей контейнер больших двоичных объектов, с помощью портала Azure.
2. Создание пространства имен концентраторов событий с помощью портала Azure.
3. Создание концентратора событий с включенной функцией записи с помощью портала Azure.
4. Отправка данных в концентратор событий с помощью скрипта Python.
5. Чтение файлов из записи и их обработка с помощью еще одного скрипта Python.

## <a name="prerequisites"></a>предварительным требованиям

- Python 2.7.x.
- Подписка Azure
- Активные [пространство имен концентраторов событий и концентратор событий](event-hubs-create.md).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Создание учетной записи хранения BLOB-объектов Azure
1. Войдите на [портал Azure][Azure portal].
2. В левой области портала последовательно выберите **Создать** > **Хранилище** > **Учетная запись хранения**.
3. Выберите нужные параметры в области **Создание учетной записи хранения** и нажмите кнопку **Создать**.
   
   ![Область "Создание учетной записи хранения"][1]
4. Получив сообщение об **успешном завершении развертывания**, выберите имя новой учетной записи хранения, а затем в колонке **Основные компоненты** щелкните **BLOB-объекты**. В верхней части открывшейся панели **Служба BLOB-объектов** выберите **+ Контейнер**. Присвойте контейнеру имя **capture**, затем закройте область **Служба BLOB-объектов**.
5. Выберите **Ключи доступа** в области слева и скопируйте имя учетной записи хранения, а также значение **key1**. Сохраните на время эти значения в Блокноте или любом другом месте.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Создание сценария Python для отправки событий в концентратор событий
1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
2. Создайте сценарий с именем **sender.py**. Этот скрипт отправляет 200 событий в ваш концентратор событий. События представляют собой простые данные показаний среды, пересылаемые в формате JSON.
3. Скопируйте приведенный ниже код и вставьте его в сценарий sender.py.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Обновите предыдущий код, указав имя пространства имен, значение ключа и имя концентратора событий, полученные при создании пространства имен концентратора событий.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Создание скрипта Python для чтения файлов записи

1. Введите нужные сведения в поля области и нажмите кнопку **Создать**.
2. Создайте скрипт с именем **capturereader.py**. Он считывает собранные файлы и создает для каждого устройства отдельный файл для записи данных только по этому устройству.
3. Скопируйте приведенный ниже код и вставьте его в скрипт capturereader.py.
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not dict.has_key(parsed_json['id']):
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. Вставьте соответствующие значения для имени учетной записи хранения и ключа в вызов `startProcessing`.

## <a name="run-the-scripts"></a>Выполнение сценариев
1. Откройте окно командной строки с Python в соответствующем расположении, а затем выполните следующие команды для установки пакетов необходимых компонентов Python.
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Если вы используете более раннюю версию **azure-storage** или **azure**, вам может понадобиться параметр **--upgrade**.
   
   Возможно, вам также потребуется выполнить следующую команду (необязательно для большинства систем).
   
   ```
   pip install cryptography
   ```
2. Укажите папку, в которой были сохранены скрипты sender.py и capturereader.py, и выполните следующую команду.
   
   ```
   start python sender.py
   ```
   
   Эта команда запускает новый процесс Python для выполнения скрипта sender.
3. Подождите несколько минут до запуска записи. Теперь введите следующую команду в исходном окне командной строки.
   
   ```
   python capturereader.py
   ```

   Этот обработчик записи загружает все большие двоичные объекты из учетной записи хранения или контейнера в локальный каталог. Он обрабатывает все непустые файлы и записывает результаты в CSV-файлы в локальном каталоге.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о концентраторах событий см. по следующим ссылкам:

* [Запись концентраторов событий Azure][Overview of Event Hubs Capture]
* [Примеры приложений, использующие концентраторы событий](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Обзор концентраторов событий Azure][Event Hubs overview].

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
