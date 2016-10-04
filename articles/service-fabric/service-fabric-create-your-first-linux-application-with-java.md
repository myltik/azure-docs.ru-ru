<properties
   pageTitle="Создание первого приложения Service Fabric в Linux с помощью Java | Microsoft Azure"
   description="Создание и развертывание приложения Service Fabric с помощью Java"
   services="service-fabric"
   documentationCenter="java"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="seanmck"/>


# Создание первого приложения Azure Service Fabric

> [AZURE.SELECTOR]
- [C Sharp](service-fabric-create-your-first-application-in-visual-studio.md)
- [Java](service-fabric-create-your-first-linux-application-with-java.md)

Service Fabric предоставляет пакеты SDK для создания служб в среде Linux с помощью .NET Core и Java. В этом руководстве мы создадим приложение для Linux. Также мы создадим службу с использованием Java.

## Предварительные требования

Перед началом работы [настройте среду разработки Linux](service-fabric-get-started-linux.md). Если вы используете Mac OS X, вы можете [настроить универсальную среду Linux на виртуальной машине с помощью Vagrant](service-fabric-get-started-mac.md).

## Создание приложения

Приложение Service Fabric может содержать одну или несколько служб, каждая из которых выполняет определенную роль в работе приложения. Пакет Service Fabric SDK для Linux включает генератор [Yeoman](http://yeoman.io/), который упрощает создание первой службы и добавление последующих. Итак, с помощью Yeoman мы создадим приложение с одной службой.

1. Введите в терминале **yo azuresfjava**.

2. Присвойте имя приложению.

3. Выберите тип первой службы и присвойте ей имя. В этом примере мы выберем службу Reliable Actor.

  ![Генератор Service Fabric Yeoman для Java][sf-yeoman]

>[AZURE.NOTE] Дополнительные сведения о доступных возможностях см. в статье [Общие сведения о модели программирования Service Fabric](service-fabric-choose-framework.md).

## Создание приложения

Шаблоны Service Fabric для Yeoman включают скрипт сборки для [Gradle](https://gradle.org/), который можно использовать для создания приложения из терминала.

  ```bash
  gradle
  ```

## Развертывание приложения

Созданное приложение можно развернуть в локальном кластере с помощью интерфейса командной строки Azure.

1. Подключитесь к удаленному кластеру Service Fabric.

    ```bash
    azuresfcli servicefabric cluster connect
    ```

2. С помощью скрипта установки (включен в шаблон) скопируйте пакет приложения в хранилище образов кластера, зарегистрируйте тип приложения и создайте экземпляр приложения.

    ```bash
    cd myapp
    ./install.sh
    ```

3. Откройте в браузере Service Fabric Explorer и перейдите по адресу http://localhost:19080/Explorer (если используется Vagrant в Mac OS X, замените localhost частным IP-адресом ВМ).

4. Разверните узел приложения. Вы увидите одну запись для типа приложения и еще одну — для первого экземпляра этого типа.

## Запуск тестового клиента и отработка отказа

Проекты субъекта предполагают использование дополнительных средств. Например, для отправки сообщений им требуется другая служба или клиент. Шаблон субъекта включает простой тестовый скрипт, обеспечивающий взаимодействие со службой субъекта.

1. Запустите этот скрипт с помощью служебной программы для отслеживания, чтобы просмотреть выходные данные службы субъекта.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. В Service Fabric Explorer найдите узел, в котором размещена первичная реплика службы субъекта. В нашем примере это узел 3.

    ![Поиск первичной реплики в Service Fabric Explorer][sfx-primary]

3. Щелкните найденный узел, а затем щелкните в меню "Действия" пункт **Отключить (перезапустить)**. Будет перезапущен один из пяти узлов в локальном кластере и выполнен принудительный переход на одну из вторичных реплик, запущенных на другом узле. После этого обратите внимание на выходные данные тестового клиента: счетчик будет увеличиваться несмотря на отработку отказа.

## Сборка и развертывание приложения с помощью подключаемого модуля Eclipse Neon

Если вы установили подключаемый модуль службы для Eclipse Neon, его можно использовать для создания, сборки и развертывания приложений Service Fabric на базе Java.

### Создание приложения

Подключаемый модуль Service Fabric можно добавить в Eclipse.

1. В Eclipse последовательно выберите **File > Other > Service Fabric** (Файл > Другое > Service Fabric). Появится набор параметров, включающий субъекты и контейнеры.

    ![Шаблоны Service Fabric в Eclipse][sf-eclipse-templates]

2. В нашем примере используется служба без отслеживания состояния.

3. Вам будет предложено подтвердить использование перспективы Service Fabric для оптимизации Eclipse при использовании в проектах Service Fabric. Щелкните "Yes" (Да).

### Развертывание приложения

Шаблоны Service Fabric включают набор задач Gradle для сборки и развертывания приложений, которые можно активировать с помощью Eclipse.

1. Выберите **Run > Run Configurations** (Запуск > Конфигурации запуска).

2. Разверните **проект Gradle** и выберите **ServiceFabricDeployer**.

3. Щелкните **Выполнить**.

Приложение будет собрано и развернуто через несколько секунд. Его состояние можно отслеживать в Service Fabric Explorer.

## Дальнейшие действия

- [Общие сведения о надежных субъектах Service Fabric](service-fabric-reliable-actors-introduction.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

<!---HONumber=AcomDC_0928_2016-->