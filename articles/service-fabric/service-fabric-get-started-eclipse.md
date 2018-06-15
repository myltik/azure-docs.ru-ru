---
title: Подключаемый модуль Azure Service Fabric для Eclipse | Документация Майкрософт
description: Начало работы с подключаемым модулем Service Fabric для Eclipse.
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
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: 2fbae584c09fd83f2233895d31c1013acd06ae3b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643004"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Подключаемый модуль Service Fabric для разработки приложений Eclipse на Java
Eclipse является одной из наиболее часто используемых интегрированных сред разработки (IDE) для разработчиков Java. В этой статье описывается, как настроить среду разработки Eclipse для работы с Azure Service Fabric. Вы узнаете, как установить подключаемый модуль Service Fabric, создать приложение Service Fabric и развернуть его в локальном или удаленном кластере Service Fabric в Eclipse. 

> [!NOTE]
> Подключаемый модуль Eclipse сейчас не поддерживается в Windows. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Установка и обновление подключаемого модуля Service Fabric в Eclipse
Вы можете установить подключаемый модуль Service Fabric в Eclipse. Он позволяет упростить процесс создания и развертывания служб Java.

> [!IMPORTANT]
> Для подключаемого модуля Service Fabric требуется Eclipse Neon или более поздняя версия. Чтобы проверить версию Eclipse, выполните инструкции под этим примечанием. Если у вас установлена более ранняя версия Eclipse, скачайте более новые версии с [сайта Eclipse](https://www.eclipse.org). Не рекомендуется устанавливать Eclipse поверх существующей установки (перезаписывать). Вы можете удалить ее перед запуском установщика или установить новую версию в другом каталоге. 
> 
> В Ubuntu мы рекомендуем установить Eclipse непосредственно с сайта, а не с помощью установщика пакета (`apt` или `apt-get`). Так вы получите последнюю версию Eclipse. 

Установите Eclipse Neon или более поздней версии с [сайта Eclipse](https://www.eclipse.org).  Также установите Buildship 2.2.1 или более поздней версии (подключаемый модуль Service Fabric не совместим с более ранними версиями Buildship):
-   Чтобы проверить версии установленных компонентов, в Eclipse выберите **Help (Справка)** > **About Eclipse (Сведения об Eclipse)** > **Installation Details (Сведения об установке)**.
-   Сведения об обновлении Buildship см. на странице [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship. Подключаемые модули Eclipse для Gradle).
-   Чтобы проверить и установить обновления для Eclipse, выберите **Help** > **Check for Updates** (Справка > Проверить обновления).

Установите подключаемый модуль Service Fabric. В Eclipse щелкните **Help (Справка)** > **Install New Software (Установка нового программного обеспечения)**.
1. В поле **Work with** (Работа с) введите **http://dl.microsoft.com/eclipse**.
2. Щелкните **Добавить**.
    ![Подключаемый модуль Service Fabric для Eclipse][sf-eclipse-plugin-install]
3. Выберите подключаемый модуль Service Fabric и нажмите кнопку **Next** (Далее).
4. Выполните установку и примите условия лицензионного соглашения на использование программного обеспечения корпорации Майкрософт.
  
Если подключаемый модуль Service Fabric уже установлен, необходимо использовать последнюю версию. 
1. Чтобы проверить наличие доступных обновлений, выберите **Help (Справка)** > **About Eclipse (Сведения об Eclipse)** > **Installation Details (Сведения об установке)**. 
2. В списке установленных подключаемых модулей выберите Service Fabric и щелкните **Update** (Обновить). После этого будут установлены доступные обновления.
3. После обновления подключаемого модуля Service Fabric также обновите проект Gradle.  Щелкните правой кнопкой мыши **build.gradle** и выберите **Обновить**.

> [!NOTE]
> Если установка или обновление подключаемого модуля Service Fabric выполняется медленно, это может быть связано с настройкой Eclipse. Eclipse собирает метаданные обо всех изменениях на сайтах обновления, зарегистрированных с помощью экземпляра Eclipse. Чтобы ускорить процесс проверки и установки обновления подключаемого модуля Service Fabric, щелкните **Available Software Sites** (Доступные сайты программного обеспечения). Снимите флажки напротив всех сайтов, которые не указывают на расположение подключаемого модуля Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Если Eclipse не работает должным образом на компьютере Mac или требуется запуск от имени суперпользователя, перейдите в папку **ECLIPSE_INSTALLATION_PATH** и откройте подпапку **Eclipse.app/Contents/MacOS**. Запустите Eclipse, выполнив команду `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Создание приложения Service Fabric в Eclipse

1.  В Eclipse выберите **File** > **New** > **Other** (Файл > Создать > Другое). Выберите **Service Fabric Project** (Проект Service Fabric) и нажмите кнопку **Next** (Далее).

    ![Создание проекта Service Fabric, страница 1][create-application/p1]

2.  Введите имя проекта и нажмите кнопку **Next** (Далее).

    ![Создание проекта Service Fabric, страница 2][create-application/p2]

3.  В списке шаблонов выберите **Service Template** (Шаблон службы). Выберите тип шаблона службы (субъект, без отслеживания состояния, контейнер или гостевой двоичный файл) и нажмите кнопку **Next** (Далее).

    ![Создание проекта Service Fabric, страница 3][create-application/p3]

4.  Введите имя службы и сведения о ней, а затем нажмите кнопку **Finish** (Готово).

    ![Создание проекта Service Fabric, страница 4][create-application/p4]

5. Если вы создаете первый проект Service Fabric, в диалоговом окне **Open Associated Perspective** (Открыть связанную перспективу) нажмите кнопку **Yes** (Да).

    ![Создание проекта Service Fabric, страница 5][create-application/p5]

6.  Новый проект выглядит следующим образом:

    ![Создание проекта Service Fabric, страница 6][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Создание и развертывание приложения Service Fabric в Eclipse

1.  Щелкните правой кнопкой мыши новое приложение Service Fabric, а затем выберите **Service Fabric**.

    ![Контекстное меню Service Fabric][publish/RightClick]

2. В подменю выберите нужный параметр:
    -   Чтобы выполнить сборку приложения без очистки, щелкните **Build Application** (Создать приложение).
    -   Для выполнения чистой сборки приложения щелкните **Rebuild Application** (Повторно создать приложение).
    -   Чтобы выполнить очистку приложения от артефактов сборки, щелкните **Clean Application** (Очистить приложение).

3.  В этом меню также есть параметры, позволяющие развернуть, отменить развертывание и опубликовать приложение.
    -   Чтобы выполнить развертывание в локальном кластере, щелкните **Deploy Application** (Развернуть приложение).
    -   В диалоговом окне **Publish Application** (Публикация приложения) выберите профиль публикации:
        -  **Local.json**;
        -  **Cloud.json**.

     В этих файлах в формате JSON хранится информация (например, о конечных точках подключения и безопасности), необходимая для подключения к локальному или облачному кластеру (Azure).

  ![Меню публикации Service Fabric][publish/Publish]

Альтернативным способом развертывания приложения Service Fabric является использование конфигураций запуска Eclipse.

  1.    Выберите **Run** > **Run Configurations** (Запуск > Конфигурации запуска).
  2.    Выберите конфигурацию запуска **ServiceFabricDeployer** в разделе **Grade Project** (Проект Gradle).
  3.    На вкладке **Arguments** (Аргументы) в области справа для параметра **publishProfile** выберите значение **local** (локальная среда) или **cloud** (облако).  По умолчанию используется значение **local**. Для развертывания в удаленном (облачном) кластере выберите **облачную** среду.
  4.    Чтобы убедиться, что в профилях публикации указаны нужные сведения, измените **Local.json** или **Cloud.json** при необходимости. Вы можете добавить или обновить сведения о конечной точке и учетные данные безопасности.
  5.    Убедитесь, что **рабочий каталог** указывает на приложение, которое необходимо развернуть. Чтобы изменить приложение, нажмите кнопку **Workspace** (Рабочая область) и выберите нужное приложение.
  6.    Щелкните **Apply** (Применить), а затем — **Run** (Запуск).

Приложение будет собрано и развернуто через несколько секунд. Состояние развертывания можно отслеживать в Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Добавление службы Service Fabric в приложение Service Fabric

Чтобы добавить службу Service Fabric в имеющееся приложение Service Fabric, сделайте следующее:

1.  Щелкните правой кнопкой мыши проект, в который нужно добавить службу, и выберите **Service Fabric**.

    ![Добавление службы Service Fabric, страница 1][add-service/p1]

2.  Щелкните **Add Service Fabric Service** (Добавить службу Service Fabric) и выполните действия для добавления службы в проект.
3.  Выберите шаблон службы и нажмите кнопку **Next** (Далее).

    ![Добавление службы Service Fabric, страница 2][add-service/p2]

4.  Введите имя службы (и другие сведения при необходимости), а затем нажмите кнопку **Add Service** (Добавить службу).  

    ![Добавление службы Service Fabric, страница 3][add-service/p3]

5.  После добавления службы общая структура проекта будет аналогична приведенной ниже.

    ![Добавление службы Service Fabric, страница 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Изменение версий манифеста приложения Service Fabric на Java

Чтобы изменить версии манифеста, щелкните проект правой кнопкой мыши, щелкните пункт **Service Fabric**, а затем в раскрывающемся меню выберите **Изменить версии манифеста...** В мастере можно обновить версии манифеста приложения, служб и версии пакетов **кода**, **конфигурации** и **данных**.

Если вы установите флажок **Автоматическое обновление версий приложения и службы**, а затем обновите версию, то версии манифестов обновятся автоматически. Например, если вы сначала установите флажок, а затем обновите версию **кода** с 0.0.0 до 0.0.1, а затем щелкните **Готово**, то версия манифеста служб и манифеста приложения будет автоматически обновлена до 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Обновление приложения Service Fabric на Java

Чтобы рассмотреть сценарий обновления, предположим, вы создали проект **App1** с помощью подключаемого модуля Service Fabric в Eclipse. Он был развернут с помощью подключаемого модуля для создания приложения с именем **fabric:/App1Application**. Тип приложения — **App1AppicationType**, а версия приложения — 1.0. Теперь необходимо обновить приложение, не прерывая доступ к нему.

Сначала внесите изменения в приложение, а затем выполните повторную сборку службы в измененном виде. Замените файл манифеста измененной службы (ServiceManifest.xml) обновленной версией для этой службы (а также все необходимые файлы из каталогов Code, Config и Data). Замените также манифест приложения (ApplicationManifest.xml) обновленной версией для приложения и измененной службы.  

Чтобы обновить приложение с помощью Eclipse, можно создать дубликат профиля конфигурации запуска. Затем вы сможете использовать его для обновления приложения.

1.  Выберите **Run** > **Run Configurations** (Запуск > Конфигурации запуска). Щелкните небольшую стрелку слева от **Gradle Project** (Проект Gradle) в области слева.
2.  Щелкните правой кнопкой мыши **ServiceFabricDeployer** и выберите **Duplicate** (Дублировать). Введите новое имя для этой конфигурации, например **ServiceFabricUpgrader**.
3.  На панели справа на вкладке **Arguments** (Аргументы) измените **-Pconfig='deploy'** на **-Pconfig='upgrade'**, а затем нажмите кнопку **Apply** (Применить).

Это действие позволяет создать и сохранить профиль конфигурации запуска, который можно использовать для обновления приложения в любое время. При этом вы также получаете последнюю обновленную версию типа приложения из файла манифеста приложения.

Обновление приложения занимает несколько минут. Его можно отслеживать в Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Перенос устаревших приложений Java из Service Fabric для использования с Maven
Мы недавно переместили библиотеки Java для Service Fabric из пакета SDK для Java Service Fabric в репозиторий Maven. В новых приложениях, созданных с помощью Eclipse, создаются последние обновленные проекты (что позволяет работать с Maven). Но вы можете обновить существующие приложения Service Fabric без учета состояния или Java Reliable Actor, в которых ранее применялся пакет SDK для Service Fabric Java, чтобы использовать зависимости Java для Service Fabric из Maven. Выполните [инструкции](service-fabric-migrate-old-javaapp-to-use-maven.md), чтобы устаревшие приложения могли взаимодействовать с Maven.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
