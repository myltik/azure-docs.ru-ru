---
title: "Приступая к работе со службами Reliable Services | Документация Майкрософт"
description: "Вводные сведения о создании приложения Service Fabric Microsoft Azure со службами с отслеживанием состояния и без него."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 4450ad62a9b05ac4c963ae3271590f9431b782ed
ms.openlocfilehash: 2a2378dbeb5e7994039291deffd35cb04bf8057c


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
Сначала создайте приложение Service Fabric. В пакет SDK для Service Fabric для Linux входит генератор Yeoman, который позволяет формировать шаблоны для приложения Service Fabric с помощью службы без отслеживания состояния. Сначала выполните следующую команду Yeoman:

```bash
$ yo azuresfjava
```

Следуйте инструкциям по созданию **надежной службы без отслеживания состояния**. В этом руководстве в качестве имени приложения используется HelloWorldApplication, а в качестве имени службы — HelloWorld. Результат будет включать в себя каталоги для `HelloWorldApplication` и `HelloWorld`.

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
protected CompletableFuture<?> runAsync() {
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

В этом учебнике рассматривается метод точки входа `runAsync()` . Используя его, вы можете сразу же начать выполнение кода.

### <a name="runasync"></a>RunAsync
Платформа вызывает этот метод, когда экземпляр службы размещен и готов к выполнению. Этот цикл открытия и закрытия экземпляра службы может выполняться много раз за все время существования службы. Это может происходить по ряду причин.

* Система перемещает экземпляры служб для распределения ресурсов.
* В коде возникают ошибки.
* Выполняется обновление приложения или системы.
* Возникает аппаратный сбой.

Этой оркестрацией управляет Service Fabric для сохранения высокой доступности и правильного баланса службы.

#### <a name="cancellation"></a>Отмена
Крайне важно, чтобы код в `runAsync()` смог остановить выполнение при получении уведомления от Service Fabric. Класс `CompletableFuture`, возвращенный из `runAsync()`, отменяется, когда Service Fabric отправляет в службу запрос на остановку выполнения. Следующий пример демонстрирует, как обрабатывать событие отмены. 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);

        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });

        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });

        return completableFuture;
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
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Выполнение приложения
Формирование шаблонов Yeoman содержит скрипт Gradle, используемый для создания приложения, и bash-скрипты, используемые для развертывания и отмены развертывания приложения. Чтобы запустить приложение, сначала его нужно создать с помощью следующего скрипта Gradle:

```bash
$ gradle
```

Этот скрипт создаст пакет приложения Service Fabric, который можно развернуть с помощью интерфейса командной строки Azure Service Fabric. Скрипт install.sh содержит необходимые команды Azure CLI, используемые для развертывания пакета приложения. Чтобы развернуть пакет, просто выполните скрипт install.sh.

```bask
$ ./install.sh
```



<!--HONumber=Dec16_HO2-->


