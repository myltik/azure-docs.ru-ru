---
title: Отправка рабочего процесса с использованием SAS вместо ключа учетной записи хранения | Документация Майкрософт
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
ms.date: 03/02/2018
ms.openlocfilehash: 802e300ac453baa4ea9bd9183223315abced2ea1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177220"
---
# <a name="submit-a-workflow-using-a-sas-instead-of-a-storage-account-key"></a>Отправка рабочего процесса с использованием SAS вместо ключа учетной записи хранения

Это краткое руководство демонстрирует, как отправить рабочий процесс в службу Microsoft Genomics, используя файл config.txt, содержащий [подписанный URL-адрес (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) вместо ключей учетной записи хранения. Эта функция может быть полезна, если есть опасения в отношении раскрытия ключа учетной записи хранения в файле config.txt. В этой статье предполагается, что вы уже установили и запустили клиент `msgen` и знаете, как использовать службу хранилища Azure. Если вы успешно отправили рабочий процесс с использованием предоставленного примера данных, можно продолжать работу с этим кратким руководством. 

## <a name="what-is-a-sas"></a>Что такое SAS?
[Подписанный URL-адрес (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) обеспечивает делегированный доступ к ресурсам в вашей учетной записи хранения. С помощью SAS можно предоставить доступ к ресурсам в учетной записи хранения, не предоставляя общий доступ к ключам учетной записи. Это ключевой момент использования подписанного URL-адреса в приложениях — SAS представляет собой безопасный способ предоставления общего доступа к ресурсам хранилища без ущерба для ключей учетной записи.

В Microsoft Genomics следует отправлять [подписанный URL-адрес уровня службы](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS), который предоставляет доступ только к большому двоичному объекту или контейнеру, где хранятся входные и выходные файлы. 

Универсальный код ресурса (URI) для токена SAS на уровне службы состоит из URI ресурса, к которому SAS будет предоставлять доступ, и следующего за ним токена SAS. Токен SAS представляет собой строку запроса, которая содержит всю информацию, необходимую для аутентификации SAS, а также сведения о ресурсе, разрешениях для доступа, интервал времени, в течение которого URL-адрес действителен, поддерживаемый IP-адрес или диапазон адресов, с которых могут поступать запросы, поддерживаемый протокол, с помощью которого может быть выполнен запрос, необязательный идентификатор политики доступа, связанный с запросом, и сам подписанный URL-адрес. 

## <a name="sas-needed-for-submitting-a-workflow-to-the-microsoft-genomics-service"></a>Необходимые свойства SAS для отправки рабочего процесса в службу Microsoft Genomics
Для каждого рабочего процесса, который отправляется в службу Microsoft Genomics, требуется два или более токенов SAS, по одному для каждого входного файла и один для выходного контейнера.

SAS для входных файлов должен иметь следующие свойства:
1.  Область (учетная запись, контейнер, большой двоичный объект): большой двоичный объект.
2.  Окончание срока действия: через 48 часов.
3.  Разрешения: чтение.

SAS для выходного контейнера должен иметь следующие свойства:
1.  Область (учетная запись, контейнер, большой двоичный объект): контейнер.
2.  Окончание срока действия: через 48 часов.
3.  Разрешения: чтение, запись, удаление.


## <a name="create-a-sas-for-the-input-files-and-the-output-container"></a>Создание SAS для входных файлов и выходного контейнера
Существует два способа создания токена SAS: с использованием Обозревателя службы хранилища Azure или программным способом.  При написании кода можно самостоятельно создать SAS. Можно также использовать пакет SDK службы хранилища Azure на предпочитаемом языке.


### <a name="set-up-create-a-sas-using-azure-storage-explorer"></a>Настройка. Создание SAS с помощью Обозревателя службы хранилища Azure

[Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) — это инструмент для управления ресурсами, сохраненными в службе хранилища Azure.  Дополнительные сведения об Обозревателе службы хранилища Azure см. [здесь](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

SAS для входных файлов должен быть привязан к конкретному входному файлу (большому двоичному объекту). Чтобы создать токен SAS, следуйте [этим инструкциям](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer#work-with-shared-access-signatures). После создания SAS на экране отобразятся полный URL-адрес со строкой запроса, а также сама строка запроса. Их можно скопировать.

 ![SAS для Genomics в Обозревателе службы хранилища](./media/quickstart-input-sas/genomics-sas-storageexplorer.png "SAS для Genomics в Обозревателе службы хранилища")


### <a name="set-up-create-a-sas-programattically"></a>Настройка. Создание SAS программным способом

Чтобы создать SAS с помощью SDK службы хранилища Azure, обратитесь к документации, доступной для нескольких языков, включая [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2#generate-a-shared-access-signature-uri-for-a-blob), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-python-how-to-use-blob-storage) и [Node.js](https://docs.microsoft.com/azure/storage/blobs/storage-nodejs-how-to-use-blob-storage#work-with-shared-access-signatures). 

Создание подписанного URL-адреса возможно без использования пакета SDK. Строку запроса SAS можно написать, включив все данные, необходимые для проверки подлинности SAS. В этих [инструкциях](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) подробно описаны компоненты строки запроса SAS и ее создание. Необходимый SAS создается путем создания HMAC с использованием информации для проверки подлинности большого двоичного объекта или контейнера, как описано в этих [инструкциях](https://docs.microsoft.com/rest/api/storageservices/service-sas-examples).


## <a name="add-the-sas-to-the-configtxt-file"></a>Добавление SAS в файл config.txt
Чтобы запустить рабочий процесс через службу Microsoft Genomics, используя строку запроса SAS, удалите из файла config.txt ключи. Затем добавьте строку запроса SAS (которая начинается с `?`) к имени выходного контейнера, как показано ниже 

![Конфигурация SAS Genomics](./media/quickstart-input-sas/genomics-sas-config.png "Конфигурация SAS Genomics")

Отправьте рабочий процесс через клиент Python Microsoft Genomics с помощью следующей команды, добавив соответствующую строку запроса SAS к каждому из имен входных больших двоичных объектов.

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read file, SAS query string appended] -b2 [name of your second paired end read file, SAS query string appended]
```

### <a name="if-adding-the-input-file-names-to-the-configtxt-file"></a>В случае добавления имен входных файлов в файл config.txt
Можно также напрямую добавить в файл config.txt имена пар целевых файлов чтения, добавив токены запросов SAS, как показано ниже.

![Имена больших двоичных объектов в конфигурации SAS для Genomics](./media/quickstart-input-sas/genomics-sas-config-blobnames.png "Имена больших двоичных объектов в конфигурации SAS для Genomics")

В этом случае используйте клиент Python Microsoft Genomics для отправки вашего рабочего процесса с помощью следующей команды, опустив команды `-b1` и `-b2`.

```python
msgen submit -f [full path to your config file] 
```

## <a name="next-steps"></a>Дополнительная информация
В этой статье использовались токены SAS вместо ключей учетной записи для отправки рабочего процесса в службу Microsoft Genomics через клиент Python `msgen`. Дополнительные сведения об отправке рабочего процесса и других командах, которые можно использовать в службе Microsoft Genomics, см. в разделе [часто задаваемых вопросов](frequently-asked-questions-genomics.md). 
