---
title: Сохранение файлов для Bash в Azure Cloud Shell | Документация Майкрософт
description: Пошаговое руководство по сохранению файлов Bash в Azure Cloud Shell.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: d8188634846a7ce75b5294cb3012069d9eafafc1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919548"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>Как работает Bash в хранилище Cloud Shell 
Bash в Cloud Shell позволяет сохранять файлы с помощью обоих приведенных ниже методов. 
* Создание образа диска для каталога `$Home` для хранения всего содержимого этого каталога. Этот образ диска сохраняется как `acc_<User>.img` в `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, и для него выполняется автоматическая синхронизация изменений. 
* Подключение выбранного файлового ресурса как `clouddrive` в каталоге `$Home`, чтобы взаимодействовать с ним напрямую. `/Home/<User>/clouddrive` сопоставляется с `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Все файлы в каталоге `$Home`, такие как ключи SSH, сохраняются в образе диска пользователя, который хранится в подключенном файловом ресурсе. Соблюдайте рекомендации при сохранении информации в каталоге `$Home` и подключенном файловом ресурсе.

## <a name="use-the-clouddrive-command"></a>Использование команды `clouddrive`
С помощью Bash в Cloud Shell можно выполнить команду `clouddrive`, что позволит вручную обновить файловый ресурс, подключенный к Cloud Shell.
![Выполнение команды clouddrive](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Подключение нового облачного диска

### <a name="prerequisites-for-manual-mounting"></a>Предварительные требования для подключения вручную
Можно обновить файловый ресурс, который связан с Cloud Shell, с помощью команды `clouddrive mount`.

При подключении имеющегося файлового ресурса учетные записи хранения должны:
* относиться к локально избыточному или геоизбыточному хранилищу, чтобы обеспечить поддержку файловых ресурсов;
* Находиться в назначенном вам регионе. При подключении назначенный вам регион указывается в имени группы ресурсов `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>Команда clouddrive mount

> [!NOTE]
> При подключении нового файлового ресурса создается пользовательский образ вашего каталога `$Home`. Предыдущий образ `$Home` сохраняется в предыдущем файловом ресурсе.

Выполните команду `clouddrive mount` со следующими параметрами:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Чтобы просмотреть дополнительные сведения, выполните `clouddrive mount -h`, как показано ниже.

![Выполнение команды clouddrive mount](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Отключение облачного диска
Файловый ресурс, подключенный к Cloud Shell, можно отключить в любое время. Так как для Cloud Shell требуется подключенный файловый ресурс, вам будет предложено создать и подключить другой файловый ресурс в следующем сеансе.

1. Запустите `clouddrive unmount`.
2. Подтвердите запросы.

Файловый ресурс будет существовать до его удаления вручную. Cloud Shell больше не будет искать эту общую папку для последующих сеансов. Чтобы просмотреть дополнительные сведения, выполните `clouddrive unmount -h`, как показано ниже.

![Выполнение команды clouddrive unmount](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Хотя при выполнении этой команды ресурсы не удаляются, ручное удаление группы ресурсов, учетной записи хранения или файлового ресурса, сопоставленного с Cloud Shell, приведет к удалению образа диска каталога `$Home` и всех файлов в файловом ресурсе. Это действие невозможно отменить.

## <a name="list-clouddrive"></a>Вывод объектов `clouddrive`
Чтобы узнать, какой файловый ресурс подключен как `clouddrive`, выполните команду `df`. 

В пути к каталогу clouddrive указано имя учетной записи хранения и файловый ресурс в URL-адресе. Например, `//storageaccountname.file.core.windows.net/filesharename`

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

[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Дополнительная информация
[Краткое руководство по Bash в Cloud Shell](quickstart.md) <br>
[Сведения о хранилище файлов Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[Использование тегов для организации ресурсов в Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
