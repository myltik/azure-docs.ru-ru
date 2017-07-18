---
title: "Как управлять хранилищем файлов Azure на портале Azure | Документация Майкрософт"
description: "Узнайте, как использовать портал Azure для управления хранилищем файлов Azure."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 58353a8058c2722788a38d2ca596061d14b68caf
ms.contentlocale: ru-ru
ms.lasthandoff: 07/12/2017

---

# <a name="how-to-use-azure-file-storage-from-the-azure-portal"></a>Как использовать хранилище файлов Azure на портале Azure
[Портал Azure](https://portal.azure.com) предоставляет пользовательский интерфейс для управления хранилищем файлов Azure. Следующие действия можно выполнять в браузере:

* Создание файлового ресурса
* передавать файлы в общую папку и скачивать файлы из нее;
* отслеживать фактическое использование каждой общей папки;
* изменять квоты на размер общей папки;
* копировать команду mount, которая позволит подключить общую папку с клиентского компьютера с Windows или Linux.

## <a name="create-file-share"></a>Создание общей папки
1. Войдите на портал Azure.
2. В меню навигации щелкните **Учетные записи хранения** или **Учетные записи хранения (классика)**.
    
    ![Снимок экрана, показывающий, как создать общую папку на портале](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. Выберите учетную запись хранения.

    ![Снимок экрана, показывающий, как создать общую папку на портале](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. Выберите службу "Файлы".

    ![Снимок экрана, показывающий, как создать общую папку на портале](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. Щелкните "Общие папки" и перейдите по ссылке для создания первой общей папки.

    ![Снимок экрана, показывающий, как создать общую папку на портале](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. Чтобы завершить создание своей первой общей папки, укажите ее имя и размер (до 5120 ГБ). После создания общей папки необходимо подключить ее из любой файловой системы, поддерживающей SMB 2.1 или SMB 3.0. Вы можете щелкнуть **Квота** в общей папке, чтобы изменить размер файла до 5120 ГБ. Используйте [калькулятор цен](https://azure.microsoft.com/pricing/calculator/), чтобы узнать стоимость использования хранилища файлов Azure.

    ![Снимок экрана, показывающий, как создать общую папку на портале](media/storage-file-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>Отправка и загрузка файлов
1. Выберите ранее созданную общую папку.

    ![Снимок экрана, показывающий, как отправлять и скачивать файлы с помощью портала](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. Щелкните **Загрузить** , чтобы открыть пользовательский интерфейс для отправки файлов.

    ![Снимок экрана, показывающий, как отправлять файлы с помощью портала](media/storage-file-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>Подключение к общей папке
-  Щелкните **Подключить**, чтобы вызвать командную строку для подключения общей папки из Windows и Linux. Дополнительные сведения о подключении для других дистрибутивов Linux см. в статье [Использование хранилища файлов Azure в Linux](storage-how-to-use-files-linux.md).

    ![Снимок экрана, показывающий, как подключить общую папку](media/storage-file-how-to-use-files-portal/use-files-portal-connect.png)
-  Вы можете скопировать команды для подключения общей папки в Windows или Linux, а также запустить их из виртуальной машины Azure или локально.

    ![Снимок экрана, показывающий команды подключения для Windows и Linux](media/storage-file-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**Подсказка.**  
Чтобы получить ключ доступа учетной записи хранения для подключения, щелкните ссылку **View access keys for this storage account** (Просмотр ключей доступа для этой учетной записи хранения) в нижней области страницы подключения.

## <a name="see-also"></a>См. также
Дополнительную информацию о хранилище файлов Azure см. по этим ссылкам.

* [Часто задаваемые вопросы](storage-files-faq.md)
* [Устранение неполадок](storage-troubleshoot-file-connection-problems.md)
