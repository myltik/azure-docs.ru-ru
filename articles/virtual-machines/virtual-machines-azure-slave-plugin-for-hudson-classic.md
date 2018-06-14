---
title: Использование подчиненного подключаемого модуля Azure на сервере непрерывной интеграции Hudson | Документация Майкрософт
description: В статье описывается, как использовать подчиненный подключаемый модуль Azure на сервере непрерывной интеграции Hudson.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: c11b59f8ea432075b147a391de4b7bd3331e639e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2018
ms.locfileid: "27704425"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Использование подчиненного подключаемого модуля Azure на сервере непрерывной интеграции Hudson
Подчиненный подключаемый модуль Azure для Hudson позволяет подготовить подчиненные узлы в Azure при выполнении распределенных сборок.

## <a name="install-the-azure-slave-plug-in"></a>Установка подчиненного подключаемого модуля Azure
1. На панели мониторинга Hudson щелкните элемент **Manage Hudson**(Управление Hudson).
2. На странице **Manage Hudson** (Управление Hudson) щелкните **Manage Plugins** (Управление подключаемыми модулями).
3. Перейдите на вкладку **Available** (Доступный).
4. Щелкните **Search** (Поиск) и введите **Azure**, чтобы ограничить список результатов соответствующими подключаемыми модулями.
   
    Подключаемый модуль также можно найти в прокручиваемом списке. Подчиненный подключаемый модуль Azure находится в разделе **Cluster Management and Distributed Build** (Управление кластером и распределенная сборка) на вкладке **Others** (Другие).
5. Установите флажок **Azure Slave Plugin**(Подчиненный подключаемый модуль Azure).
6. Нажмите **Установить**.
7. Перезапустите Hudson.

Теперь, когда подключаемый модуль установлен, необходимо настроить его с помощью профиля подписки Azure и создать шаблон, который будет использоваться при создании виртуальной машины для подчиненного узла.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Настройка подчиненного подключаемого модуля Azure с помощью профиля подписки
Профиль подписки, также называемый настройками публикации, представляет собой XML-файл, содержащий защищенные учетные данные и некоторые дополнительные сведения, необходимые для использования Azure в среде разработки. Для настройки подчиненного подключаемого модуля Azure необходимы следующие данные:

* идентификатор подписки;
* сертификат управления подпиской.

Эти сведения можно найти в [профиле подписки]. Ниже приведен пример профиля подписки.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

После создания профиля подписки выполните перечисленные ниже действия, чтобы настроить подчиненный подключаемый модуль Azure.

1. На панели мониторинга Hudson щелкните элемент **Manage Hudson**(Управление Hudson).
2. Щелкните **Configure System**(Настройка системы).
3. Прокрутите страницу вниз до раздела **Cloud** (Облако).
4. Щелкните **Add new cloud > Microsoft Azure** (Добавить новое облако > Microsoft Azure).
   
    ![добавление нового облака][add new cloud]
   
    Появятся поля, в которые необходимо ввести сведения о подписке.
   
    ![настройка профиля][configure profile]
5. Скопируйте идентификатор подписки и сертификат управления из профиля подписки и вставьте их в соответствующие поля.
   
    При копировании идентификатора подписки и сертификата управления **не** копируйте кавычки, в которые заключены значения.
6. Щелкните **Verify configuration**(Проверить конфигурацию).
7. После проверки правильности конфигурации щелкните **Save**(Сохранить).

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Настройка шаблона виртуальной машины для подчиненного подключаемого модуля Azure
Шаблон виртуальной машины определяет параметры, которые подключаемый модуль будет использовать для создания подчиненного узла в Azure. Далее мы создадим шаблон для виртуальной машины Ubuntu.

1. На панели мониторинга Hudson щелкните элемент **Manage Hudson**(Управление Hudson).
2. Щелкните **Configure System**(Настройка системы).
3. Прокрутите страницу вниз до раздела **Cloud** (Облако).
4. В разделе **Cloud** (Облако) найдите элемент **Add Azure Virtual Machine Template** (Добавить шаблон виртуальной машины Azure) и нажмите кнопку **Add** (Добавить).
   
    ![добавление шаблона виртуальной машины][add vm template]
5. В поле **Name** (Имя) введите имя облачной службы. Если указанное имя ссылается на уже существующую облачную службу, виртуальная машина будет подготовлена в ней. В противном случае Azure создаст новую службу.
6. В поле **Description** (Описание) введите описание создаваемого вами шаблона. Эти сведения будут использоваться только в информационных целях и при подготовке виртуальной машины не учитываются.
7. В поле **Labels** (Метки) введите **linux**. Эта метка идентифицирует создаваемый вами шаблон и используется для обращения к нему при создании задания Hudson.
8. Выберите регион для создания виртуальной машины.
9. Выберите размер виртуальной машины.
10. Укажите учетную запись хранения, в которой будет создана виртуальная машина. Убедитесь, что она находится в одном регионе с облачной службой, которую вы собираетесь использовать. Если вы хотите создать новое хранилище, оставьте это поле пустым.
11. Время хранения — это время в минутах, которое должно пройти до удаления неактивного подчиненного модуля из Hudson. Оставьте значение по умолчанию 60.
12. В поле **Usage**(Использование) выберите условие для использования этого подчиненного узла. На этом этапе выберите вариант **Utilize this node as much as possible**(Использовать этот узел постоянно).
    
     На этом этапе заполняемая форма будет выглядеть примерно так:
    
     ![настройка шаблона][template config]
13. В поле **Image Family or Id** (Семейство или идентификатор образов) укажите, какой образ системы будет установлен на вашей виртуальной машине. Выберите образ из списка семейств или укажите пользовательский вариант.
    
     Если вы хотите выбрать образ из списка семейств, введите первую букву имени семейства (с учетом регистра). Например, если вы введете **U** , появится список семейств Ubuntu Server. При подготовке виртуальной машины Jenkins будет использовать последнюю версию образа системы из семейства, выбранного в списке.
    
     ![список семейств ОС][OS family list]
    
     Если вы хотите использовать пользовательский образ, введите его имя. Имена пользовательских образов не отображаются в списке, поэтому его необходимо ввести правильно.    
    
     В данном примере при вводе буквы **U** открывается список образов Ubuntu, и мы выбираем вариант **Ubuntu Server 14.04 LTS**.
14. В поле **Launch method** (Способ запуска) выберите **SSH**.
15. Скопируйте приведенный ниже сценарий и вставьте его в поле **Init script** (Сценарий инициализации).
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     **Сценарий инициализации** будет выполнен после создания виртуальной машины. В этом примере сценарий устанавливает Java, Git и Ant.
16. В полях **Username** (Имя пользователя) и **Password** (Пароль) введите значения для учетной записи администратора, которая будет создана на вашей виртуальной машине.
17. Чтобы проверить допустимость указанных параметров, щелкните **Verify Template** (Проверить шаблон).
18. Щелкните **Save**(Сохранить).

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Создание задания Hudson, выполняемого в подчиненном узле в Azure
В этом разделе вы узнаете, как создать задачу Hudson, которая должна выполняться в подчиненном узле в Azure.

1. На панели мониторинга Hudson щелкните элемент **New Job**(Создать задание).
2. Введите название создаваемого задания.
3. В качестве типа задания выберите **Build a free-style software job**(Создание программного задания в свободной форме).
4. Последовательно выберите **ОК**.
5. На странице настройки задания выберите **Restrict where this project can be run**(Ограничения для запуска этого проекта).
6. Откройте меню **Node and label** (Узел и метка) и выберите вариант **linux** (мы указали эту метку при создании шаблона виртуальной машины в предыдущем разделе).
7. В поле **Build** (Сборка) щелкните **Add build step** (Добавить шаг сборки) и выберите **Execute shell** (Запустить оболочку).
8. В показанном ниже сценарии вместо **{your github account name}**, **{your project name}** и **{your project directory}** укажите соответствующие значения и вставьте измененный сценарий в появившуюся текстовую область.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Щелкните **Save**(Сохранить).
10. На панели мониторинга Hudson найдите только что созданное задание и щелкните значок **Schedule a build** (Назначить сборку).

Затем на основании шаблона, созданного в предыдущем разделе, Hudson создаст подчиненный узел и выполнит сценарий, который вы указали на этапе сборки данной задачи.

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об использовании Azure с Java можно найти в [Центре разработчиков Java для Azure].

<!-- URL List -->

[Центре разработчиков Java для Azure]: https://azure.microsoft.com/develop/java/
[профиле подписки]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

