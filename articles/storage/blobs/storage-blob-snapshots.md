---
title: Создание моментального снимка большого двоичного объекта только для чтения в службе хранилища Azure | Документация Майкрософт
description: Узнайте, как создать моментальный снимок большого двоичного объекта для резервного копирования данных BLOB-объектов в конкретный момент времени. Узнайте, как выставляются счета за моментальные снимки и как их можно использовать для сокращения расходов емкости.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 1a27dfd61850d9dfa1f232eacf7f09d66202cafe
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29852937"
---
# <a name="create-a-blob-snapshot"></a>Создание моментального снимка BLOB-объекта

Моментальный снимок — это версия BLOB-объекта только для чтения, сделанная в определенный момент времени. Моментальные снимки полезны для архивации BLOB-объектов. После создания моментального снимка его можно прочитать, скопировать или удалить, но вы не можете изменять его.

Моментальный снимок большого двоичного объекта идентичен объекту, на основе которого он создан. Единственное исключение: к URI большого двоичного объекта добавляется значение **DateTime**, которое указывает время создания снимка. Например, если страничный BLOB-объект имеет URI `http://storagesample.core.blob.windows.net/mydrives/myvhd`, то URI снимка будет иметь такой вид: `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Все моментальные снимки имеют одинаковый URI базового большого двоичного объекта. Большой двоичный объект и его моментальный снимок отличаются только добавлением значения **DateTime** .
>

Большой двоичный объект может иметь любое количество моментальных снимков. Моментальные снимки хранятся до тех пор, пока не будут явно удалены. При удалении исходного BLOB-объекта моментальный снимок будет также удален. Чтобы было удобнее контролировать моментальные снимки большого двоичного объекта, их можно пронумеровать.

При создании моментального снимка BLOB-объекта его системные свойства и их значения копируются в снимок. Метаданные базового большого двоичного объекта также копируются в моментальный снимок, если при его создании не были указаны отдельные метаданные для снимка.

Свойства аренды, связанные с базовым BLOB-объектом, не влияют на моментальный снимок. Вы не можете получить аренду в моментальном снимке.

VHD-файл используется для хранения текущей информации о диске виртуальной машины и его состояния. Можно отключить диск в виртуальной машине или завершить ее работу, после чего создать моментальный снимок его VHD-файла. Этот файл моментального снимка можно будет использовать позже, чтобы получить VHD-файл на этот момент времени и повторно создать виртуальную машину.

## <a name="create-a-snapshot"></a>Создание моментального снимка
В следующем примере кода показано, как создать моментальный снимок с помощью [клиентской библиотеки хранилища Azure для .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). В этом примере мы указываем дополнительные метаданные для моментального снимка при его создании.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // Specify metadata at the time that the snapshot is created to specify unique metadata for the snapshot.
        // If no metadata is specified when the snapshot is created, the base blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="copy-snapshots"></a>Копирование моментальных снимков
Операции копирования больших двоичных объектов и моментальных снимков подчиняются следующим правилам.

* Можно скопировать моментальный снимок и заменить им исходный большой двоичный объект. Повысив уровень моментального снимка до базового большого двоичного объекта, можно восстановить более раннюю версию большого двоичного объекта. Моментальный снимок остается, но базовый BLOB-объект перезаписывается с помощью копии моментального снимка, допускающей запись.
* Можно скопировать моментальный снимок в целевой большой двоичный объект с другим именем. Полученный целевой BLOB-объект не станет моментальным снимком, а будет BLOB-объектом, доступным для записи.
* Если копируется исходный большой двоичный объект, его моментальные снимки не копируются. Если большой двоичный объект восстанавливается из его копии, это не влияет на связанные с ним моментальные снимки.
* При создании моментального снимка блочного BLOB-объекта в снимок также копируется его список зафиксированных блоков. Незафиксированные блоки не копируются.

## <a name="specify-an-access-condition"></a>Назначение условий доступа
При вызове [CreateSnapshotAsync][dotnet_CreateSnapshotAsync] можно указать условие доступа, чтобы моментальный снимок создавался только в том случае, если данное условие выполняется. Для этого воспользуйтесь параметром [AccessCondition][dotnet_AccessCondition]. При невыполнении указанного условия моментальный снимок не будет создан, а служба BLOB-объектов вернет код состояния [HTTPStatusCode][dotnet_HTTPStatusCode].PreconditionFailed.

## <a name="delete-snapshots"></a>Удаление моментальных снимков
Большой двоичный объект с моментальными снимками невозможно удалить, пока не будут удалены все его снимки. Можно удалить моментальный снимок отдельно или указать, чтобы при удалении исходного большого двоичного объекта были удалены и все его моментальные снимки. Попытка удалить большой двоичный объект, у которого еще есть моментальные снимки, завершится ошибкой.

В следующем коде показан пример удаления большого двоичного объекта и его моментальных снимков в .NET, где `blockBlob` является объектом типа [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="snapshots-with-azure-premium-storage"></a>Моментальные снимки и хранилище Azure Premium
При использовании моментальных снимков с хранилищем класса "Премиум" применяются такие правила:

* Учетная запись хранения класса Premium поддерживает до 100 моментальных снимков на каждый страничный BLOB-объект. При превышении этого предела операция Snapshot Blob возвращает код ошибки 409 (`SnapshotCountExceeded`).
* При использовании учетной записи хранения класса Premium каждые десять минут можно создавать один моментальный снимок страничного BLOB-объекта. При превышении этого значения операция Snapshot Blob возвращает код ошибки 409 (`SnapshotOperationRateExceeded`).
* Для чтения моментального снимка вы можете воспользоваться операцией Copy Blob, чтобы скопировать его в другой страничный BLOB-объект в пределах учетной записи. При этом в целевом большом двоичном объекте, выбранном для копирования, не должно быть других моментальных снимков. Если у целевого большого двоичного объекта есть моментальные снимки, операция Copy Blob возвращает код ошибки 409 (`SnapshotsPresent`).

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Возврат абсолютного URI для моментального снимка
Этот пример кода C# создает новый моментальный снимок и записывает абсолютный URI для первичного расположения.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Общая информация о том, как моментальные снимки увеличивают плату
Создание моментальных снимков, представляющих собой копии больших двоичных объектов с доступом только для чтения, может привести к дополнительной плате за хранение данных в вашей учетной записи. При проектировании приложения важно учитывать, как эта плата может накапливаться, чтобы минимизировать затраты.

### <a name="important-billing-considerations"></a>Важные вопросы о выставлении счетов
Следующий список включает ключевые пункты, которые следует рассмотреть при создании моментального снимка.

* Плата взимается за уникальные блоки и страницы независимо от того, где они находятся: в большом двоичном объекте или моментальном снимке. Ваша учетная запись не влечет за собой дополнительных затрат на моментальные снимки, связанные с большим двоичным объектом, пока большой двоичный объект, на котором они основаны, не будет обновлен. После изменения базового большого двоичного объекта он начнет отличаться от своих моментальных снимков. Теперь вы будете оплачивать все уникальные блоки и страницы в каждом большом двоичном объекте или моментальном снимке.
* Когда вы заменяете блок в блочном большом двоичном объекте, за этот блок начинает взиматься плата как за уникальный. Это произойдет, даже если у блока тот же идентификатор блока и те же данные, что и в моментальном снимке. Как только блок фиксируется заново, он расходится со всеми своими дубликатами во всех моментальных снимках и вы будете оплачивать его данные. Это же происходит и для страницы в страничном BLOB-объекте, обновляемом идентичными данными.
* При замещении блочного BLOB-объекта путем вызова метода [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] или [UploadFromByteArray][dotnet_UploadFromByteArray] заменяются все блоки в этом большом двоичном объекте. Если с этим большим двоичным объектом связан моментальный снимок, то все блоки в базовом большом двоичном объекте перестанут совпадать с блоками в моментальном снимке, и вы будете оплачивать хранение всех блоков в обоих больших двоичных объектах. Это произойдет, даже если данные базового большого двоичного объекта и моментального снимка останутся идентичными.
* В службе BLOB-объектов Azure нет средств, позволяющих определить, содержат ли два блока идентичные данные. Каждый блок, который был загружен и зафиксирован, обрабатывается как уникальный, даже если он содержит те же данные и имеет тот же идентификатор блока. Так как плата добавляется за уникальные блоки, важно иметь в виду, что обновление большого двоичного объекта, содержащего моментальный снимок, приведет к появлению дополнительных уникальных блоков и дополнительным расходам.

### <a name="minimize-cost-with-snapshot-management"></a>Минимизация затрат с помощью управления моментальными снимками

Рекомендуется управлять моментальными снимками осторожно, чтобы избежать наценок. Выполните приведенные ниже рекомендации, чтобы свести к минимуму затраты на хранение моментальных снимков.

* Удаляйте и повторно создавайте моментальные снимки, связанные с большим двоичным объектом, при обновлении этого объекта, даже если вы обновляете его идентичными данными, если только структура вашего приложения не требует сохранения моментальных снимков. За счет удаления и повторного создания моментальных снимков больших двоичных объектов можно исключить расхождение большого двоичного объекта с моментальными снимками.
* Если необходимо хранить моментальные снимки для больших двоичных объектов, то не вызывайте для их обновления методы [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] и [UploadFromByteArray][dotnet_UploadFromByteArray]. Эти методы заменяют все блоки в большом двоичном объекте, и после этого базовый большой двоичный объект начинает значительно отличаться от своих моментальных снимков. Вместо этого обновляйте наименьшее возможное количество блоков с помощью методов [PutBlock][dotnet_PutBlock] и [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Сценарии выставления счетов за моментальные снимки
В следующих сценариях показано, как добавляется плата за большой двоичный объект и его моментальные снимки.

**Сценарий 1**

В сценарии 1 базовый большой двоичный объект создания моментального снимка не обновлялся, поэтому плата начисляется только за уникальные блоки 1, 2 и 3.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

**Сценарий 2**

В сценарии 2 базовый большой двоичный объект изменился, а моментальный снимок — нет. Блок 3 был обновлен, и, хотя он содержит такие же данные и тот же идентификатор, он отличается от блока 3 в моментальном снимке. В результате плата в учетной записи взимается за четыре блока.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

**Сценарий 3**

В сценарии 3 базовый большой двоичный объект изменился, а моментальный снимок — нет. Блок 3 был заменен блоком 4 в базовом большом двоичном объекте, но моментальный снимок по-прежнему отражает блок 3. В результате плата в учетной записи взимается за четыре блока.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

**Сценарий 4**

В сценарии 4 базовый большой двоичный объект был полностью обновлен и не содержит никаких первоначальных блоков. В результате плата в учетной записи взимается за все восемь уникальных блоков. Этот сценарий может произойти при использовании таких методов обновления, как [UploadFromFile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream] и [UploadFromByteArray][dotnet_UploadFromByteArray], так как эти методы заменяют все содержимое больших двоичных объектов.

![Ресурсы хранилища Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о работе с моментальными снимками диска виртуальной машины см. в статье [Архивация неуправляемых дисков виртуальной машины Azure с помощью добавочных моментальных снимков](../../virtual-machines/windows/incremental-snapshots.md).

* Дополнительные примеры кода, в которых используется хранилище BLOB-объектов, см. на странице с [примерами кода Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Вы можете скачать пример приложения и запустить его или просмотреть код на GitHub.

[dotnet_AccessCondition]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.accesscondition.aspx
[dotnet_CloudBlockBlob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx
[dotnet_CreateSnapshotAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.createsnapshotasync.aspx
[dotnet_HTTPStatusCode]: https://msdn.microsoft.com/library/system.net.httpstatuscode(v=vs.110).aspx
[dotnet_PutBlockList]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblocklist.aspx
[dotnet_PutBlock]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.putblock.aspx
[dotnet_UploadFromByteArray]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfrombytearray.aspx
[dotnet_UploadFromFile]: https://msdn.microsoft.com/library/azure/mt705654.aspx
[dotnet_UploadFromStream]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstream.aspx
[dotnet_UploadText]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.uploadtext.aspx