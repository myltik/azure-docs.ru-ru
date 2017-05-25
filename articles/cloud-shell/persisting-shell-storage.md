---
title: "Сохранение файлов в Azure Cloud Shell (предварительная версия) | Документация Майкрософт"
description: "Пошаговое руководство по сохранению файлов в Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 540cd10066e055e2dc132445b9adba5a4112d63a
ms.contentlocale: ru-ru
ms.lasthandoff: 05/16/2017

---

# <a name="persisting-files-in-azure-cloud-shell"></a>Сохранение файлов в Azure Cloud Shell
На начальном этапе Azure Cloud Shell запрашивает подписку для создания учетной записи хранения LRS и общей папки Azure.

![](media/storage-prompt.png)

### <a name="three-resources-will-be-created-on-your-behalf-in-a-supported-region-nearest-to-you"></a>В ближайшем поддерживаемом регионе от вашего имени будет создано три ресурса:
1. Группа ресурсов с именем: `cloud-shell-storage-<region>`
2. Учетная запись хранения с именем: `cs-uniqueGuid`
3. Общая папка с именем: `cs-<user>-<domain>-com-uniqueGuid`

Общая папка будет подключена как `clouddrive` в каталоге $Home. Она используется для хранения созданного образа размером 5 ГБ, который автоматически обновляет и сохраняет файлы в каталоге $Home. Подключение выполняется один раз, его не нужно повторять для последующих сеансов.

### <a name="cloud-shell-persists-files-with-both-methods-below"></a>Вот как можно сохранять файлы в Cloud Shell:
1. Создайте образ диска каталога $Home для сохранения файлов в этом каталоге. Этот образ диска сохраняется в указанной общей папке с именем `acc_<User>.img` в пути `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`.

2. Подключите указанную общую папку как `clouddrive` в каталоге $Home, чтобы взаимодействовать с ней напрямую. 
`/Home/<User>/clouddrive` сопоставляется с `fileshare.storage.windows.net/fileshare`.
 
> [!Note]
> Все файлы в каталоге $Home, такие как ключи SSH, сохраняются в образе диска пользователя, который хранится в подключенной общей папке. Соблюдайте рекомендации при сохранении информации в каталоге $Home и подключенной общей папке.

## <a name="using-clouddrive"></a>Использование команды clouddrive
Cloud Shell разрешает пользователям выполнять команду `clouddrive`, с помощью которой можно вручную обновить общую папку, подключенную к Cloud Shell.
![](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Подключение нового облачного диска

### <a name="pre-requisites-for-manual-mounting"></a>Предварительные требования для подключения вручную
Cloud Shell создаст учетную запись хранения и общую папку при первом запуске, но вы также можете обновить общую папку с помощью команды `clouddrive mount`.

При подключении имеющейся общей папки учетные записи хранения должны:
1. Соответствовать типу LRS или GRS для поддержки общих папок.
2. Находиться в назначенном вам регионе. При подключении регион, к которому вы привязаны, помещается в группу ресурсов `cloud-shell-storage-<region>`.

### <a name="supported-storage-regions"></a>Поддерживаемые регионы хранилища
Файлы Azure должны находиться в том же регионе, что и компьютер, к которому выполняется подключение. Компьютеры Cloud Shell находятся в следующих регионах:
|Область|Регион|
|---|---|
|Северная и Южная Америка|Восточная часть США, юго-центральный регион США, западная часть США|
|Европа|Северная Европа, Западная Европа|
|Азиатско-Тихоокеанский регион|Центральная Индия, Юго-Восточная Азия|

### <a name="mount-command"></a>Команда подключения

> [!NOTE]
> При подключении новой общей папки будет создан пользовательский образ для каталога $Home, так как предыдущий образ $Home хранится в предыдущей общей папке.

1. Выполните команду `clouddrive mount` со следующими параметрами: <br>

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Для получения дополнительных сведений выполните команду `clouddrive mount -h`: <br>
![](media/mount-h.png)

## <a name="unmount-clouddrive"></a>Отключение облачного диска
Общую папку, подключенную к Cloud Shell, можно отключить в любое время. Однако для Cloud Shell требуется подключенная общая папка, поэтому в случае ее удаления вам будет предложено создать и подключить новую общую папку для следующего сеанса.

Чтобы отключить общую папку в Cloud Shell, сделайте следующее:
1. Запустите `clouddrive unmount`
2. Подтвердите запросы.

Общая папка будет существовать до ее удаления вручную. Cloud Shell больше не будет искать эту общую папку для последующих сеансов.

Для получения дополнительных сведений выполните команду `clouddrive mount -h`: <br>
![](media/unmount-h.png)

> [!WARNING]
> Эта команда не удалит какие-либо ресурсы. Однако в результате ручного удаления группы ресурсов, учетной записи хранения или общей папки, сопоставляемой с Cloud Shell, будут удалены образ диска каталога $Home и все файлы в общей папке. Это действие невозможно отменить.

## <a name="list-clouddrive"></a>Вывод сведений об облачном диске
Чтобы узнать, какая общая папка подключается как `clouddrive`, сделайте следующее:
1. Запустите `df` 

В пути к каталогу clouddrive содержится имя учетной записи хранения и общая папка в URL-адресе.

`//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>Передача локальных файлов в Cloud Shell
Каталог `clouddrive` синхронизируется c колонкой хранилища на портале Azure. Используйте его для передачи локальных файлов в общую папку и их извлечения. Обновленные файлы в Cloud Shell отображаются в графическом интерфейсе хранилища файлов после обновления колонки.

### <a name="download-files"></a>Скачивание файлов
![](media/download.gif)
1. Перейдите к подключенной общей папке.
2. Выберите конечный файл на портале.
3. Щелкните "Скачать".

### <a name="upload-files"></a>Отправка файлов
![](media/upload.gif)
1. Перейдите к подключенной общей папке.
2. Щелкните "Отправить".
3. Выберите файл, который необходимо отправить.
4. Подтвердите отправку.

Теперь этот файл должен отобразиться в каталоге clouddrive в Cloud Shell.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по Cloud Shell](quickstart.md) 
[Хранилище файлов](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 
[Использование тегов для организации ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) 
