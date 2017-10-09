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
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: ru-ru
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench: руководство по устранению неполадок и описание известных проблем 
С помощью сведений в этой статье можно найти и исправить ошибки или сбои, обнаруженные при использовании приложения Azure Machine Learning Workbench. 

> [!NOTE]
> При обращении к службе поддержки по электронной почте или с помощью записей на форуме полезно указывать номер сборки. Номер сборки приложения можно найти, выбрав меню **Справка**. Щелкните номер сборки, и он скопируется в буфер обмена. Его можно вставить в сообщение электронной почты или запись на форуме поддержки, когда вы обращаетесь за помощью.

![Проверка номера версии](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows и Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>Виртуальная машина Azure для обработки и анализа данных на основе CentOS 
* Отправка заданий в виртуальную машину Azure для обработки и анализа данных на основе CentOS не поддерживается. Можно выбрать [виртуальную машину для обработки и анализа данных на основе Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu).

### <a name="docker-error"></a>Ошибка Docker 
* Если вы обнаружите следующую ошибку при выполнении локального контейнера Docker, ее можно исправить, изменив DNS-сервер Docker с автоматического на фиксированный (значение 8.8.8.8). 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![Образ —](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Пароль для виртуальной машины Linux 
* При изменении пароля виртуальной машины под управлением Linux (Ubuntu) на портале Azure может появиться следующая ошибка во время выполнения задания:
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  Для устранения этой проблемы можно добавить файл (например, _myDockerUsers_) в _/etc/sudoers.d_ со следующим содержимым:
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>Ключи SSH 
* Ключи SSH не поддерживаются при подключении к удаленному компьютеру или кластеру Spark через SSH. Поддерживается только режим "имя пользователя и пароль".

## <a name="windows-only"></a>Только для Windows 
### <a name="sharing-c-drive-in-docker"></a>Общий доступ к диску C в Docker 
* Проверьте общий доступ к диску C в проводнике.
* Откройте параметры сетевого адаптера и удалите и/или переустановите службу доступа к файлам и принтерам сетей Microsoft для vEthernet.
* Откройте параметры Docker и предоставьте общий доступ к диску C.
* Изменение пароля Windows влияет на общий доступ. Откройте проводник, повторно предоставьте общий доступ к диску C и введите новый пароль.
* Также может возникнуть проблема с брандмауэром при попытке предоставить общий доступ к диску C с помощью Docker. Эта [запись Stack Overflow](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) может быть полезна.
* При предоставлении общего доступа к диску C с помощью учетных данных домена общий доступ может перестать работать в сетях, где контроллер домена недоступен (например, домашняя сеть, общедоступный Wi-Fi и т. д.). Дополнительные сведения см. в [этой записи](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).



## <a name="mac-only"></a>Только для Mac 
* Преобразования кластеризации текста не поддерживается на компьютере Mac.
* Библиотека RevoScalePy не поддерживается на компьютере Mac.

## <a name="azure-machine-learning-service-limits"></a>Ограничения службы машинного обучения Azure

- Максимально допустимый размер папки проекта: 25 МБ.
    >[!Note]
    >Это ограничение не применяется к папкам `.git`, `docs` и `outputs`. В именах этих папок учитывается регистр.

- Максимальное время выполнения эксперимента: 7 дней.
- Максимальный размер файла, записанного в папку `outputs` после выполнения: 512 МБ. 

>[!NOTE]
>При работе с большими файлами ознакомьтесь со статьей [Сохранение изменений и работа с большими файлами](how-to-read-write-files.md).

## <a name="other-issues"></a>Другие проблемы
Если вам известно о других недокументированных проблемах, отправьте нам отзыв, используя значок _Send a Smile/Frown_ (Отправить нахмуренный смайлик). 




