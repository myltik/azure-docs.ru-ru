---
title: "Пошаговое руководство. Архив концентраторов событий Azure | Документация Майкрософт"
description: "Пример приложения, использующего пакет SDK Azure Python для демонстрации функции архива концентраторов событий."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 0dc22f03c114508190a8da7ae4ca385c39690d2c
ms.openlocfilehash: ee85bfc9cfd852306a52d21772d33accdf484fdf


---
# <a name="event-hubs-archive-walkthrough-python"></a>Пошаговое руководство. Использование архива концентраторов событий с Python
Архив концентраторов событий — это новая функция концентраторов событий, которая позволяет автоматически передавать потоковые данные из концентратора событий в выбранную учетную запись хранилища BLOB-объектов Azure. Это упрощает выполнение пакетной обработки данных потоковой передачи в режиме реального времени. В этой статье мы расскажем, как использовать архив концентраторов событий с Python. Дополнительные сведения о концентраторах событий см. в [этом обзоре](event-hubs-archive-overview.md).

В приведенном примере для демонстрации функции архива используется пакет SDK Azure для Python. Программа sender.py отправляет имитацию телеметрии окружающей среды концентраторам событий в формате JSON. Настройками концентратора событий предусмотрено использование функции архива для пакетной записи этих данных в хранилище BLOB-объектов. Затем archivereader.py считывает эти большие двоичные объекты и создает файл для дозаписи для каждого устройства, после чего записывает данные в CSV-файлы.

Наши задачи:

1. создать учетную запись хранилища BLOB-объектов Azure, содержащую контейнер больших двоичных объектов, с помощью портала Azure;
2. создать пространство имен концентратора событий с помощью портала Azure;
3. создать концентратор событий с функцией архива с помощью портала Azure;
4. отправить данные в концентратор событий с помощью сценария Python;
5. прочитать файлы из архива и обработать их с помощью другого сценария Python.

Предварительные требования

- Python 2.7.x.
- Подписка Azure

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure
1. Войдите на [портал Azure][Azure portal].
2. В области навигации слева на странице портала щелкните **Создать** > **Данные+хранилище** > **Учетная запись хранения**.
3. Заполните поля в колонке учетной записи хранения и нажмите кнопку **Создать**.
   
   ![][1]
4. Получив сообщение об **успешном выполнении развертывания**, щелкните имя новой учетной записи хранения, затем в колонке **Основные компоненты** щелкните **BLOB-объекты**. В верхней части открывшейся колонки **Служба BLOB-объектов** щелкните **+ Container** (+ Контейнер). Присвойте контейнеру имя **archive**, затем закройте колонку **Служба BLOB-объектов**.
5. Щелкните **Ключи доступа** в колонке слева и скопируйте имя учетной записи хранения, а также значение **key1**. Сохраните на время эти значения в Блокноте или любом другом месте.

[!INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Создание сценария Python для отправки событий в концентратор событий
1. Откройте предпочитаемый редактор Python, например [Visual Studio Code][Visual Studio Code].
2. Создайте сценарий с именем **sender.py**. Этот сценарий отправит 200 событий в ваш концентратор событий. События представляют собой простые данные показаний среды, пересылаемые в формате JSON.
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
           sbs.send\_event('myhub', s)
       print y
   ```
4. Обновите предыдущий код, указав имя пространства имен и значения ключей, полученные при создании пространства имен концентратора событий.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Создание сценария Python для чтения архивных файлов
1. Заполните поля в колонке и щелкните **Создать**.
2. Создайте сценарий с именем **archivereader.py**. Он будет считывать файлы архива и создавать для каждого устройства отдельный файл для записи данных только по этому устройству.
3. Скопируйте приведенный ниже код и вставьте его в сценарий archivereader.py.
   
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
           parsed\_json = json.loads(reading["Body"])
           if not 'id' in parsed\_json:
               return
           if not dict.has\_key(parsed\_json['id']):
           list = []
           dict[parsed\_json['id']] = list
       else:
           list = dict[parsed\_json['id']]
           list.append(parsed\_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
       generator = block\_blob\_service.list\_blobs(container)
       for blob in generator:
           if blob.properties.content\_length != 0:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '\_')
               block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block\_blob\_service.delete\_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
   ```
4. Не забудьте вставить соответствующие значения имени вашей учетной записи хранения и ключа в вызов `startProcessing`.

## <a name="run-the-scripts"></a>Выполнение сценариев
1. Откройте окно командной строки с Python в соответствующем расположении, а затем выполните следующие команды для установки пакетов необходимых компонентов Python.
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Если вы используете более раннюю версию Azure или хранилища Azure, вам может понадобиться параметр **--upgrade** .
   
   Возможно, вам также потребуется выполнить следующую команду (необязательно для большинства систем).
   
   ```
   pip install cryptography
   ```
2. Укажите папку, в которой были сохранены сценарии sender.py и archivereader.py, и выполните следующую команду.
   
   ```
   start python sender.py
   ```
   
   Будет запущен новый процесс Python для выполнения сценария sender.
3. Подождите несколько минут до запуска архива. Теперь введите следующую команду в исходном окне командной строки.
   
    ```
    python archivereader.py
    ```

    Этот обработчик архива загружает все BLOB-объекты из учетной записи хранения или контейнера в локальный каталог. Он обрабатывает все непустые файлы и записывает результаты в CSV-файлы в локальном каталоге.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о концентраторах событий см. в следующих источниках:

* [обзор архива концентраторов событий][Overview of Event Hubs Archive];
* полный [пример приложения, использующего концентраторы событий][sample application that uses Event Hubs];
* пример [развертывания обработки событий с помощью концентраторов событий][Scale out Event Processing with Event Hubs];
* [Обзор концентраторов событий Azure][Event Hubs overview].

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Archive]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO2-->


