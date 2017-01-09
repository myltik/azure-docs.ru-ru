---
title: "Приступая к работе с Service Fabric Reliable Actors | Документация Майкрософт"
description: "В этом учебнике последовательно описаны этапы создания, отладки и развертывания простой службы на основе субъекта с использованием Service Fabric Reliable Actors."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: d31dc8ab-9760-4619-a641-facb8324c759
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e349d0c76078889b6e41340ee8bb2f599819ba3


---
# <a name="getting-started-with-reliable-actors"></a>Приступая к работе с Reliable Actors
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-actors-get-started.md)
> * [Java в Linux](service-fabric-reliable-actors-get-started-java.md)
> 
> 

В этой статье представлены общие сведения о Reliable Actors в Azure Service Fabric, а также описывается процесс создания и развертывания простого приложения Reliable Actors в Java.

## <a name="installation-and-setup"></a>Установка и настройка
Перед началом установите и настройте на своем компьютере среду разработки Service Fabric.
Дополнительные сведения см. в статье [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md) и [Подготовка среды разработки в Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Основные понятия
Чтобы приступить к работе с субъектами Reliable Actors, необходимо ознакомиться только с несколькими основными понятиями.

* **Служба субъекта**. Субъекты Reliable Actors упаковываются в службы Reliable Services, которые могут быть развернуты в инфраструктуре Service Fabric. Экземпляры субъектов активируются в именованном экземпляре службы.
* **Регистрация субъекта**. Как и в случае Reliable Services, служба Reliable Actor должна быть зарегистрирована в среде выполнения Service Fabric. Кроме того, тип субъекта должен быть зарегистрирован в среде выполнения субъектов.
* **Интерфейс субъекта**. Интерфейс субъекта используется для определения строго типизированного общедоступного интерфейса субъекта. В терминах модели Reliable Actors интерфейс субъекта определяет типы сообщений, которые субъект может понять и обработать. Интерфейс субъекта используется другими субъектами или клиентскими приложениями для отправки (асинхронной) сообщений субъекту. Субъекты Reliable Actors могут реализовывать несколько интерфейсов.
* **Класс ActorProxy**. Класс ActorProxy используется клиентскими приложениями для вызова методов, предоставляемых через интерфейсы субъекта. Класс ActorProxy предоставляет две важные функциональные возможности:
  
  * Разрешение имен. Класс позволяет найти субъект в кластере (определить, на каком узле кластера размещается субъект).
  * Обработка сбоев. Класс может повторно вызывать методы и повторно сопоставить расположение субъекта, например после сбоя, в результате которого субъект перемещается на другой узел в кластере.

Следует упомянуть правила, которые относятся к интерфейсу субъекта:

* методы интерфейса субъекта не могут быть перегружены;
* методы интерфейса субъекта не должны иметь выходных, ссылочных или необязательных параметров.
* универсальные интерфейсы не поддерживаются.

## <a name="create-an-actor-service"></a>Создание службы субъекта
Сначала создайте приложение Service Fabric. В пакет SDK для Service Fabric для Linux входит генератор Yeoman, который позволяет формировать шаблоны для приложения Service Fabric с помощью службы без отслеживания состояния. Сначала выполните следующую команду Yeoman:

```bash
$ yo azuresfjava
```

Следуйте инструкциям по созданию **службы Reliable Actor**. В этом руководстве в качестве имени приложения используется HelloWorldActorApplication, а в качестве имени субъекта — HelloWorldActor. Мы получим следующее формирование шаблонов:

```bash
HelloWorldActorApplication/
├── build.gradle
├── HelloWorldActor
│   ├── build.gradle
│   ├── settings.gradle
│   └── src
│       └── reliableactor
│           ├── HelloWorldActorHost.java
│           └── HelloWorldActorImpl.java
├── HelloWorldActorApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldActorPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   ├── _readme.txt
│       │   └── Settings.xml
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── HelloWorldActorInterface
│   ├── build.gradle
│   └── src
│       └── reliableactor
│           └── HelloWorldActor.java
├── HelloWorldActorTestClient
│   ├── build.gradle
│   ├── settings.gradle
│   ├── src
│   │   └── reliableactor
│   │       └── test
│   │           └── HelloWorldActorTestClient.java
│   └── testclient.sh
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="reliable-actors-basic-building-blocks"></a>Простые стандартные блоки в решении на базе надежных субъектов
Основные понятия, описанные выше, формируют базовые стандартные блоки службы Reliable Actor.

### <a name="actor-interface"></a>Интерфейс субъекта
Он содержит определение интерфейса субъекта. Этот интерфейс определяет контракт субъекта, который совместно используется реализацией субъекта и клиентами, вызывающими субъект. Поэтому обычно имеет смысл определить его в расположении, которое отличается от реализации субъекта и может совместно использоваться несколькими службами и клиентскими приложениями.

`HelloWorldActorInterface/src/reliableactor/HelloWorldActor.java`:

```java
public interface HelloWorldActor extends Actor {
    @Readonly   
    CompletableFuture<Integer> getCountAsync();

    CompletableFuture<?> setCountAsync(int count);
}
```

### <a name="actor-service"></a>Служба субъекта
Служба субъекта содержит реализацию и код регистрации субъекта. Класс субъекта реализует интерфейс субъекта. Здесь субъект выполняет свою работу.

`HelloWorldActor/src/reliableactor/HelloWorldActorImpl`:

```java
@ActorServiceAttribute(name = "HelloWorldActor.HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends ReliableActor implements HelloWorldActor {
    Logger logger = Logger.getLogger(this.getClass().getName());

    protected CompletableFuture<?> onActivateAsync() {
        logger.log(Level.INFO, "onActivateAsync");

        return this.stateManager().tryAddStateAsync("count", 0);
    }

    @Override
    public CompletableFuture<Integer> getCountAsync() {
        logger.log(Level.INFO, "Getting current count value");
        return this.stateManager().getStateAsync("count");
    }

    @Override
    public CompletableFuture<?> setCountAsync(int count) {
        logger.log(Level.INFO, "Setting current count value {0}", count);
        return this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value);
    }
}
```

### <a name="actor-registration"></a>Регистрация субъекта
Служба субъекта должна быть зарегистрирована в среде выполнения Service Fabric с указанием типа службы. Чтобы служба субъекта выполняла экземпляры вашего субъекта, его тип также нужно зарегистрировать в службе субъекта. Метод регистрации `ActorRuntime` выполняет это действие для субъектов.

`HelloWorldActor/src/reliableactor/HelloWorldActorHost`:

```java
public class HelloWorldActorHost {

    public static void main(String[] args) throws Exception {

        try {
            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new ActorServiceImpl(context, actorType, ()-> new HelloWorldActorImpl()), Duration.ofSeconds(10));

            Thread.sleep(Long.MAX_VALUE);

        } catch (Exception e) {
            e.printStackTrace();
            throw e;
        }
    }
}
```

### <a name="test-client"></a>Тестовый клиент
Тестовый клиент — это простое тестовое клиентское приложение, которое можно запустить отдельно от приложения Service Fabric для проверки службы субъекта. В нем ActorProxy можно использовать, чтобы активировать экземпляры субъекта и установить между ними связь. Он не развертывается с вашей службой.

### <a name="the-application"></a>Приложение
Приложение упаковывает службу субъекта и другие службы в пакет для последующего развертывания. Оно содержит файл *ApplicationManifest.xml* и заполнители для пакета службы субъекта.

## <a name="run-the-application"></a>Выполнение приложения
Формирование шаблонов Yeoman содержит скрипт Gradle, используемый для создания приложения, и bash-скрипты, используемые для развертывания и отмены развертывания приложения. Чтобы запустить приложение, сначала его нужно создать с помощью следующего скрипта Gradle:

```bash
$ gradle
```

Этот скрипт создаст пакет приложения Service Fabric, который можно развернуть с помощью интерфейса командной строки Azure Service Fabric. Скрипт install.sh содержит необходимые команды Azure CLI, используемые для развертывания пакета приложения. Чтобы развернуть пакет, просто выполните скрипт install.sh.

```bask
$ ./install.sh
```



<!--HONumber=Nov16_HO3-->


