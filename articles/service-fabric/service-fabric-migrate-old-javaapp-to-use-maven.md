---
title: Перенос из пакета SDK для Java в Maven — обновление старых приложений Java в Azure Service Fabric для использования Maven | Документация Майкрософт
description: Обновите старые приложения Java, которые используют пакет SDK для Java Service Fabric, чтобы получить зависимости Java в Service Fabric из Maven. По завершении этой настройки ваши старые приложения Java смогут выполнять сборку.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: timlt
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: rapatchi
ms.openlocfilehash: 178d49a9481cec20da3e9418d9c104684f303267
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206661"
---
# <a name="update-your-previous-java-service-fabric-application-to-fetch-java-libraries-from-maven"></a>Обновление старого приложения Java в Service Fabric для получения библиотек Java из Maven
Мы недавно разместили двоичные файлы Java Service Fabric из пакета SDK для Java Service Fabric в Maven. Теперь вы можете использовать **mavencentral** для получения последних зависимостей Service Fabric Java. С помощью этого краткого руководства вы обновите существующие приложения Java, созданные ранее для использования с пакетом SDK для Java Service Fabric. Чтобы обеспечить совместимость со сборкой Maven, будет использован шаблон Yeoman или Eclipse.

## <a name="prerequisites"></a>предварительным требованиям
1. Сначала удалите существующий пакет SDK для Java.

  ```bash
  sudo dpkg -r servicefabricsdkjava
  ```
2. Установите последнюю версию интерфейса командной строки Service Fabric, следуя инструкциям [здесь](service-fabric-cli.md).

3. Чтобы собрать приложения Java в Service Fabric и работать с ними, сначала установите JDK 1.8 и Gradle. Для установки JDK 1.8 (openjdk-8-jdk) и Gradle (если они еще не установлены) используйте следующие команды:

 ```bash
 sudo apt-get install openjdk-8-jdk-headless
 sudo apt-get install gradle
 ```
4. Обновите скрипты установки и удаления приложения для использования нового интерфейса командной строки Service Fabric, следуя инструкциям [здесь](service-fabric-application-lifecycle-sfctl.md). В качестве образца используйте наши [примеры](https://github.com/Azure-Samples/service-fabric-java-getting-started) для начала работы.

>[!TIP]
> После удаления пакета SDK для Java Service Fabric Yeoman не будет работать. Чтобы восстановить и запустить генератор шаблонов Java Yeoman Service Fabric, выполните предварительные требования, описанные [здесь](service-fabric-create-your-first-linux-application-with-java.md).

## <a name="service-fabric-java-libraries-on-maven"></a>Библиотеки Java для Service Fabric в Maven
Библиотеки Java для Service Fabric размещены в Maven. Чтобы использовать библиотеки Java для Service Fabric из **mavenCentral**, вы можете добавить зависимости в файлы ``pom.xml`` или ``build.gradle`` своих проектов.

### <a name="actors"></a>Субъекты

Поддержка субъекта Service Fabric Reliable Actors для приложения.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.10.0'
  }
  ```

### <a name="services"></a>Службы

Поддержка службы без отслеживания состояния Service Fabric для приложения.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.10.0'
  }
  ```

### <a name="others"></a>Прочее
#### <a name="transport"></a>Транспортировка

Поддержка транспортного уровня для приложения Java в Service Fabric. Вам не нужно явно добавлять эту зависимость в приложения Reliable Actors или Reliable Services, если ваша программа не находится на транспортном уровне.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.10.0'
  }
  ```

#### <a name="fabric-support"></a>Поддержка Service Fabric

Поддержка на уровне системы для платформы Service Fabric, которая взаимодействует с собственной средой выполнения Service Fabric. Вам не нужно явно добавлять эту зависимость в приложения Reliable Actors или Reliable Services. Эта зависимость извлекается автоматически из Maven при включении других зависимостей, описанных выше.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.10.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.10.0'
  }
  ```


## <a name="migrating-service-fabric-stateless-service"></a>Миграция службы без отслеживания состояния Service Fabric

Чтобы собрать существующую службу Java без отслеживания состояния в Service Fabric с помощью зависимостей Service Fabric, полученных из Maven, необходимо обновить файл ``build.gradle`` в службе. Ранее файл выглядел так:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':Interface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
}
.
.
.
task copyDeps <<{
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('libj*.so')
    }
}
```
Теперь для получения зависимостей из Maven **обновленный** файл ``build.gradle`` будет содержать следующие части:
```
repositories {
        mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':Interface')
    azuresf ('com.microsoft.servicefabric:sf-services-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'statelessservice.MyStatelessServiceHost',
                "Class-Path": mpath)
    baseName "MyStateless"
    destinationDir = file('./../MyStatelessApplication/MyStatelessPkg/Code')
   }
}
.
.
.
task copyDeps <<{
    copy {
        from("lib/")
        into("./../MyStatelessApplication/MyStatelessPkg/Code/lib")
        include('*')
    }
}
```
Чтобы понять, как скрипт сборки будет выглядеть для службы Java без отслеживания состояния в Service Fabric, см. любой пример в нашей подборке примеров для начала работы. Например, это файл [build.gradle](https://github.com/Azure-Samples/service-fabric-java-getting-started/blob/master/Services/EchoServer/EchoServer1.0/EchoServerService/build.gradle) для примера EchoServer.

## <a name="migrating-service-fabric-actor-service"></a>Миграция службы субъекта в Service Fabric

Чтобы собрать существующее приложение Java субъекта в Service Fabric с помощью зависимостей Service Fabric, полученных из Maven, необходимо обновить файл ``build.gradle`` в пакете интерфейса и в пакете службы. При наличии пакета TestClient его также необходимо обновить. То есть для субъекта ``Myactor`` вам нужно обновить следующие части:
```
./Myactor/build.gradle
./MyactorInterface/build.gradle
./MyactorTestClient/build.gradle
```

#### <a name="updating-build-script-for-the-interface-project"></a>Обновление скрипта построения для проекта интерфейса

Ранее файл выглядел так:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
}
.
.
```
Теперь для получения зависимостей из Maven **обновленный** файл ``build.gradle`` будет содержать следующие части:
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
```

#### <a name="updating-build-script-for-the-actor-project"></a>Обновление скрипта построения для проекта субъекта

Ранее файл выглядел так:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
    compile project(':MyactorInterface')
}
.
.
.
jar {
    manifest {
    attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
      baseName "myactor"
    destinationDir = file('./../myjavaapp/MyactorPkg/Code')
    }
}
.
.
.
task copyDeps<< {
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
    copy {
        from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('libj*.so')
    }
    copy {
        from("../MyactorInterface/out/lib")
        into("./../myjavaapp/MyactorPkg/Code/lib")
        include('*.jar')
    }
}
```
Теперь для получения зависимостей из Maven **обновленный** файл ``build.gradle`` будет содержать следующие части:
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar {
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
        attributes(
                'Main-Class': 'reliableactor.MyactorHost',
                "Class-Path": mpath)
    baseName "myactor"
    destinationDir = file('../myjavaapp/MyactorPkg/Code')}
 }
.
.
.
task copyDeps<< {
      copy {
              from("lib/")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*')
      }
      copy {
              from("../MyactorInterface/out/lib")
              into("../myjavaapp/MyactorPkg/Code/lib")
              include('*.jar')
      }
}
```

#### <a name="updating-build-script-for-the-test-client-project"></a>Обновление скрипта построения для проекта тестового клиента

Эти изменения похожи на изменения, описанные в предыдущем разделе, посвященном проекту субъекта. Ранее скрипт Gradle выглядел так:
```
dependencies {
    compile fileTree(dir: '/opt/microsoft/sdk/servicefabric/java/packages/lib', include: ['*.jar'])
      compile project(':MyactorInterface')
}
.
.
.
jar
{
    manifest {
    attributes(
        'Main-Class': 'reliableactor.test.MyactorTestClient',
        "Class-Path": configurations.compile.collect { 'lib/' + it.getName() }.join(' '))
    }
    baseName "myactor-test"
  destinationDir = file('out/lib')
}
.
.
.
task copyDeps<< {
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
        copy {
                from("/opt/microsoft/sdk/servicefabric/java/packages/lib")
                into("./out/lib/lib")
                include('libj*.so')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```
Теперь для получения зависимостей из Maven **обновленный** файл ``build.gradle`` будет содержать следующие части:
```
repositories {
    mavenCentral()
}

configurations {
    azuresf
}

dependencies {
    compile project(':MyactorInterface')
    azuresf ('com.microsoft.servicefabric:sf-actors-preview:0.10.0')
    compile fileTree(dir: 'lib', include: '*.jar')
}

task explodeDeps(type: Copy, dependsOn:configurations.azuresf) { task ->
    configurations.azuresf.filter { it.toString().contains("native-preview") }.each{
        from zipTree(it)
    }
    configurations.azuresf.filter { !it.toString().contains("native-preview") }.each {
        from it
    }
    into "lib"
    include "libj*.so", "*.jar"
}

compileJava.dependsOn(explodeDeps)
.
.
.
jar
{
    manifest {
        def mpath = configurations.compile.collect {'lib/'+it.getName()}.join (' ')
        mpath = mpath + ' ' + configurations.azuresf.collect {'lib/'+it.getName()}.join (' ')
    attributes(
                'Main-Class': 'reliableactor.test.MyactorTestClient',
                "Class-Path": mpath)
    baseName "myactor-test"
    destinationDir = file('./out/lib')
        }
}
.
.
.
task copyDeps<< {
        copy {
                from("lib/")
                into("./out/lib/lib")
                include('*')
        }
        copy {
                from("../MyactorInterface/out/lib")
                into("./out/lib/lib")
                include('*.jar')
        }
}
```

## <a name="next-steps"></a>Дополнительная информация

* [Создание первого Java-приложения Service Fabric в Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java](service-fabric-get-started-eclipse.md)
* [Azure Service Fabric command line](service-fabric-cli.md) (Командная строка Azure Service Fabric)
