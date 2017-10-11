---
title: "Руководство по устранению неполадок и описание известных проблем | Документация Майкрософт"
description: "Список известных проблем и руководство по устранению неполадок"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 8a8d4a9e9246bca5513787e776e91e7e3f2eed68
ms.contentlocale: ru-ru
ms.lasthandoff: 09/28/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench: руководство по устранению неполадок и описание известных проблем 
С помощью сведений в этой статье можно найти и исправить ошибки или сбои, обнаруженные при использовании приложения Azure Machine Learning Workbench. 

> [!IMPORTANT]
> При обращении в службу поддержки важно указывать номер сборки. Номер сборки приложения можно найти, выбрав меню **Справка**. Щелкните номер сборки, и он скопируется в буфер обмена. Его можно вставить в сообщение электронной почты или запись на форуме поддержки, когда вы обращаетесь за помощью.

![Проверка номера версии](media/known-issues-and-troubleshooting-guide/buildno.png)

## <a name="how-to-get-help"></a>Как получить справку
Получить справку можно несколькими способами.

### <a name="post-to-msdn-forum"></a>Публикация на форуме MSDN
Вы можете публиковать вопросы на нашем форуме MSDN. За форумом активно следит команда разработчиков. Адрес форума: [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum).

### <a name="gather-diagnostics-information"></a>Сбор данных диагностики
В некоторых случаях мы можем попросить вас отправить нам данные диагностики, касающиеся выполнения конкретного задания. Вы можете упаковать соответствующие файлы, используя следующую команду:

```azurecli
# Find out the run id first
$ az ml history list -o table

# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

Команда `az ml experiment diagnostics` создает файл `diagnostics.zip` в корневой папке проекта. Этот ZIP-пакет содержит папку всего проекта в состоянии на момент выполнения, а также сведения о ведении журнала. Удалите все конфиденциальные сведения, которые не должны содержаться в файле диагностики, прежде чем отправить его нам.

### <a name="send-us-a-frown-or-a-smile"></a>Отправка нахмуренного или обычного смайлика

При работе с Azure ML Workbench вы можете также отправить нам нахмуренный или обычный смайлик, щелкнув значок смайлика в левом нижнем углу оболочки приложения. При необходимости вы можете указать свой адрес электронной почты (для обратной связи) и (или) снимок экрана текущего состояния. 

## <a name="known-service-limits"></a>Известные ограничения службы
- Максимально допустимый размер папки проекта: 25 МБ.
    >[!NOTE]
    >Это ограничение не применяется к папкам `.git`, `docs` и `outputs`. В именах этих папок учитывается регистр. При работе с большими файлами ознакомьтесь со статьей [Сохранение изменений и работа с большими файлами](how-to-read-write-files.md).

- Максимальное время выполнения эксперимента: 7 дней.
- Максимальный размер файла, записанного в папку `outputs` после выполнения: 512 МБ.
  - Это означает, что, если скрипт создает файл размером больше 512 МБ в папке выходных данных, данные не будут собраны в этой папке. При работе с большими файлами ознакомьтесь со статьей [Сохранение изменений и работа с большими файлами](how-to-read-write-files.md).

- Ключи SSH не поддерживаются при подключении к удаленному компьютеру или кластеру Spark через SSH. Сейчас поддерживается только режим с применением имени пользователя и пароля.

- Преобразования кластеризации текста не поддерживается на компьютере Mac.

- Библиотека RevoScalePy поддерживается только в Windows и Linux (в контейнерах Docker). Она не поддерживается в macOS.

## <a name="docker-error-read-connection-refused"></a>Ошибка Docker "read: connection refused" (Чтение: в подключении отказано)
Иногда при выполнении в локальном контейнере Docker может произойти следующая ошибка: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Вы можете исправить ее, изменив значение DNS-сервера Docker с `automatic` на фиксированное значение `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Устранение ошибки "No tty present" (Отсутствует файл TTY) при выполнении виртуальной машины
При выполнении в локальном контейнере Docker на удаленной виртуальной машине Linux может появиться следующее сообщение об ошибке:
```
sudo: no tty present and no askpass program specified.
``` 
Это ошибка может произойти, когда вы меняете пароль пользователя root на виртуальной машине Ubuntu Linux с помощью портала Azure. 

В Azure Machine Learning Workbench требуется доступ без пароля к sudo для запуска на удаленных узлах. Для этого проще всего использовать команду _visudo_, чтобы изменить следующий файл (можете создать файл, если он не существует).

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Очень важно вносить изменения в файл с помощью команды _visudo_, а не другой команды. Команда _visudo_ автоматически проверяет синтаксис всех файлов конфигурации sudo. Если создать синтаксически неправильный файл sudoers, можно потерять доступ к sudo.

В конце файла вставьте следующую строку:

```
username ALL=(ALL) NOPASSWD:ALL
```

Где _username_ — имя пользователя, которое Azure Machine Learning Workbench будет использовать для входа на удаленный узел.

Строку необходимо вставить после #includedir /etc/sudoers.d. В противном случае ее может переопределить другое правило.

Если конфигурация sudo более сложная, ознакомьтесь с документацией по sudo для Ubuntu по следующей ссылке: https://help.ubuntu.com/community/Sudoers.

Описанная выше ошибка также может произойти, если в качестве целевого объекта задания вы используете не виртуальную машину под управлением Ubuntu Linux в Azure. Для удаленного выполнения поддерживается только виртуальная машина под управлением Ubuntu Linux. 

## <a name="vm-disk-is-full"></a>Диск виртуальной машины переполнен
По умолчанию при создании виртуальной машины Linux в Azure предоставляется диск размером 30 ГБ для операционной системы. Подсистема Docker использует по умолчанию один диск для извлечения образов и выполнения контейнеров. В результате диск ОС может быть заполнен и отобразится ошибка "VM Disk is Full" (Диск виртуальной машины переполнен).

Чтобы быстро устранить эту ошибку, удалите все образы Docker, которые вы больше не используете. Это можно сделать с помощью следующей команды Docker. (Конечно, необходимо подключиться к виртуальной машине по протоколу SSH, чтобы выполнить команду Docker в оболочке Bash.)

```
$ docker system prune -a
```

Можно также добавить диск данных и настроить в подсистеме Docker использование этого диска для хранения образов. Сведения о добавлении диска данных см. [здесь](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk). Затем вы сможете [изменить расположение, в котором Docker хранит образы](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Вы также можете расширить диск ОС, и вам не придется менять конфигурацию подсистемы Docker. Сведения о расширении диска ОС см. [здесь](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk).

## <a name="sharing-c-drive-on-windows"></a>Общий доступ к диску C в Windows
Вы можете повысить производительность выполнения в локальном контейнере Docker в Windows, установив для `sharedVolumes` значение `true` в файле `docker.compute` в папке `aml_config`. Но для этого потребуется предоставить общий доступ к диску C в _подсистеме Docker для Windows_. Если не удается предоставить общий доступ к диску C, воспользуйтесь следующими рекомендациями:

* Проверьте общий доступ к диску C в проводнике.
* Откройте параметры сетевого адаптера и удалите и/или переустановите службу доступа к файлам и принтерам сетей Microsoft для vEthernet.
* Откройте параметры Docker и предоставьте общий доступ к диску C.
* Изменение пароля Windows влияет на общий доступ. Откройте проводник, повторно предоставьте общий доступ к диску C и введите новый пароль.
* Также может возникнуть проблема с брандмауэром при попытке предоставить общий доступ к диску C с помощью Docker. Эта [запись Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) может быть полезна.
* При предоставлении общего доступа к диску C с помощью учетных данных домена общий доступ может перестать работать в сетях, где контроллер домена недоступен (например, домашняя сеть, общедоступный Wi-Fi и т. д.). Дополнительные сведения см. в [этой записи](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Вы также можете предотвратить проблему с общим доступом при низкой производительности, задав для `sharedVolumne` значение `false` в файле `docker.compute`.

## <a name="some-useful-docker-commands"></a>Некоторые полезные команды Docker

Ниже приведены некоторые полезные команды Docker.

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
