---
title: Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью Azure Storage Explorer | Документация Майкрософт
description: Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью обозревателя хранилищ Azure
services: machine-learning,storage
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: deguhath
ms.openlocfilehash: 464290901959ee052059b092b737e369928bd19b
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837795"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью Azure Storage Explorer
Azure Storage Explorer — это бесплатный инструмент от корпорации Майкрософт, позволяющий визуально работать с данными из службы хранилища Azure на платформе Windows, MacOS и Linux. В этом разделе описывается, как использовать этот инструмент для передачи и скачивания данных из хранилища BLOB-объектов Azure. Этот инструмент можно скачать с сайта [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Если используется виртуальная машина, созданная с помощью скриптов, предоставленных [виртуальными машинами для обработки и анализа данных в Azure](virtual-machines.md), то обозреватель хранилищ Azure уже установлен на виртуальной машине.
> 
> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>предварительным требованиям
Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ. 

* Сведения о настройке подписки Azure см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и получению сведений об учетной записи и ключах см. в статье [Об учетных записях хранения Azure](../../storage/common/storage-create-storage-account.md). Запишите ключ доступа для учетной записи хранения. Он необходим для подключения к учетной записи с помощью инструмента Azure Storage Explorer.
* Инструмент Azure Storage Explorer можно скачать с сайта [Microsoft Azure Storage Explorer](http://storageexplorer.com/). Во время установки примите значения по умолчанию.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Использование обозревателя хранилищ Azure
Ниже рассказывается о том, как отправить или скачать данные с помощью обозревателя хранилищ Azure. 

1. Запустите Microsoft Azure Storage Explorer.
2. Чтобы отобразить мастер **Sign in to your account…** (Вход в учетную запись…), щелкните значок **Azure account settings (Параметры учетной записи Azure)**, а затем выберите **Add an account** (Добавить учетную запись) и введите свои учетные данные. ![Добавление учетной записи хранения Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Чтобы открыть мастер **Connect to Azure Storage** (Подключиться к хранилищу Azure), щелкните значок **Connect to Azure Storage** (Подключиться к службе хранилища Azure). ![Подключение к службе хранилища Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Введите ключ доступа из своей учетной записи хранения Azure в мастере **Connect to Azure Storage** (Подключиться к хранилищу Azure), а затем нажмите кнопку **Далее**. ![Подключение к службе хранилища Azure](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Введите имя учетной записи хранения в поле **Account name** (Имя учетной записи) и нажмите кнопку **Далее**. ![Подключение внешнего хранилища](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Теперь добавленная учетная запись хранения должна быть в списке. Чтобы создать контейнер больших двоичных объектов в учетной записи хранения, щелкните правой кнопкой мыши узел **Blob Containers** (Контейнеры больших двоичных объектов) в этой учетной записи, выберите **Create Blob Container** (Создать контейнер BLOB-объектов) и введите имя.
7. Чтобы передать данные в контейнер, выберите целевой контейнер и нажмите кнопку **Отправить**. ![Учетные записи хранения](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Щелкните знак многоточия **…** справа от поля **Файлы**, выберите в файловой системе один или несколько файлов для передачи и нажмите кнопку **Отправить**, чтобы начать их передачу. ![Передача файлов](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Чтобы скачать данные, выберите большой двоичный объект в соответствующем контейнере и нажмите кнопку **Download**(Скачать). ![Скачивание файлов](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

