<properties
   pageTitle="Настройка среды разработки для Mac OS X | Microsoft Azure"
   description="Установите среду выполнения, пакет SDK и инструменты и создайте локальный кластер разработки. После завершения установки вы сможете создавать приложения на базе Mac OS X."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# Настройка среды разработки для Mac OS X

> [AZURE.SELECTOR]
-[ Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Вы можете создать приложения Service Fabric на базе Mac OS X, работающие в кластерах Linux. Из этой статьи вы узнаете, как настроить систему Mac для разработки приложений.

## Предварительные требования

Платформа Service Fabric изначально не предназначена для работы в OS X. Чтобы запустить локальный кластер Service Fabric, мы предоставляем предварительно настроенную виртуальную машину Ubuntu с использованием программ Vagrant и VirtualBox. Перед началом работы вам потребуются:

- [Vagrant (1.8.4 или более поздней версии)](http://wwww.vagrantup.com/downloads);
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads).

## Создание локальной виртуальной машины Linux

Чтобы создать локальную виртуальную машину, содержащую кластер Service Fabric с пятью узлами, выполните следующие действия.

1. Клонируйте репозиторий Vagrantfile.

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Перейдите к локальному клону репозитория.

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Необязательно.) Измените параметры по умолчанию для виртуальной машины.

    По умолчанию локальные виртуальные машины имеют следующую конфигурацию:

    - 3 ГБ выделенной памяти;
    - частная сеть хоста с IP 192.168.50.50, обеспечивающая транзит трафика с узла Mac.

    Вы можете изменить любой из этих параметров или добавить другую конфигурацию виртуальной машины в Vagrantfile. Полный список параметров см. в [документации по Vagrant](http://www.vagrantup.com/docs).

4. Создание виртуальной машины

    ```bash
    vagrant up
    ```

    На этом этапе требуется скачать предварительно настроенный образ виртуальной машины, загрузить его на локальном устройстве, а затем установить в нем локальный кластер Service Fabric. Это займет несколько минут. Если установка выполнена успешно, в окне вывода появится сообщение о начале запуска кластера.

    ![Начало установки кластера после подготовки виртуальной машины][cluster-setup-script]

5. Проверьте правильность установки кластера. Для этого перейдите в Service Fabric Explorer: http://192.168.50.50:19080/Explorer (при условии, что вы сохранили IP-адрес частной сети по умолчанию).

    ![Вид Service Fabric Explorer с узла Mac][sfx-mac]


## Установка подключаемого модуля Service Fabric для Eclipse Neon (необязательно)

Платформа Service Fabric предоставляет подключаемый модуль для интерфейса IDE Eclipse Neon, который упрощает процесс создания и развертывания служб Java.

1. Откройте Eclipse и убедитесь, что у вас установлена версия Buildship 1.0.17 или более поздняя. Вы можете проверить версии установленных компонентов, щелкнув **Help > Installation Details** (Справка > Сведения об установке). Чтобы обновить Buildship, воспользуйтесь [инструкциями][buildship-update].

2. Чтобы установить подключаемый модуль Service Fabric, щелкните **Help > Install New Software** (Справка > Установка нового программного обеспечения).

3. В поле Work with (Работа с) введите http://dl.windowsazure.com/eclipse/servicefabric.

4. Нажмите Добавить.

    ![Подключаемый модуль Eclipse Neon для Service Fabric][sf-eclipse-plugin-install]

5. Выберите подключаемый модуль Service Fabric и нажмите кнопку Next (Далее).

6. Выполните необходимые шаги установки и примите условия лицензионного соглашения.

## Дальнейшие действия

- [Create your first Azure Service Fabric application](service-fabric-create-your-first-linux-application-with-java.md) (Создание первого приложения Azure Service Fabric)

<!-- Links -->

- [Создание кластера Service Fabric в Azure с помощью портала Azure](service-fabric-cluster-creation-via-portal.md)
- [Создание кластера Service Fabric в Azure с помощью Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
- [Моделирование приложения в Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!---HONumber=AcomDC_0928_2016-->