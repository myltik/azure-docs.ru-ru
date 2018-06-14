---
title: Сериализация объектов надежной коллекции в Azure Service Fabric | Документация Майкрософт
description: Сериализация объектов надежных коллекций в Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 9d35374c-2d75-4856-b776-e59284641956
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/8/2017
ms.author: mcoskun
ms.openlocfilehash: b02d8924749abb0e2fe815b555d55767bf1e5cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207671"
---
# <a name="reliable-collection-object-serialization-in-azure-service-fabric"></a>Сериализация объектов надежной коллекции в Azure Service Fabric
Надежные коллекции реплицируют и сохраняют свои элементы, чтобы обеспечить надежность их работы в случае сбоев машин и отключения электроэнергии.
Для репликации и сохранения элементов надежным коллекциям необходимо сериализовать их.

Надежные коллекции получают соответствующий сериализатор для данного типа из диспетчера надежных состояний.
Диспетчер надежных состояний содержит встроенные сериализаторы и позволяет регистрировать пользовательские сериализаторы для данного типа.

## <a name="built-in-serializers"></a>Встроенные сериализаторы

Диспетчер надежных состояний включает встроенный сериализатор для некоторых распространенных типов, поэтому они по умолчанию могут эффективно сериализоваться. Для других типов диспетчер надежных состояний использует [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer(v=vs.110).aspx).
Встроенные сериализаторы эффективнее, так как их типы не могут измениться и им не нужно включать сведения о типе, например его имя.

Диспетчер надежных состояний имеет встроенный сериализатор для следующих типов: 
- Guid
- bool
- byte
- sbyte
- byte[]
- char;
- строка
- decimal
- Double
- float;
- int
- uint
- длинное целое число
- ulong
- short
- ushort

## <a name="custom-serialization"></a>Настраиваемая сериализация

Настраиваемые сериализаторы обычно используются для повышения производительности или шифрования данных при передаче по сети и хранении на диске. Помимо прочего, настраиваемые сериализаторы часто являются более эффективными, чем универсальные сериализаторы, так как вам не нужно сериализовать информацию о типе. 

[IReliableStateManager.TryAddStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestatemanager.tryaddstateserializer--1?Microsoft_ServiceFabric_Data_IReliableStateManager_TryAddStateSerializer__1_Microsoft_ServiceFabric_Data_IStateSerializer___0__) используется для регистрации настраиваемого сериализатора для данного типа T. Эта регистрация должна произойти при построении StatefulServiceBase, чтобы гарантировать, что до начала восстановления все надежные коллекции будут иметь доступ к соответствующим сериализаторам для чтения сохраненных данных.

```csharp
public StatefulBackendService(StatefulServiceContext context)
  : base(context)
  {
    if (!this.StateManager.TryAddStateSerializer(new OrderKeySerializer()))
    {
      throw new InvalidOperationException("Failed to set OrderKey custom serializer");
    }
  }
```

> [!NOTE]
> Настраиваемые сериализаторы имеют приоритет над встроенными сериализаторами. Например, когда регистрируется настраиваемый сериализатор для типа int, он используется для сериализации целых чисел вместо встроенного сериализатора для int.

### <a name="how-to-implement-a-custom-serializer"></a>Реализация настраиваемого сериализатора

Настраиваемый сериализатор должен реализовать интерфейс [IStateSerializer<T>](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.istateserializer-1).

> [!NOTE]
> IStateSerializer<T> включает перегрузку Write и Read, которая принимает дополнительное базовое значение, называемое T. Этот API предназначен для разностной сериализации. Сейчас функция разностной сериализации не предоставляется. Следовательно эти две перегрузки не вызываются, пока не будет предоставлена и разрешена разностная сериализация.

Ниже приведен пример настраиваемого типа под названием OrderKey, который содержит четыре свойства.

```csharp
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }

    public short District { get; set; }

    public int Customer { get; set; }

    public long Order { get; set; }

    #region Object Overrides for GetHashCode, CompareTo and Equals
    #endregion
}
```

Ниже приведен пример реализации IStateSerializer<OrderKey>.
Обратите внимание, что перегрузки Read и Write, которые принимают базовое значение, вызывают соответствующую перегрузку для прямой совместимости.

```csharp
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
  OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
  {
      var value = new OrderKey();
      value.Warehouse = reader.ReadByte();
      value.District = reader.ReadInt16();
      value.Customer = reader.ReadInt32();
      value.Order = reader.ReadInt64();

      return value;
  }

  void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
  {
      writer.Write(value.Warehouse);
      writer.Write(value.District);
      writer.Write(value.Customer);
      writer.Write(value.Order);
  }
  
  // Read overload for differential de-serialization
  OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
  {
      return ((IStateSerializer<OrderKey>)this).Read(reader);
  }

  // Write overload for differential serialization
  void IStateSerializer<OrderKey>.Write(OrderKey baseValue, OrderKey newValue, BinaryWriter writer)
  {
      ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
  }
}
```

## <a name="upgradability"></a>Возможность обновления
При [последовательном обновлении приложения](service-fabric-application-upgrade.md)обновление применяется к подмножеству узлов, переходя от одного домена обновления к другому. Во время этого процесса некоторые домены обновления будут использовать более раннюю версию приложения, чем другие. В ходе развертывания новая версия вашего приложения должна иметь способность считывать старые версии данных, а старая версия приложения — новую версию данных. Если формат данных не обладает прямой и обратной совместимостью, обновление может завершиться неудачей либо данные могут быть утрачены.

При использовании встроенного сериализатора вам не нужно беспокоиться о совместимости.
Однако при использовании настраиваемого сериализатора или DataContractSerializer данные должны быть бесконечно прямо или обратно совместимы.
Иными словами, каждая версия сериализатора должна иметь возможность сериализовать и десериализовать тип любой версии.

Пользователи контракта данных должны следовать строго заданным правилам управления версиями при добавлении, удалении или изменении полей. Кроме того, контракт данных обладает поддержкой обработки неизвестных полей, участвующих в процессе сериализации и десериализации, а также наследования классов. Дополнительные сведения см. в разделе [Использование контракта данных](https://msdn.microsoft.com/library/ms733127.aspx).

Пользователи настраиваемого сериализатора должны придерживаться рекомендаций в отношении сериализатора, который они используют, чтобы обеспечить его прямую и обратную совместимость.
Распространенный способ обеспечения поддержки всех версий — добавление сведений о размере в начале и добавление только необязательных свойств.
Таким образом, каждая версия считает только то, что сможет, и перепрыгнет через оставшуюся часть потока.

## <a name="next-steps"></a>Дополнительная информация
  * [Влияние сериализации данных на обновление приложений](service-fabric-application-upgrade-data-serialization.md)
  * [Справочник разработчика по надежным коллекциям](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
  * [Руководство по обновлению приложений Service Fabric с помощью Visual Studio](service-fabric-application-upgrade-tutorial.md) поможет вам выполнить поэтапное обновление приложения с помощью Visual Studio.
  * [Обновление приложения с помощью PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) поможет вам выполнить обновление приложения с помощью PowerShell.
  * Управление обновлениями приложения осуществляется с помощью [параметров обновления](service-fabric-application-upgrade-parameters.md).
  * [Дополнительные разделы](service-fabric-application-upgrade-advanced.md)содержат сведения о работе с расширенными функциями при обновлении приложения.
  * Сведения об устранении распространенных проблем при обновлении приложений см. в статье [Устранение неполадок при обновлениях приложений](service-fabric-application-upgrade-troubleshooting.md).
