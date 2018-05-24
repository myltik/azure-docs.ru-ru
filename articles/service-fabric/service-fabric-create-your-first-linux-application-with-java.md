---
title: Создание приложения Java Reliable Actors для Linux в Azure Service Fabric | Документация Майкрософт
description: Узнайте как создавать приложение Java Reliable Actors Service Fabric в течении пяти минут.
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/27/2018
ms.author: ryanwi
ms.openlocfilehash: 01f085a7ec69780fe3558c6892e254ed3a7e2fb0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205694"
---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Создание первого приложения Java Reliable Actors для Linux в Service Fabric
> [!div class="op_single_selector"]
> * [C# для Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java для Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# для Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Используя это краткое руководство, вы создадите первое Java-приложение Azure Service Fabric в среде разработки Linux всего за несколько минут.  В результате вы получите простое приложение Java с одной службой, работающее в кластере локальной разработки.  

## <a name="prerequisites"></a>предварительным требованиям
Перед началом работы установите пакет SDK для Service Fabric, Service Fabric CLI и Yeoman, а также настройте интегрированную среду разработки Java и кластер разработки в своей [среде разработки Linux](service-fabric-get-started-linux.md). Если вы используете Mac OS X, можно [настроить среду разработки для Mac с помощью Docker](service-fabric-get-started-mac.md).

Кроме того, установите [интерфейс командной строки Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Установка и настройка генераторов для Java
Service Fabric предоставляет средства формирования шаблонов, которые позволяют создавать приложения Java в Service Fabric из терминала с помощью генератора шаблонов Yeoman.  Если Yeoman не установлен, см. руководство по [началу работы с Service Fabric в Linux](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables). Выполните следующую команду, чтобы установить шаблон генератора Service Fabric Yeoman для Java.

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

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

## <a name="create-the-application"></a>Создание приложения
Приложение Service Fabric содержит одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. Генератор, установленный в предыдущем разделе, упрощает создание первой службы и добавление последующих.  Кроме того, можно создать, собрать и развернуть Java-приложения Service Fabric с помощью подключаемого модуля для Eclipse. См. статью [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md). В этом руководстве используйте Yeoman для создания приложения с одной службой, которая сохраняет и получает значение счетчика.

1. В окне терминала введите ``yo azuresfjava``.
2. Присвойте имя приложению.
3. Выберите тип первой службы и присвойте ей имя. Для этого примера выберите службу Reliable Actor. Дополнительные сведения о других типах служб см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).
   ![Генератор Service Fabric Yeoman для Java][sf-yeoman]

Если в качестве имени приложения используется HelloWorldActorApplication, а в качестве имени субъекта — HelloWorldActor, мы получим следующее формирование шаблонов.

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
@ActorServiceAttribute(name = "HelloWorldActorService")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class HelloWorldActorImpl extends FabricActor implements HelloWorldActor {
    private Logger logger = Logger.getLogger(this.getClass().getName());

    public HelloWorldActorImpl(FabricActorService actorService, ActorId actorId){
        super(actorService, actorId);
    }

    @Override
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

private static final Logger logger = Logger.getLogger(HelloWorldActorHost.class.getName());
    
public static void main(String[] args) throws Exception {
        
        try {

            ActorRuntime.registerActorAsync(HelloWorldActorImpl.class, (context, actorType) -> new FabricActorService(context, actorType, (a,b)-> new HelloWorldActorImpl(a,b)), Duration.ofSeconds(10));
            Thread.sleep(Long.MAX_VALUE);
        } catch (Exception e) {
            logger.log(Level.SEVERE, "Exception occured", e);
            throw e;
        }
    }
}
```

## <a name="build-the-application"></a>Создание приложения
Шаблоны Service Fabric для Yeoman включают скрипт сборки для [Gradle](https://gradle.org/), который можно использовать для создания приложения из терминала.
Зависимости Java в Service Fabric извлекаются из Maven. Чтобы создавать приложения Java в Service Fabric и работать с ними, сначала установите JDK и Gradle. Если эти компоненты не установлены, см. руководство по [началу работы с Service Fabric в Linux](service-fabric-get-started-linux.md#set-up-java-development).

Используйте следующую команду, чтобы собрать и упаковать приложение:

  ```bash
  cd HelloWorldActorApplication
  gradle
  ```

## <a name="deploy-the-application"></a>Развертывание приложения
Созданное приложение можно развернуть в локальном кластере.

1. Подключитесь к локальному кластеру Service Fabric (кластер должен быть [настроен и запущен](service-fabric-get-started-linux.md#set-up-a-local-cluster)).

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    ./install.sh
    ```

Созданное приложение развертывается так же, как и любое другое приложение Service Fabric. Дополнительные сведения см. в документации по [управлению приложениями Service Fabric с помощью интерфейса командной строки Service Fabric](service-fabric-application-lifecycle-sfctl.md).

Параметры для этих команд можно найти в созданном манифесте в пакете приложения.

Когда приложение будет развернуто, откройте браузер и перейдите к [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) по адресу [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Затем разверните узел **Приложения**. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

## <a name="start-the-test-client-and-perform-a-failover"></a>Запуск тестового клиента и отработка отказа
Проекты субъекта предполагают использование дополнительных средств. Например, для отправки сообщений им требуется другая служба или клиент. Шаблон субъекта включает простой тестовый скрипт, обеспечивающий взаимодействие со службой субъекта.

1. Запустите этот скрипт с помощью служебной программы для отслеживания, чтобы просмотреть выходные данные службы субъекта.  Сценарий теста вызывает для субъекта метод `setCountAsync()`, увеличивающий значение счетчика. Затем для субъекта вызывается метод `getCountAsync()`, чтобы получить новое значение счетчика и вывести его на консоль.

    ```bash
    cd HelloWorldActorTestClient
    watch -n 1 ./testclient.sh
    ```

2. В Service Fabric Explorer найдите узел, в котором размещена первичная реплика службы субъекта. В нашем примере это узел 3. Первичная реплика службы обрабатывает операции чтения и записи.  Затем изменения в состоянии службы реплицируются во вторичные реплики, выполняющиеся на узлах 0 и 1, как показано на снимке экрана ниже.

    ![Поиск первичной реплики в Service Fabric Explorer][sfx-primary]

3. В разделе **Узлы** щелкните найденный узел, а затем выберите в меню "Действия" пункт **Отключить (перезапустить)**. После этого узел, на котором запущена первичная реплика службы, перезапускается и выполняется принудительная отработка отказа с переходом на одну из вторичных реплик, запущенных на другом узле.  Вторичная реплика повышается до первичной, на другом узле создается еще одна вторичная реплика, и первичная реплика начинает выполнять операции чтения и записи. После перезапуска узла обратите внимание на выходные данные тестового клиента: счетчик будет увеличиваться несмотря на отработку отказа.

## <a name="remove-the-application"></a>Удаление приложения
С помощью скрипта для удаления экземпляра приложения (включен в шаблон) отмените регистрацию пакета приложения и удалите его из хранилища образов кластера.

```bash
./uninstall.sh
```

В обозревателе Service Fabric вы увидите, что приложение и его тип больше не отображаются в узле **Приложения**.

## <a name="service-fabric-java-libraries-on-maven"></a>Библиотеки Java для Service Fabric в Maven
Библиотеки Java для Service Fabric размещены в Maven. Чтобы использовать библиотеки Java для Service Fabric из **mavenCentral**, вы можете добавить зависимости в файлы ``pom.xml`` или ``build.gradle`` своих проектов. 

### <a name="actors"></a>Субъекты

Поддержка субъекта Service Fabric Reliable Actors для приложения.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Службы

Поддержка служб Service Fabric Reliable Services в приложении.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Прочее
#### <a name="transport"></a>Транспортировка

Поддержка транспортного уровня для приложения Java в Service Fabric. Вам не нужно явно добавлять эту зависимость в приложения Reliable Actors или Reliable Services, если ваша программа не находится на транспортном уровне.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Поддержка Service Fabric

Поддержка на уровне системы для платформы Service Fabric, которая взаимодействует с собственной средой выполнения Service Fabric. Вам не нужно явно добавлять эту зависимость в приложения Reliable Actors или Reliable Services. Эта зависимость извлекается автоматически из Maven при включении других зависимостей, описанных выше.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="next-steps"></a>Дополнительная информация

* [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md)
* [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Azure Service Fabric command line](service-fabric-cli.md) (Командная строка Azure Service Fabric)
* [Сведения о вариантах поддержки Service Fabric](service-fabric-support.md)
* [Командная строка Azure Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png
