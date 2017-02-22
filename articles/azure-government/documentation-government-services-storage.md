---
title: "Хранилище Azure для государственных организаций | Документация Майкрософт"
description: "Данное руководство включает сравнительный анализ характеристик и рекомендации по разработке приложений для разработчиков Azure."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: 83df022b-d791-4efb-9fdf-8afe47a885d5
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/22/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 831334f2b835d00aa3fd22292764b69e85d735a6
ms.openlocfilehash: a6b61df5884031eb5b53f983b834357bd23a622e


---
# <a name="azure-government-storage"></a>Хранилище Azure для государственных организаций
## <a name="azure-storage"></a>Хранилище Azure
Дополнительные сведения об этой службе и ее использовании см. в [общедоступной документации по службе хранилища Azure](../storage/index.md).

### <a name="storage-service-availability-by-azure-government-region"></a>Доступность службы хранилища по регионам Azure для государственных организаций

| служба | Правительство штата Вирджиния | Правительство штата Айова | Примечания
| --- | --- | --- | --- |
| [Хранилище BLOB-объектов] (../storage/storage-introduction.md#blob-storage) |GA |GA |
| [Хранилище таблиц] (../storage/storage-introduction.md#table-storage) |GA  |GA |
| [Хранилище очередей] (../storage/storage-introduction.md#queue-storage) |GA | GA |
| [Хранилище файлов] (../storage/storage-introduction.md#file-storage) |GA |GA |
| [Хранилище BLOB-объектов "горячего" и "холодного" уровней] (../storage/storage-blob-storage-tiers.md) |Нет данных |Нет данных |
| [Шифрование службы хранилища] (../storage/storage-service-encryption.md) |GA |GA |
| [Хранилище класса Premium] (../storage/storage-premium-storage.md) |GA |Нет данных | Кроме того, доступны виртуальные машины серии DS. |
| [Импорт и экспорт больших двоичных объектов] (../storage/storage-import-export-service.md) |GA |GA |
| [StorSimple] (../storsimple/storsimple-ova-overview.md) |GA |GA |

### <a name="variations"></a>Варианты
URL-адреса для учетных записей хранения отличаются в Azure для государственных организаций:

| Тип службы | Azure Public | Azure Government |
| --- | --- | --- |
| Хранилище BLOB-объектов |*.blob.core.windows.net |*.blob.core.usgovcloudapi.net |
| Хранилище очередей |*.queue.core.windows.net |*.queue.core.usgovcloudapi.net |
| Хранилище таблиц |*.table.core.windows.net |*.table.core.usgovcloudapi.net |
| Хранилище файлов |*.file.core.windows.net |*.file.core.usgovcloudapi.net | 

> [!NOTE]
> Во всех сценариях и кодах должны учитываться соответствующие конечные точки.  См. статью [Настройка строк подключения службы хранилища Azure](../storage/storage-configure-connection-string.md). 
>
>

Дополнительные сведения об интерфейсах API см. в статье <a href="https://msdn.microsoft.com/en-us/library/azure/mt616540.aspx">Cloud Storage Account Constructor</a> (Конструктор учетных записей облачного хранилища).

В этих перегрузках следует использовать суффикс конечной точки core.usgovcloudapi.net.

> [!NOTE]
> [Обозреватель службы хранилища Microsoft Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md) сейчас не поддерживает [подключение к подписке Azure] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-an-azure-subscription) путем добавления учетной записи в Azure для государственных организаций. Используйте другие методы [подключения к учетной записи хранения] (../vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service).
При [подключение к внешней учетной записи хранения] (../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-to-an-external-storage-account) на **шаге 3** выберите значение **Other (specify below)** (Другое (укажите ниже)) в качестве домена конечной точки службы хранения и укажите значение **core.usgovcloudapi.net** для Azure для государственных организаций.
>
>

> [!NOTE]
> Возвращение ошибки 53 "Сетевой путь не найден" во время [подключения общей папки] (../storage/storage-dotnet-how-to-use-files.md#mount-the-file-share) может быть вызвано тем, что брандмауэр блокирует исходящий порт. Попробуйте подключить общую папку на виртуальной машине, которая находится в той же подписки Azure, что и учетная запись хранения.
>
>

> [!NOTE]
> Развертывая службу диспетчера StorSimple, используйте URL-адреса https://portal.azure.us/ и https://manage.windowsazure.us/ для портала Azure и классического портала соответственно. Инструкции по развертыванию для виртуального массива StorSimple см. в статье [Системные требования для виртуального массива StorSimple] (../storsimple/storsimple-ova-system-requirements.md), а для серий StorSimple 8000 — в статье [Программное обеспечение StorSimple, высокий уровень доступности и требования к сети] (../storsimple/storsimple-system-requirements.md), а затем с левой панели переходов перейдите к разделу развертывания. Общую документацию по StorSimple см. в [этом разделе] (../storsimple/index.md).
>
>

### <a name="considerations"></a>Рекомендации
Информация ниже определяет границу службы Azure для государственных организаций для службы хранилища Azure.

| Данные, подлежащие регулированию и контролю, разрешены. | Данные, подлежащие регулированию и контролю, не разрешены. |
| --- | --- |
| Некоторые сведения, которые вводятся в службу хранилища Azure, а также хранятся и обрабатываются в ней, могут подлежать экспортному контролю. Статические средства аутентификации, например пароли и PIN-коды смарт-карты, предназначенные для доступа к платформе Azure. Закрытые ключи сертификатов, используемые для управления компонентами платформы Azure. Другие сведения защиты и секреты, например сертификаты, ключи шифрования, главные ключи и ключи к хранилищу данных, хранящиеся в службах Azure. |Метаданные службы хранилища Azure не должны содержать данные, подлежащие экспортному контролю. К метаданным относятся данные конфигурации, которые вводятся во время создания и обслуживания продукта службы хранилища.  Не вводите данные, подлежащие контролю или регулированию, в такие поля: "Группы ресурсов", "Имена развертываний", "Имена ресурсов", "Теги ресурсов". |

## <a name="next-steps"></a>Дальнейшие действия
Чтобы получать дополнительные сведения и обновления, подпишитесь на <a href="https://blogs.msdn.microsoft.com/azuregov/">блог Microsoft Azure для государственных организаций</a>.



<!--HONumber=Feb17_HO2-->


