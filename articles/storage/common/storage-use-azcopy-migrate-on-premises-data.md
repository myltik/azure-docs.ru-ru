---
title: "Миграция локальных данных в хранилище Azure с помощью AzCopy | Документы Microsoft"
description: "Для переноса данных или скопировать данные в или из больших двоичных объектов, таблиц и содержимое файла с помощью команды AzCopy. Легко перенести данные из локального хранилища в хранилище Azure."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: f2c0b80248ef706394b3f84df4c3da26fb79026a
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/02/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Миграция локальных данных в Облачное хранилище с помощью AzCopy

AzCopy — программа командной строки, предназначенных для копирования данных из хранилища больших двоичных объектов Microsoft Azure, таблицы и файла, используя простых команд, предназначенных для обеспечения оптимальной производительности. Можно копировать данные между файловую систему и учетной записи хранения или учетные записи хранения.  

Существует две версии, которую можно загрузить AzCopy.

* [AzCopy в Linux](storage-use-azcopy.md) строится при помощи .NET Core Framework, который нацелен на платформы Linux предложения POSIX стиля параметры командной строки. 
* Служебная программа [AzCopy для Windows](../storage-use-azcopy.md) основана на платформе .NET Framework и использует параметры командной строки в формате Windows. 
 
Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создайте учетную запись хранения. 
> * С помощью команды AzCopy для отправки всех данных
> * Изменение данных в целях тестирования
> * Создание запланированного задания задачи или cron для определения новых файлов для отправки

## <a name="prerequisites"></a>Технические условия

Для работы с этим руководством: 

* Загрузите последнюю версию AzCopy на [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) или [Windows](http://aka.ms/downloadazcopy). 

## <a name="log-in-to-the-azure-portalhttpsportalazurecom"></a>Войдите на [портал Azure](https://portal.azure.com/).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Если требуется иметь возможность загрузить локальное хранилище больших двоичных объектов из вторичного региона и наоборот, задайте **репликации** для **чтения access географически избыточное хранилище**. При выборе этого параметра создается [географически избыточное хранилище](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage) учетной записи. 
>
>

## <a name="create-a-container"></a>Создание контейнера

Большие двоичные объекты всегда отправляются в контейнер. Контейнеры можно упорядочивать группы больших двоичных объектов, как упорядочение файлов на компьютере в папках. 

Выполните следующие действия, чтобы создать контейнер.

1. Выберите **учетные записи хранения** на главной странице и выберите созданную учетную запись хранилища.
2. Выберите **большие двоичные объекты** под **службы**, а затем выберите **контейнер**. 

![Создание контейнера](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Имена контейнеров должны начинаться с буквы или цифры и могут содержать только буквы, цифры и знак дефиса (-). Дополнительные сведения об именовании больших двоичных объектов и контейнеров см. в статье [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Именование контейнеров, больших двоичных объектов и метаданных и ссылка на них).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Отправить все файлы в папке в хранилище больших двоичных объектов

AzCopy можно использовать для передачи все файлы в папку для хранения больших двоичных объектов на [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) или [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Чтобы передать все большие двоичные объекты в папке, введите следующую команду AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Замените  **\<ключ\>**  ваш ключ учетной записи. На портале Azure можно получить ключ учетной записи, выбрав **ключи доступа** в настройках вашей учетной записи хранилища. Выберите ключ и вставить его в команды AzCopy. Если указанный контейнер назначения не существует, AzCopy создаст его и передаст в него файл. Обновить исходный путь к каталогу данных и замените **myaccount** в URL-адрес назначения для вашей учетной записи хранилища.

Укажите `--recursive` и `/S` параметры, Linux и Windows соответственно, для передачи содержимого в указанном каталоге рекурсивно хранилища больших двоичных объектов. При запуске AzCopy с одним из этих параметров, также загружаются все вложенные папки и файлы.

## <a name="upload-modified-files-to-blob-storage"></a>Передача измененных файлов хранилища больших двоичных объектов
Можно использовать AzCopy для [передачи файлов](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) на основе их времени последнего изменения. Попробуйте это, изменение или создание новых файлов в исходном каталоге для тестирования. Чтобы отправить только обновленных или новых файлов, добавьте `--exclude-older`или `/XO` соответственно команды AzCopy Linux и Windows.

Если требуется только для копирования ресурсов источника, которые не существуют в месте назначения, укажите оба `--exclude-older` и `--exclude-newer` или `/XO` и `/XN` соответственно команды параметров в AzCopy Linux и Windows. AzCopy отправляет обновленные данные по их меткам времени.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Создать запланированное задание, задачи или cron 
Можно создать запланированных задач/cron задания, который выполняет сценарий команды AzCopy, определяет и отправляет новый локальных данных облачного хранилища в определенный период времени. 

Скопируйте команду AzCopy в текстовый редактор. Обновите значения параметров команды AzCopy с соответствующими значениями. Сохраните файл как `script.sh` или `script.bat` для AzCopy в Linux и Windows соответственно. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy запускается с verbose `--verbose` (Linux) и `/V` параметр (Windows) и вывод перенаправляется в файл журнала. 

В этом учебнике [запланированное](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) используется для создания задачи, запланированные в Windows и [Crontab](http://crontab.org/) команда используется для создания задания cron в Linux. 
 **Запланированное** позволяет администратору запроса, создание, удаление, изменение, запуска и запланированные задачи на локальном или удаленном компьютере. **Cron** позволяет пользователям Unix и Linux для выполнения команд или сценариев в указанную дату и время с использованием [выражение cron](https://en.wikipedia.org/wiki/Cron#CRON_expression)


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
**Чтобы создать задание cron в Linux**, введите следующую команду для терминала. 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Указание выражения cron `*/5 * * * * ` в команде указывает сценарий оболочки `script.sh` должна выполняться каждые пять минут. Скрипт может быть запланировано выполнение в определенное время каждый день, месяц или год. В разделе [выражения cron](https://en.wikipedia.org/wiki/Cron#CRON_expression) для получения дополнительных сведений о настройке дату и время для выполнения заданий. 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
**Для создания задачи, запланированные на Windows**, введите следующую команду в командной строке или PowerShell.

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

Команда использует `/SC` для указания расписания и `/MO` параметр для задания интервала времени в пять минут. `/TN` Параметр используется для указания имени задачи и `/TR` параметр для указания пути к `script.bat` файла. Посетите [запланированное](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes) для получения дополнительных сведений о создании запланированной задачи в Windows.

---
 
Для запланированных задач/cron проверки правильности выполнения задания, создание новых файлов в каталоге `myfolder`. Подождите пять минут, чтобы убедиться, что новые файлы были загружены в вашу учетную запись хранения. Перейдите в каталог журнала для просмотра журналов выходные данные задания, запланированные задачи/cron. 

Посетите [перемещения на локальные данные](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) Дополнительные сведения о способах перемещения локальных данных в хранилище Azure и наоборот.  

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительной информации о службе хранилища Azure и AzCopy ознакомьтесь со следующими ресурсами.

### <a name="azure-storage-documentation"></a>Документация по хранилищу Azure:
* [Введение в хранилище Azure](../storage-introduction.md)
* [Передача данных с помощью AzCopy в Windows](storage-use-azcopy.md) 
* [Перенос данных с помощью AzCopy для Linux](storage-use-azcopy-linux.md) 
* [создать учетную запись хранения;](../storage-create-storage-account.md)
* [Управление ресурсами хранилища BLOB-объектов Azure с помощью обозревателя хранилищ (предварительная версия)](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

