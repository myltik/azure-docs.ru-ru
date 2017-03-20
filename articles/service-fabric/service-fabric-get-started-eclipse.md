---
title: "Начало работы с подключаемым модулем Eclipse для Azure Service Fabric | Документация Майкрософт"
description: "Начало работы с подключаемым модулем Eclipse для Azure Service Fabric"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 891df38aa685cac7bbfecb71b15c88d557ac493b
ms.lasthandoff: 03/11/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Начало работы с подключаемым модулем Eclipse для разработки приложений Service Fabric на Java
Eclipse является одной из наиболее часто используемых сред для разработчиков Java. В этой статье мы рассмотрим, как настроить среду разработки Eclipse для работы с Service Fabric. Здесь описано, как установить подключаемый модуль, создать приложения Service Fabric и развернуть приложение Service Fabric в локальный или удаленный кластер Service Fabric.

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Установка и обновление подключаемого модуля Eclipse Neon для Service Fabric
Платформа Service Fabric предоставляет подключаемый модуль для **Eclipse IDE для разработчиков Java**, который упрощает процесс создания и развертывания служб Java.

1. Убедитесь, что у вас установлена последняя версия Eclipse **Neon** и Buildship (версия 1.0.17 или более поздняя). Вы можете проверить версии установленных компонентов, щелкнув ``Help => Installation Details``. Чтобы обновить Buildship, воспользуйтесь инструкциями [здесь][buildship-update]. Чтобы проверить версию Eclipse Neon и при необходимости обновить ее, перейдите по ссылке ``Help => Check for Updates``.

2. Чтобы установить подключаемый модуль Service Fabric , выберите ``Help => Install New Software...``.
  1. В поле Work with (Работа с) введите ``http://dl.windowsazure.com/eclipse/servicefabric``.
  2. Нажмите Добавить.

  ![Подключаемый модуль Eclipse Neon для Service Fabric][sf-eclipse-plugin-install]

  3. Выберите подключаемый модуль Service Fabric и нажмите кнопку Next (Далее).
  4. Выполните необходимые шаги установки и примите условия лицензионного соглашения.

Если подключаемый модуль Eclipse Service Fabric уже установлен, убедитесь, что вы используете последнюю версию. Вы можете проверить, можно ли его обновить, перейдя к ``Help => Installation Details``. Найдите Service Fabric в списке установленных подключаемых модулей и выберите обновление. При наличии ожидающего обновления оно будет извлечено и установлено.

> [!NOTE]
> Если установка или обновление подключаемого модуля Service Fabric Eclipse занимает много времени, это происходит потому, что каждый раз Eclipse пытается получить метаданные всех новых изменений, которые произошли на всех узлах обновления, зарегистрированных в экземпляре Eclipse. Для ускорения этих процессов можно использовать небольшую хитрость. Можно перейти к `Available Software Sites` и снять флажки напротив всех пунктов, которые не указывают на расположение подключаемого модуля Service Fabric `http://dl.windowsazure.com/eclipse/servicefabric`.
>

## <a name="create-service-fabric-application-using-eclipse"></a>Создание приложения Service Fabric на Java с помощью Eclipse

1. Перейдите на сайт ``File => New => Other``. Выберите ``Service Fabric Project``. Щелкните ``Next``.

    ![Создание проекта Service Fabric, страница 1][create-application/p1]

2. Присвойте имя проекту. Щелкните ``Next``.

    ![Создание проекта Service Fabric, страница 2][create-application/p2]

3. Выберите шаблон службы из набора доступных шаблонов (субъект, без отслеживания состояния, контейнер или исполняемый файл гостя). Щелкните ``Next``.

    ![Создание проекта Service Fabric, страница 3][create-application/p3]

4. Введите на странице имя службы и (или) соответствующие сведения о службе и щелкните ``Finish``.

    ![Создание проекта Service Fabric, страница 4][create-application/p4]

5. При создании первого проекта Service Fabric вам будет предложено подтвердить использование перспективы Service Fabric. Чтобы продолжить, выберите ``yes``.

    ![Создание проекта Service Fabric, страница 5][create-application/p5]

6. После успешного создания проект будет выглядеть следующим образом.

    ![Создание проекта Service Fabric, страница 6][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Создание и развертывание приложения Service Fabric с помощью Eclipse

* Щелкните правой кнопкой мыши созданное приложение Service Fabric. В контекстном меню выберите параметр ``Service Fabric``. Появится подменю с несколькими параметрами. Оно будет выглядеть следующим образом.

    ![контекстное меню Service Fabric][publish/RightClick]

  После выбора параметров будут выполнены необходимые действия (сборка, повторная сборка или очистка).
  - ``Build Application`` — будет выполнена сборка приложения без очистки.
  - ``Rebuild Application`` — будет выполнена чистая сборка приложения.
  - ``Clean Application`` — будет выполнена очистка приложения от артефактов сборки.


* Также в этом меню есть параметры, позволяющие развернуть, отменить развертывание и опубликовать приложение.
  - ``Deploy Application`` — будет выполнено развертывание в локальном кластере.
  - ``Publish Application...`` запрашивает, какой из профилей публикации необходимо выбрать: ``Local.json`` или ``Cloud.json``. Эти файлы JSON используются для хранения информации (например, конечных точек подключения и сведений о безопасности), необходимой для подключения к локальному или облачному кластеру (Azure).

  ![контекстное меню Service Fabric][publish/Publish]

* Существует альтернативный способ, с помощью которого можно развернуть приложение Service Fabric, используя конфигурации запуска Eclipse.

  1. Выберите ``Run => Run Configurations``. В разделе ``Grade Project`` выберите конфигурацию запуска ``ServiceFabricDeployer``.
  2. На вкладке ``Arguments`` в области справа в качестве ``publishProfile`` укажите **локальную** или **облачную** среду. По умолчанию используется **локальная** среда. Для развертывания в удаленном (облачном) кластере выберите **облачную** среду.
  3. Убедитесь, что в профилях публикации указаны нужные сведения, изменив файл `Local.json` или `Cloud.json` соответствующим образом, внеся сведения о конечной точке и учетные данные безопасности, если это необходимо.
  4. Убедитесь, что ``Working Directory`` в области справа в разделе ``Grade Project`` указывает на приложение, которое необходимо развернуть. В противном случае просто нажмите кнопку ``Workspace...`` и выберите нужное приложение.
  5. Нажмите кнопку **Применить**, а затем — **Выполнить**.

Приложение будет собрано и развернуто через несколько секунд. Его состояние можно отслеживать в Service Fabric Explorer.  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Добавление новой службы Service Fabric в приложение Service Fabric

Добавить новую службу Service Fabric к существующему приложению Service Fabric можно, выполнив следующие действия:

1. Щелкните правой кнопкой мыши проект, в который необходимо добавить службу, и в контекстном меню выберите параметр Service Fabric. Появится подменю с несколькими параметрами.

    ![Добавление службы Service Fabric, страница 1][add-service/p1]

2. Выберите параметр `Add ServiceFabric Service` и выполните следующие действия, чтобы добавить службу в проект.
3. Выберите шаблон службы и нажмите кнопку "Далее".

    ![Добавление службы Service Fabric, страница 2][add-service/p2]

4. Введите имя службы (и другие необходимые сведения) и нажмите кнопку "Добавить службу".  

    ![Добавление службы Service Fabric, страница 3][add-service/p3]

5. После успешного добавления службы структура проекта будет выглядеть следующим образом:

    ![Добавление службы Service Fabric, страница 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Обновление приложения Service Fabric на Java

Предположим, что вы создали и развернули проект ``App1`` с использованием подключаемого модуля Service Fabric Eclipse, чтобы создать приложение типа ``App1AppicationType`` версии 1.0 с именем ``fabric:/App1Application``. Теперь необходимо обновить приложение, не останавливая его.

Внесите изменения в приложение и выполните повторную сборку службы в измененном виде.  Замените файл манифеста измененной службы (``ServiceManifest.xml``) обновленной версией для этой службы (а также все необходимые файлы из каталогов Code, Config и Data). Также замените манифест приложения (``ApplicationManifest.xml``) обновленной версией для приложения и измененной службы.  

Чтобы обновить приложение с помощью Eclipse, можно создать дубликат конфигурации запуска и использовать его для обновления, выполнив следующие действия:
1. Выберите ``Run => Run Configurations``. Щелкните небольшую стрелку слева от ``Grade Project`` в области слева.
2. Щелкните правой кнопкой мыши ``ServiceFabricDeployer`` и выберите ``Duplicate``. Присвойте новое имя этой конфигурации, например ``ServiceFabricUpgrader``.
3. В области справа на вкладке ``Arguments`` измените ``-Pconfig='deploy'`` на ``-Pconfig=upgrade`` и нажмите кнопку ``Apply``.
4. Вы создали и сохранили конфигурацию запуска для обновления приложения, которую можно ``Run`` при необходимости. Это позволит получать последнюю обновленную версию типа приложения из файла манифеста приложения.

Обновление приложения можно отслеживать с помощью Service Fabric Explorer. Процесс обновления завершится через несколько минут.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

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

