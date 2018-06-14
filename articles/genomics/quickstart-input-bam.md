---
title: Краткое руководство. Отправка рабочего процесса с помощью входного BAM-файла | Документация Майкрософт
titleSuffix: Azure
description: В этом руководстве предполагается, что вы установили клиент msgen и успешно запустили примеры данных через службу.
services: microsoft-genomics
author: grhuynh
manager: jhubbard
editor: jasonwhowell
ms.author: grhuynh
ms.service: microsoft-genomics
ms.workload: genomics
ms.topic: quickstart
ms.date: 12/07/2017
ms.openlocfilehash: 9887121593cad4931c86b55012f1c22686adf25f
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
ms.locfileid: "26684523"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Отправка рабочего процесса с помощью входного BAM-файла

В этом руководстве показано, как отправить рабочий процесс в службу Microsoft Genomics, если входной файл является одним BAM-файлом. В этом разделе предполагается, что вы уже установили и запустили клиент `msgen` и знаете, как использовать службу хранилища Azure. Если вы успешно отправили рабочий процесс с использованием предоставленного примера данных, можно продолжать работу с этим кратким руководством. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Настройка: передача BAM-файла в хранилище Azure
Предположим, что у вас есть один BAM-файл, *reads.bam*, и вы отправили его в свою учетную запись хранения *myaccount* в Azure как **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/inputs/reads<span></span>.bam<span></span>**. У вас есть URL-адрес API и ключ доступа. Вы хотите разместить выходные данные на странице **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Отправка задания в клиент `msgen` 


Минимальный набор аргументов, которые потребуется предоставить клиенту `msgen` (для наглядности добавлены разрывы строк):

Для Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Для Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Если вы предпочитаете использовать файл конфигурации, он должен содержать следующие строки:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Отправьте файл `config.txt` с помощью этого вызова: `msgen submit -f config.txt`

## <a name="next-steps"></a>Дополнительная информация
С помощью этой статьи вы научились отправлять BAM-файлы в службу хранилища Azure и освоили отправку рабочего процесса в службу Microsoft Genomics через клиент Python `msgen`. Дополнительные сведения об отправке рабочего процесса и других командах, которые можно использовать в службе Microsoft Genomics, см. в разделе [часто задаваемых вопросов](frequently-asked-questions-genomics.md). 
