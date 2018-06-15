---
title: Руководство по устранению неполадок и описание известных проблем | Документация Майкрософт
description: Список известных проблем и руководство по устранению неполадок
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: 05db4e47e5b2931a101a323a0210c080b87c1c42
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832947"
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench: руководство по устранению неполадок и описание известных проблем 
С помощью сведений в этой статье можно найти и исправить ошибки или сбои, обнаруженные при использовании приложения Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Поиск номера сборки рабочей среды
При обращении в группе поддержки важно указать номер сборки приложения Workbench. В Windows, чтобы узнать номер сборки, щелкните меню **Справка** и выберите **About Azure ML Workbench** (О Azure ML Workbench). В macOS можно щелкнуть меню **Azure ML Workbench** и выбрать **About Azure ML Workbench** (О Azure ML Workbench).

## <a name="machine-learning-msdn-forum"></a>Форум MSDN по машинному обучению
Вы можете публиковать вопросы на нашем форуме MSDN. За форумом активно следит команда разработчиков. URL-адрес форума: [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Сбор данных диагностики
Иногда при обращении за помощью полезно предоставить диагностические сведения. Вот где расположены файлы журналов:

### <a name="installer-log"></a>Журнал установщика
Если возникла проблема во время установки, то файлы журнала установщика можно найти здесь:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Можно заархивировать содержимое этих папок и отправить нам для диагностики.

### <a name="workbench-desktop-app-log"></a>Журнал классического приложения Workbench
Если возникли проблемы с входом или в случае аварийного завершения Workbench, файлы журнала можно найти здесь:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Можно заархивировать содержимое этих папок и отправить нам для диагностики.

### <a name="experiment-execution-log"></a>Журнал выполнения эксперимента
В случае сбоя какого-либо определенного сценария при отправке из классического приложения попытайтесь повторно отправить его с помощью команды интерфейса командной строки `az ml experiment submit`. Она должна выдать полное сообщение об ошибке в формате JSON и, что самое важное, значение**идентификатора операции**. Отправьте нам JSON-файл и **идентификатор операции**, и мы поможем диагностировать проблему. 

Если какой-либо сценарий успешно отправляется, но при его выполнении происходит сбой, то он должен выводить **идентификатор запуска**, чтобы можно было определить, во время которого выполнения это произошло. Вы можете упаковать соответствующие файлы журнала, используя следующую команду.

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

Команда `az ml experiment diagnostics` создает файл `diagnostics.zip` в корневой папке проекта. Этот ZIP-файл пакета содержит папку всего проекта в состоянии на момент выполнения, а также сведения о ведении журнала. Удалите все конфиденциальные сведения, которые не должны содержаться в файле диагностики, прежде чем отправить его нам.

## <a name="send-us-a-frown-or-a-smile"></a>Отправка нахмуренного или обычного смайлика

При работе с Azure ML Workbench вы можете также отправить нам нахмуренный или обычный смайлик, щелкнув значок смайлика в левом нижнем углу оболочки приложения. При необходимости вы можете указать свой адрес электронной почты (для обратной связи) и (или) снимок экрана текущего состояния. 

## <a name="known-service-limits"></a>Известные ограничения службы
- Максимально допустимый размер папки проекта: 25 МБ.
    >[!NOTE]
    >Это ограничение не применяется к папкам `.git`, `docs` и `outputs`. В именах этих папок учитывается регистр. При работе с большими файлами ознакомьтесь со статьей [Сохранение изменений и работа с большими файлами](../desktop-workbench/how-to-read-write-files.md).

- Максимальное время выполнения эксперимента: 7 дней.

- Максимальный размер файла, записанного в папку `outputs` после выполнения: 512 МБ.
  - Это означает, что, если скрипт создает файл размером больше 512 МБ в папке выходных данных, данные не будут собраны в этой папке. При работе с большими файлами ознакомьтесь со статьей [Сохранение изменений и работа с большими файлами](../desktop-workbench/how-to-read-write-files.md).

- Ключи SSH не поддерживаются при подключении к удаленному компьютеру или кластеру Spark через SSH. Сейчас поддерживается только режим с применением имени пользователя и пароля.

- Если кластер HDInsight применяется в качестве вычислительной цели, он должен использовать большой двоичный объект Azure в качестве основного хранилища. Использование хранилища Azure Data Lake не поддерживается.

- Преобразования кластеризации текста не поддерживается на компьютере Mac.

- Библиотека RevoScalePy поддерживается только в Windows и Linux (в контейнерах Docker). Она не поддерживается в macOS.

- Записные книжки Jupyter имеют ограничение максимального размера 5 МБ при открытии в приложении Workbench. Большие записные книжки можно открыть в интерфейсе командной строки с помощью команды az ml notebook start и очистить выходные данные ячейки для уменьшения размера файла.

## <a name="cant-update-workbench"></a>Не удается обновить Workbench
Когда появляется новое обновление, на главной странице приложения Workbench отображается сообщение, информирующее об этом. В левом нижнем углу приложения на значке с колокольчиком появляется индикатор обновления. Щелкните индикатор и следуйте указаниям мастера установки, чтобы установить обновление. 

![Изображение обновления](./media/known-issues-and-troubleshooting-guide/update.png)

Если уведомление не отображается, попробуйте перезапустить приложение. Если даже после перезапуска уведомление об обновлении не отображается, может быть несколько причин.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Вы запускаете приложение Workbench с помощью закрепленного ярлыка на панели задач
Обновление может быть уже установлено. Однако закрепленный ярлык по-прежнему указывает на старые биты на диске. Чтобы проверить это, перейдите в папку `%localappdata%/AmlWorkbench` и просмотрите, установлена ли там новейшая версия. Также следует изучить свойство закрепленного ярлыка, проверив, куда он указывает. После проверки просто удалите старый ярлык, из меню "Пуск" запустите программу Workbench и (необязательно) создайте новый закрепленный ярлык на панели задач.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Приложение Workbench установлено по ссылке "Установить Azure ML Workbench" на виртуальной машине для обработки и анализа данных (DSVM) Windows.
К сожалению, для этой проблемы нет простого решения. Необходимо выполнить следующие действия для удаления установленных битов, а затем скачать новейшую версию установщика для установки приложения Workbench с нуля: 
   - удалите папку `C:\Users\<Username>\AppData\Local\amlworkbench`;
   - удалите скрипт `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`;
   - удалите с рабочего стола ярлык, который запускает указанный выше скрипт;
   - скачайте установщик https://aka.ms/azureml-wb-msi и выполните повторную установку.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>После входа постоянно отображается экран с надписью о проверке учетной записи службы экспериментирования
После входа в приложение Workbench могут возникнуть затруднения. Отображается пустой экран с сообщением "Checking experimentation account" (Проверка учетной записи службы экспериментирования) и крутящееся колесико. Чтобы устранить эту проблему, выполните описанные ниже действия.
1. Завершите работу приложения.
2. Удалите следующий файл:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Перезапустите приложение.

## <a name="cant-delete-experimentation-account"></a>Не удается удалить учетную запись службы "Экспериментирование"
С помощью интерфейса командной строки можно удалить учетную запись службы "Экспериментирование". Но сначала необходимо удалить дочерние рабочие области и дочерние проекты в этих рабочих областях. В противном случае появится ошибка "Не удается удалить ресурс до удаления вложенных ресурсов".

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Вы также можете удалить проекты и рабочие области из приложения Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Не удается открыть файл, если проект находится в OneDrive
При использовании Windows 10 Fall Creators Update, если проект создан в локальной папке, сопоставленной с OneDrive, может сложиться ситуация, когда ни один файл не удается открыть в приложении Workbench. Причина в ошибке, которая появилась в обновлении Fall Creators Update и вызывает сбой кода node.js в папке OneDrive. Эта ошибка будет скоро исправлена в обновлении Windows, но до этого момента не следует создавать проекты в папке OneDrive.

## <a name="file-name-too-long-on-windows"></a>Имя файла слишком длинное для Windows
Если используется Workbench для Windows, вы можете столкнуться с используемым по умолчанию ограничением длины имени файла в 260 символов. Признаком этого может быть сообщение об ошибке "Системе не удается найти указанный путь". Можно изменить параметр раздела реестра, чтобы разрешить гораздо более длинные пути к файлам. Прочитайте [эту статью](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath), чтобы узнать больше о том, как настроить раздел реестра _MAX_PATH_.

## <a name="interrupt-cli-execution-output"></a>Прерывание вывода при выполнении CLI
Если вы запускаете эксперимент, используя `az ml experiment submit` или `az ml notebook start`, и необходимо прервать вывод: 
- в Windows используется сочетание клавиш CTRL+BREAK на клавиатуре;
- в macOS используется CTRL+C.

Обратите внимание на то, что это прерывает только поток вывода в окне CLI. Фактически выполнение задания не останавливается. Если вы хотите отменить текущее задание, используйте команду `az ml experiment cancel -r <run_id> -t <target name>`.

На компьютерах под управлением Windows с клавиатурами, на которых нет клавиши BREAK, можно использовать альтернативные сочетания, такие как Fn+B, CTRL+Fn+B или Fn+Esc. Конкретную комбинацию клавиш см. в документации поставщика оборудования.

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

Можно также добавить диск данных и настроить в подсистеме Docker использование этого диска для хранения образов. Сведения о добавлении диска данных см. [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Затем вы сможете [изменить расположение, в котором Docker хранит образы](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Вы также можете расширить диск ОС, и вам не придется менять конфигурацию подсистемы Docker. Сведения о расширении диска ОС см. [здесь](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Общий доступ к диску C в Windows
Вы можете повысить производительность выполнения в локальном контейнере Docker в Windows, установив для `sharedVolumes` значение `true` в файле `docker.compute` в папке `aml_config`. Но для этого потребуется предоставить общий доступ к диску C в _подсистеме Docker для Windows_. Если не удается предоставить общий доступ к диску C, воспользуйтесь следующими рекомендациями:

* Проверьте общий доступ к диску C в проводнике.
* Откройте параметры сетевого адаптера и удалите и/или переустановите службу доступа к файлам и принтерам сетей Microsoft для vEthernet.
* Откройте параметры Docker и предоставьте общий доступ к диску C.
* Изменение пароля Windows влияет на общий доступ. Откройте проводник, повторно предоставьте общий доступ к диску C и введите новый пароль.
* Также может возникнуть проблема с брандмауэром при попытке предоставить общий доступ к диску C с помощью Docker. Эта [запись Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) может быть полезна.
* При предоставлении общего доступа к диску C с помощью учетных данных домена общий доступ может перестать работать в сетях, где контроллер домена недоступен (например, домашняя сеть, общедоступный Wi-Fi и т. д.). Дополнительные сведения см. в [этой записи](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Вы также можете предотвратить проблему с общим доступом при низкой производительности, задав для `sharedVolumne` значение `false` в файле `docker.compute`.

## <a name="wipe-clean-workbench-installation"></a>Полная очистка установки Workbench
Обычно этого не нужно делать. Однако если необходимо выполнить полную очистку установки, сделайте следующее:

- Действия для ОС Windows.
  - Сначала убедитесь, что при удалении записи приложения _Azure Machine Learning Workbench_ используется приложение _Установка и удаление программ_ на _панели управления_.  
  - Затем можно скачать и запустить один из следующих сценариев:
    - [Сценарий командной строки Windows](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Сценарий Windows PowerShell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Необходимо выполнить `Set-ExecutionPolicy Unrestricted` в окне PowerShell с повышенными привилегиями, прежде чем вы сможете запустить сценарий.)
- Действия для MacOS.
  - Просто скачайте и запустите [сценарий оболочки macOS bash](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Использование службой "Машинное обучение Azure" расположения Python, отличного от расположения среды Python, установленной этой службой
Из-за последних изменений в Azure Machine Learning Workbench пользователи могут заметить, что локальные переменные выполнения могут не указывать на среду Python, установленную Azure ML Workbench. Это может произойти, если пользователь установил другую среду Python на компьютере и задал путь Python, указывающий на эту среду. Для использования среду Python, установленную Azure ML Workbench, выполните следующие действия.
- Перейдите к файлу local.compute в папке aml_config в корневом каталоге проекта.
- Измените переменную pythonLocation, указав физический путь к среде Python, установленной Azure ML Workbench. Этот путь можно получить двумя способами.
    - Расположение среды Python для Azure ML можно найти с помощью %localappdata%\AmlWorkbench\python\python.exe
    - Можно открыть окно командной строки из Azure ML Workbench, ввести в ней python, импортировать sys.exe, запустить sys.executable и получить путь. 



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
