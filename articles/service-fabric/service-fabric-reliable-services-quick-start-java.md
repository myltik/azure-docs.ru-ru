---
title: "Создание первой надежной микрослужбы Azure на языке Java | Документация Майкрософт"
description: "Вводные сведения о создании приложения Service Fabric Microsoft Azure со службами с отслеживанием состояния и без него."
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: cf8f717d5343ae27faefdc10f81b4feaccaa53b9
ms.openlocfilehash: 5a29d6838af7f3952ad96158e5962b17c0f4cb6b
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-reliable-services"></a>Приступая к работе с надежными службами
> [!div class="op_single_selector"]
> * [C# в Windows](service-fabric-reliable-services-quick-start.md)
> * [Java в Linux](service-fabric-reliable-services-quick-start-java.md)
>
>

В этой статье представлены общие сведения о Reliable Services в Azure Service Fabric, а также описывается процесс создания и развертывания простого приложения Reliable Services, написанного на языке Java. В этом видеоролике от Академии Microsoft Virtual Academy также показано, как создать службу Reliable Services без отслеживания состояния: <center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>Установка и настройка
Перед началом установите и настройте на своем компьютере среду разработки Service Fabric.
Дополнительные сведения см. в статье [Настройка среды разработки для Mac OS X](service-fabric-get-started-mac.md) и [Подготовка среды разработки в Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Основные понятия
Чтобы приступить к работе с Reliable Services, необходимо ознакомиться только с несколькими основными понятиями.

* **Тип службы**. Это ваша реализация службы. Ее определяет создаваемый вами класс, который расширяет `StatelessService`, и любой другой код или зависимости, используемые в нем, а также имя и номер версии.
* **Именованный экземпляр службы**. Для запуска службы создаются именованные экземпляры вашего типа службы, что во многом похоже на создание экземпляров объектов типа класса. Экземпляры службы фактически являются создаваемыми экземплярами объектов написанного вами класса службы.
* **Узел службы**. Создаваемые экземпляры службы должны выполняться на узле. Узел службы — это просто процесс, в котором можно запускать экземпляры вашей службы.
* **Регистрации службы**. Регистрация объединяет все элементы. Тип службы должен быть зарегистрирован в среде выполнения Service Fabric на узле службы, что позволит Service Fabric создавать экземпляры службы для запуска.  

## <a name="create-a-stateless-service"></a>Создание службы без отслеживания состояния
Для начала создайте приложение Service Fabric. В пакет SDK для Service Fabric для Linux входит генератор Yeoman, который позволяет формировать шаблоны для приложения Service Fabric с помощью службы без отслеживания состояния. Сначала выполните следующую команду Yeoman:

```bash
$ yo azuresfjava
```

Следуйте инструкциям по созданию **надежной службы без отслеживания состояния**. В этом руководстве в качестве имени приложения используется HelloWorldApplication, а в качестве имени службы — HelloWorld. Результат включает в себя каталоги для `HelloWorldApplication` и `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Реализация службы
Откройте файл **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Этот класс определяет тип службы и может выполнить любой код. API службы предоставляет две точки входа для кода.

* Вызывается метод `runAsync()` с открытой точкой входа, в котором можно начать выполнение любой рабочей нагрузки, например длительных вычислений.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* Точка входа связи, где можно подключить любой стек связи по выбору. С ее помощью вы можете получать запросы от пользователей и других служб.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

В этом руководстве рассматривается метод точки входа `runAsync()`. Используя его, вы можете сразу же начать выполнение кода.

### <a name="runasync"></a>RunAsync
Платформа вызывает этот метод, когда экземпляр службы размещен и готов к выполнению. Для службы без отслеживания состояния это означает, что экземпляр службы открыт. Токен отмены предоставляется для координации закрытия экземпляра вашей службы. В Service Fabric этот цикл открытия и закрытия экземпляра службы может выполняться много раз за все время существования службы. Это может происходить по ряду причин.

* Система перемещает экземпляры служб для распределения ресурсов.
* В коде возникают ошибки.
* Выполняется обновление приложения или системы.
* Возникает аппаратный сбой.

Этой оркестрацией управляет Service Fabric для сохранения высокой доступности и правильного баланса службы.

Реализация `runAsync()` не должна использовать синхронную блокировку. Реализация runAsync должна вернуть CompletableFuture, чтобы среда выполнения могла продолжить операции. Если для вашей рабочей нагрузки необходимо реализовать длительную задачу, то она должна выполняться в пределах CompletableFuture.

#### <a name="cancellation"></a>Отмена
Отмена рабочей нагрузки является совместным действием, координированным предоставленным токеном отмены. Чтобы продолжать работу, система должна дождаться завершения задачи (успешного завершения, отмены или завершения ошибкой). Когда система запрашивает отмену задачи, важно как можно быстрее подтвердить токен отмены, завершить все выполняющиеся операции и выйти из `runAsync()` . Следующий пример демонстрирует, как обрабатывать событие отмены.

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
```

### <a name="service-registration"></a>Регистрация службы
Типы службы должны быть зарегистрированы в среде выполнения Service Fabric. Тип службы определен в `ServiceManifest.xml` и в классе службы, реализующем `StatelessService`. Регистрация службы выполняется в главной точке входа в рамках процесса. В этом примере главная точка входа в рамках процесса — это `HelloWorldServiceHost.java`.

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Выполнение приложения
Формирование шаблонов Yeoman включает в себя сценарий Gradle, используемый для создания приложения, и сценарии bash, используемые для развертывания и отмены развертывания приложения. Чтобы запустить приложение, сначала его нужно создать с помощью следующего скрипта Gradle:

```bash
$ gradle
```

Этот сценарий создает пакет приложения Service Fabric, который можно развернуть с помощью интерфейса командной строки Azure Service Fabric. Скрипт install.sh содержит необходимые команды Azure CLI, используемые для развертывания пакета приложения. Чтобы развернуть пакет, просто выполните сценарий install.sh.

```bash
$ ./install.sh
```

