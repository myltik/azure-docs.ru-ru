---
title: "Перемещение данных в хранилище BLOB-объектов Azure и из него с помощью обозревателя хранилищ Azure | Документация Майкрософт"
description: "Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью обозревателя хранилищ Azure"
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 10bd283f-0875-4c67-af63-6492270b7656
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: a67f2e77d3bc7da35a03b68d7f32fd3a2a42bfcd
ms.openlocfilehash: 18c05ade1ee0c0c716307613a3c0daf1c8d853aa


---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Перемещение данных в хранилище больших двоичных объектов Azure и из него с помощью обозревателя хранилищ Azure
Azure Storage Explorer — это бесплатный инструмент от корпорации Майкрософт, позволяющий визуально работать с данными из службы хранилища Azure на платформе Windows, MacOS и Linux. В этом разделе описывается, как использовать этот инструмент для передачи и скачивания данных из хранилища BLOB-объектов Azure. Этот инструмент можно скачать с сайта [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

[!INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Если используется виртуальная машина, созданная с помощью скриптов, предоставленных [виртуальными машинами для обработки и анализа данных в Azure](machine-learning-data-science-virtual-machines.md), то обозреватель хранилищ Azure уже установлен на виртуальной машине.
> 
> [!NOTE]
> Полное описание базовых принципов использования хранилища BLOB-объектов Azure см. в статьях [Приступая к работе с хранилищем BLOB-объектов Azure с помощью .NET](../storage/storage-dotnet-how-to-use-blobs.md) и [Основные понятия службы BLOB-объектов](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Предварительные требования
Для выполнения указаний в этом документе у вас должна быть подписка Azure, учетная запись хранения и соответствующий ключ к хранилищу данных для этой учетной записи. Чтобы отправлять и скачивать данные, необходимо знать имя учетной записи хранения Azure и ее ключ. 

* Сведения о настройке подписки Azure см. на странице [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/).
* Инструкции по созданию учетной записи хранения и получению сведений об учетной записи и ключах см. в статье [Об учетных записях хранения Azure](../storage/storage-create-storage-account.md). Запишите ключ доступа для учетной записи хранения. Он необходим для подключения к учетной записи с помощью инструмента Azure Storage Explorer.
* Инструмент Azure Storage Explorer можно скачать с сайта [Microsoft Azure Storage Explorer](http://storageexplorer.com/). Во время установки примите значения по умолчанию.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Использование обозревателя хранилищ Azure
Ниже рассказывается о том, как отправить или скачать данные с помощью обозревателя хранилищ Azure. 

1. Запустите Microsoft Azure Storage Explorer.
2. Чтобы отобразить мастер **Sign in to your account…** (Вход в учетную запись…), щелкните значок **Azure account settings (Параметры учетной записи Azure)**, а затем выберите **Add an account** (Добавить учетную запись) и введите свои учетные данные. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Чтобы открыть мастер **Connect to Azure Storage** (Подключиться к хранилищу Azure), щелкните значок **Connect to Azure Storage** (Подключиться к службе хранилища Azure). ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Введите ключ доступа из своей учетной записи хранения Azure в мастере **Connect to Azure Storage** (Подключиться к хранилищу Azure), а затем нажмите кнопку **Далее**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Введите имя учетной записи хранения в поле **Account name** (Имя учетной записи) и нажмите кнопку **Далее**. ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Теперь добавленная учетная запись хранения должна быть в списке. Чтобы создать контейнер больших двоичных объектов в учетной записи хранения, щелкните правой кнопкой мыши узел **Blob Containers** (Контейнеры больших двоичных объектов) в этой учетной записи, выберите **Create Blob Container** (Создать контейнер BLOB-объектов) и введите имя.
7. Чтобы передать данные в контейнер, выберите целевой контейнер и нажмите кнопку **Передать**.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Щелкните знак многоточия **…** справа от поля **Файлы**, выберите в файловой системе один или несколько файлов для передачи и нажмите кнопку **Передать**, чтобы начать их передачу.![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Чтобы скачать данные, выберите большой двоичный объект в соответствующем контейнере и нажмите кнопку **Download**(Скачать). ![](./media/machine-learning-data-science-move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)




<!--HONumber=Nov16_HO3-->


