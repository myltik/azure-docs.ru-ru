---
title: Размещение веб-сайта Ruby on Rails в виртуальной машине Linux | Документация Майкрософт
description: Настройка и размещение веб-сайта на основе Ruby on Rails в Azure с помощью виртуальной машины Linux.
services: virtual-machines-linux
documentationcenter: ruby
author: rmcmurray
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: aad32685-3550-4bff-9c73-beb8d70b3291
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: ruby
ms.topic: article
ms.date: 06/27/2017
ms.author: robmcm
ms.openlocfilehash: fa19f3dc7dded712102d4ba9b66dd4df1bfd20dd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29397603"
---
# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Веб-приложение Ruby on Rails на виртуальной машине Azure
В этом учебнике описано, как разместить веб-сайт Ruby on Rails в Azure с помощью виртуальной машины Linux.  

Данный учебник был проверен с использованием Ubuntu Server 14.04 LTS. Если вы используете другой дистрибутив Linux, могут потребоваться другие действия для установления Rails.

> [!IMPORTANT]
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель Resource Manager и классическая модель](../../../azure-resource-manager/resource-manager-deployment-model.md).  В этой статье рассматривается использование классической модели развертывания. Для большинства новых развертываний Майкрософт рекомендует использовать модель диспетчера ресурсов.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]
>

## <a name="create-an-azure-vm"></a>Создание виртуальной машины Azure
Начните с создания виртуальной машины Azure с помощью образа Linux.

Чтобы создать виртуальную машину, можно использовать портал Azure или интерфейс командной строки Azure (Azure CLI).

### <a name="azure-portal"></a>Портал Azure
1. Войдите на [портал Azure](https://portal.azure.com).
2. Щелкните **Создать ресурс**, затем в поле поиска введите "Ubuntu Server 14.04". Щелкните запись, полученную в результате поиска. Чтобы указать модель развертывания, выберите **Классическая**, и нажмите кнопку "Создать".
3. В колонке "Основные" укажите следующие значения в обязательных полях: имя (для виртуальной машины), имя пользователя, тип аутентификации и соответствующие учетные данные, подписку Azure, группу ресурсов и расположение.

   ![Создание нового образа Ubuntu](./media/virtual-machines-linux-classic-ruby-rails-web-app/createvm.png)

4. После подготовки виртуальной машины выберите ее имя и щелкните **Конечные точки** в категории **Параметры**. Найдите конечную точку SSH в списке **Автономная**.

   ![Конечная точка по умолчанию](./media/virtual-machines-linux-classic-ruby-rails-web-app/endpointsnewportal.png)

### <a name="azure-cli"></a>Инфраструктура CLI Azure
Выполните действия в статье [Создание виртуальной машины под управлением Linux][vm-instructions].

После подготовки виртуальной машины можно получить конечную точку SSH, выполнив следующую команду:

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Установка Ruby on Rails
1. Используйте SSH, чтобы подключиться к виртуальной машине.
2. В сеансе SSH выполните следующие команды, чтобы установить Ruby на виртуальной машине:

        sudo apt-get update -y
        sudo apt-get upgrade -y

        sudo apt-add-repository ppa:brightbox/ruby-ng
        sudo apt-get update
        sudo apt-get install ruby2.4

        > [!TIP]
        > The brightbox repository contains the current Ruby distribution.

    Установка может занять несколько минут. После завершения воспользуйтесь следующей командой, чтобы проверить установку Ruby:

        ruby -v

3. Используйте следующую команду для установки Rails:

        sudo gem install rails --no-rdoc --no-ri -V

    С помощью флагов --no-rdoc и --no-ri можно пропустить установку документации, что значительно ускорит процесс.
    Вероятно, эта команда будет долго выполняться. Добавив параметр -V, вы сможете наблюдать за ходом установки.

## <a name="create-and-run-an-app"></a>Создание и запуск приложения
Войдите в систему через SSH и выполните следующие команды:

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

Команда [new](http://guides.rubyonrails.org/command_line.html#rails-new) создает новое приложение Rails. Команда [server](http://guides.rubyonrails.org/command_line.html#rails-server) запускает веб-сервер WEBrick, входящий в состав Rails. (В рабочей среде вы, вероятно, будете использовать другой сервер, например Unicorn или Passenger).

Вы должны увидеть результат, аналогичный приведенному ниже.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Добавление конечной точки
1. Перейдите на [портал Azure][https://portal.azure.com] и выберите виртуальную машину.

2. Выберите **Конечные точки** в категории **Параметры** у левого края страницы.

3. В верхней части страницы щелкните **Добавить**.

4. На диалоговой странице **Добавление конечной точки** введите следующие сведения.

   * **Имя**: HTTP.
   * **Протокол**: TCP
   * **Общий порт**: 80.
   * **Частный порт**: 3000.
   * **Плавающий IP-адрес**: отключен.
   * **Список управления доступом — порядок**: 1001 или другое значение, которое задает приоритет этого правила доступа.
   * **Список управления доступом — имя**: allowHTTP.
   * **Список управления доступом — действие**: разрешить.
   * **Список управления доступом — удаленная подсеть**: 1.0.0.0/16.

     У этой конечной точки есть общий порт 80, который будет применяться для маршрутизации трафика на частный порт 3000, на котором сервер Rails ожидает передачи данных. Правило списка управления доступом разрешает передачу общего трафика через порт 80.

     ![Новая конечная точка](./media/virtual-machines-linux-classic-ruby-rails-web-app/createendpoint.png)

5. Нажмите кнопку "ОК", чтобы сохранить конечную точку.

6. Должно появиться сообщение о **сохранении конечной точки виртуальной машины**. После того как сообщение исчезнет, конечная точка будет активирована. Теперь вы можете протестировать приложение, перейдя к имени DNS виртуальной машины. Веб-сайт должен выглядеть аналогично следующему:

    ![страница Rails по умолчанию][default-rails-cloud]

## <a name="next-steps"></a>Дополнительная информация
В этом учебнике большинство действий вы выполняли вручную. В рабочей среде вы напишете приложение на компьютере для разработки и развернете его на виртуальной машине Azure. Кроме того, большинство рабочих сред размещают приложение Rails вместе с другим серверным процессом, например Apache или NginX, который обрабатывает маршрутизацию запросов к нескольким экземплярам приложения Rails и обслуживает статические ресурсы. Дополнительные сведения см. на странице http://rubyonrails.org/deploy/.

Дополнительные сведения о Ruby on Rails см. на веб-сайте [руководств по Ruby on Rails][rails-guides].

Об использовании служб Azure из приложения Ruby можно узнать из следующих материалов:

* [Хранение неструктурированных данных с использованием BLOB-объектов][blobs]
* [Хранение пар "ключ-значение" с помощью таблиц][tables]
* [Передача контента с широкой полосой пропускания с помощью CDN][cdn-howto]

<!-- WA.com links -->
[blobs]:../../../storage/blobs/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]:https://azure.microsoft.com/develop/ruby/app-services/
[tables]:../../../cosmos-db/table-storage-how-to-use-ruby.md
[vm-instructions]:createportal-classic.md

<!-- External Links -->
[rails-guides]:http://guides.rubyonrails.org/
[sqlite3]:http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]:./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]:./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]:./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]:./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png
