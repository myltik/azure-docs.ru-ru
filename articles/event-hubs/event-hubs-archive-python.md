<properties
	pageTitle="Пошаговое руководство. Архив концентраторов событий Azure | Microsoft Azure"
	description="Пример приложения, использующего пакет SDK Azure Python для демонстрации функции архива концентраторов событий."
	services="event-hubs"
	documentationCenter=""
	authors="andkjell"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="andkjell"/>

# Пошаговое руководство. Использование архива концентраторов событий с Python

Архив концентраторов событий — это новая функция концентраторов событий, которая позволяет автоматически передавать потоковые данные из концентратора событий в выбранную учетную запись хранилища BLOB-объектов Azure. Это упрощает выполнение пакетной обработки данных потоковой передачи в режиме реального времени. В этой статье мы расскажем, как использовать архив концентраторов событий с Python. Дополнительную информацию о концентраторах событий см. в [этом обзоре](event-hubs-archive-overview.md).

В приведенном примере для демонстрации функции архива используется пакет SDK Azure Python. Сценарий sender.py отправляет имитацию телеметрии окружающей среды концентраторам событий в формате JSON. Настройками концентратора событий предусмотрено использование функции архива для пакетной записи этих данных в хранилище BLOB-объектов. Затем archivereader.py считывает эти BLOB-объекты и создает файл для дозаписи для каждого устройства, после чего записывает данные в CSV-файлы.

Наши задачи:

1.  создать учетную запись хранилища BLOB-объектов Azure, содержащую контейнер BLOB-объектов, с помощью портала Azure;

2.  создать пространство имен концентратора событий с помощью портала Azure;

3.  создать концентратор событий с функцией архива с помощью портала Azure;

4.  отправить данные в концентратор событий с помощью сценария Python;

5.  прочитать файлы из архива и обработать их с помощью другого сценария Python.

Предварительные требования

1.  Python 2.7.x.

2.  Подписка Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Создание учетной записи хранения Azure

1.  Войдите на [портал Azure][].

2.  В области навигации слева на странице портала щелкните "Создать" > "Данные+хранилище" > "Учетная запись хранения".

3.  Заполните поля в колонке учетной записи хранения и нажмите кнопку **Создать**.

    ![][1]

4.  Получив сообщение об **успешном выполнении развертывания**, щелкните новую учетную запись хранения, затем в колонке **Основные компоненты** щелкните **BLOB-объекты**. В верхней части открывшейся колонки **Служба BLOB-объектов** щелкните **+ Контейнер**. Присвойте контейнеру имя **archive**, затем закройте колонку **Служба BLOB-объектов**.

5.  Щелкните **Ключи доступа** в колонке слева и скопируйте имя учетной записи хранения, а также значение **key1**. Сохраните на время эти значения в Блокноте или любом другом месте.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## Создание сценария Python для отправки событий в концентратор событий

1.  Откройте предпочитаемый редактор Python, например [Visual Studio Code][].

2.  Создайте сценарий с именем **sender.py**. Этот сценарий отправит 200 событий в ваш концентратор событий. События представляют собой простые данные показаний среды, пересылаемые в формате JSON.

3.  Скопируйте приведенный ниже код и вставьте его в сценарий sender.py.

	```
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
4.  Обновите предыдущий код, указав имя пространства имен и значения ключей, полученные при создании пространства имен концентратора событий.

## Создание сценария Python для чтения архивных файлов

1.  Заполните поля в колонке и щелкните **Создать**.

2.  Создайте сценарий с именем **archivereader.py**. Он будет считывать файлы архива и создавать для каждого устройства отдельный файл для записи данных только по этому устройству.

3.  Скопируйте приведенный ниже код и вставьте его в сценарий archivereader.py.

	```
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

4.  Не забудьте вставить соответствующие значения имени вашей учетной записи хранения и ключа в вызов `startProcessing`.

## Выполнение сценариев

1.  Откройте окно командной строки с Python в соответствующем расположении, а затем выполните следующие команды для установки пакетов необходимых компонентов Python.

	```
    pip install azure-storage
	pip install azure-servicebus
	pip install avro
    ```
  
    Если вы используете более раннюю версию Azure или хранилища Azure, вам может понадобиться параметр **--upgrade**.

    Возможно, вам также потребуется выполнить следующую команду (необязательно для большинства систем).

    ```
    pip install cryptography
    ```

2.  Укажите папку, в которой были сохранены сценарии sender.py и archivereader.py, и выполните следующую команду.

    ```
    start python sender.py
    ```
    
    Будет запущен новый процесс Python для выполнения сценария sender.

3. Подождите несколько минут до запуска архива. Теперь введите следующую команду в исходном окне командной строки.

    ```
    python archivereader.py
    ```

Этот обработчик архива загружает все BLOB-объекты из учетной записи хранения или контейнера в локальный каталог. Он обрабатывает все непустые файлы и записывает результаты в CSV-файлы в локальном каталоге.

## Дальнейшие действия

Дополнительные сведения о концентраторах событий см. в следующих источниках:

- [Azure Event Hubs Archive][] \(Архив концентраторов событий Azure)
- Полный [пример приложения, использующего концентраторы событий][].
- Пример [развертывания обработки событий при помощи концентраторов событий][].
- [Event Hubs overview][] \(Общие сведения о концентраторах событий Azure)
 

[портал Azure]: https://portal.azure.com/
[Azure Event Hubs Archive]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/documentation/articles/storage-create-storage-account/
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-overview.md
[пример приложения, использующего концентраторы событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[развертывания обработки событий при помощи концентраторов событий]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->
