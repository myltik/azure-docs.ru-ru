---
title: "Непрерывная интеграция и непрерывное развертывание из Jenkins на виртуальных машинах Azure с помощью Team Services | Документация Майкрософт"
description: "Настройка непрерывной интеграции (CI) и непрерывного развертывания (CD) приложения Node.js с помощью Jenkins на виртуальных машинах Azure из Release Management в Visual Studio Team Services (VSTS) или Microsoft Team Foundation Server (TFS)."
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/15/2017
ms.author: ahomer
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a40e26a8681df31fad664e4d1df4c1513311900d
ms.contentlocale: ru-ru
ms.lasthandoff: 08/21/2017

---

# <a name="deploy-your-app-to-linux-vms-using-jenkins-and-team-services"></a>Развертывание приложения на виртуальных машинах Linux с помощью Jenkins и Team Services

Непрерывная интеграция (CI) и непрерывное развертывание (CD) представляют собой конвейер, с помощью которого можно выполнить сборку, выпустить и развернуть свой код. Team Services предоставляет полнофункциональный набор инструментов для автоматизации CI и CD для развертывания в Azure. Jenkins — это популярный серверный инструмент CI и CD стороннего поставщика, который также обеспечивает автоматизацию этих процессов. Эти инструменты можно использовать вместе, чтобы настроить способ доставки своего облачного приложения или службы.

В этом руководстве используется Jenkins для выполнения сборки **веб-приложения Node.js** и Visual Studio Team Services — для его развертывания в [группе развертывания](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/), содержащей виртуальные машины Linux.

Вы сможете выполнять следующие задачи:

> [!div class="checklist"]
> * Выполнение сборки приложения в Jenkins.
> * Настройка Jenkins для интеграции с Team Services.
> * Создание группы развертывания для виртуальных машин Azure.
> * Создание определения выпуска, которое настраивает виртуальные машины и развертывает приложение.

## <a name="before-you-begin"></a>Перед началом работы

* Вам нужен доступ к учетной записи Jenkins. Если вы еще не создали сервер Jenkins, ознакомьтесь с [документацией по Jenkins](https://jenkins.io/doc/). 

* Войдите в свою учетную запись Team Services (`https://{youraccount}.visualstudio.com`). 
  Вы можете получить [бесплатную учетную запись Team Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Дополнительные сведения см. в разделе [Connect to Visual Studio Team Services from Eclipse, Xcode, Visual Studio, and more](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) (Подключение к Visual Studio Team Services из Eclipse, Xcode, Visual Studio и других сред).

* Создайте личный маркер доступа (PAT) в своей учетной записи Team Services, если у вас его еще нет. Эти данные требуются Jenkins для доступа к учетной записи Team Services.
  Прочитайте статью [Authenticate access with personal access tokens for Team Services and TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) (Аутентификация доступа с помощью личных маркеров доступа для Team Services и TFS), чтобы узнать, как создать такой маркер.

## <a name="get-the-sample-app"></a>Получение примера приложения

Развертываемое приложение должно храниться в репозитории Git.
В данном руководстве мы рекомендуем использовать [этот пример приложения с сайта GitHub](https://github.com/azooinmyluggage/fabrikam-node).

1. Создайте вилку этого приложения и запишите расположение (URL-адрес), чтобы использовать его позже в данном руководстве.

1. Сделайте вилку **общедоступной**, чтобы упростить предстоящее подключение к GitHub.

> [!NOTE]
> Дополнительные сведения см. в разделах [Fork A Repo](https://help.github.com/articles/fork-a-repo/) (Создание вилки репозитория) и [Making a private repository public](https://help.github.com/articles/making-a-private-repository-public/) (Как сделать частный репозиторий общедоступным).

> [!NOTE]
> Приложение было создано с помощью [Yeoman](http://yeoman.io/learning/index.html). В нем используются **Express**, **bower** и **grunt**, а также несколько пакетов **npm** в качестве зависимостей.
> Пример приложения содержит набор [шаблонов Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment), которые используются для динамического создания виртуальных машин для развертывания в Azure. Эти шаблоны используются задачами в [определении выпуска Team Services](https://www.visualstudio.com/docs/build/actions/work-with-release-definitions).
> Основной шаблон создает группу безопасности сети, виртуальную машину и виртуальную сеть. Он назначает общедоступный IP-адрес и открывает входящий порт 80. Он также добавляет тег, который используется группой развертывания для выбора целевых компьютеров для развертывания.
>
> Кроме того, пример содержит сценарий, который устанавливает Nginx и развертывает приложение. Он выполняется на каждой виртуальной машине. В частности, этот сценарий устанавливает Node, Nginx и PM2, настраивает Nginx и PM2, а затем запускает приложение Node.

## <a name="configure-jenkins-plugins"></a>Настройка подключаемых модулей Jenkins

Сначала необходимо настроить два подключаемых модуля Jenkins для **NodeJS** и **интеграции с Team Services**.

1. Откройте учетную запись Jenkins и выберите **Manage Jenkins** (Управление Jenkins).

1. На странице **Manage Jenkins** (Управление Jenkins) щелкните **Manage Plugins** (Управление подключаемыми модулями).

1. Отфильтруйте список, чтобы найти подключаемый модуль **NodeJS**, и установите его без перезапуска.

   ![Добавление подключаемого модуля NodeJS в Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)

1. Отфильтруйте список, чтобы найти подключаемый модуль **Team Foundation Server**, и установите его. (Этот подключаемый модуль подходит и для Team Services, и для Team Foundation Server.) Перезапускать Jenkins не обязательно.

## <a name="configure-jenkins-build-for-nodejs"></a>Настройка сборки Jenkins для Node.js

Создайте в Jenkins проект сборки и настройте его следующим образом.

1. На вкладке **General** (Общие) введите имя проекта сборки.

1. На вкладке **Source Code Management** (Управление исходным кодом) выберите **Git** и введите сведения о репозитории и ветви, содержащей код приложения.

   ![Добавление репозитория в сборку](media/tutorial-build-deploy-jenkins/jenkins-git.png)

   > [!NOTE]
   > Если репозиторий не является общедоступным, щелкните **Add** (Добавить) и введите учетные данные для подключения к нему.

1. На вкладке **Build Triggers** (Триггеры сборки) выберите **Poll SCM** (Опрос SCM) и введите расписание `H/03 * * * *` для опроса репозитория Git на наличие изменений каждые три минуты. 

1. На вкладке **Build Environment** (Среда сборки) щелкните **Provide Node &amp; npm bin/ folder PATH** (Укажите путь к папке bin для Node и npm) и введите `NodeJS` в качестве значения установки Node JS. Для параметра **npmrc file** (NPMRC-файл) оставьте значение "use system default" (Использовать системное значение по умолчанию).

1. На вкладке**Build** (Сборка) введите команду `npm install`, чтобы обновить все зависимости.

## <a name="configure-jenkins-for-team-services-integration"></a>Настройка Jenkins для интеграции с Team Services.

1. На вкладке **Post-build Actions** (Действия после сборки) для параметра **Files to archive** (Файлы для архивации) введите `**/*`, чтобы включить в архив все файлы.

1. На вкладке **Trigger release in TFS/Team Services** (Активация выпуска в TFS и Team Services) следует ввести полный URL-адрес учетной записи (например, `https://your-account-name.visualstudio.com`), имя проекта, имя определения сборки (будет создано позже) и учетные данные для подключения к вашей учетной записи.
   Вам потребуется имя пользователя и личный маркер доступа, созданный ранее. 

   ![Настройка действий после сборки Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)

1. Сохраните проект сборки.

## <a name="create-a-jenkins-service-endpoint"></a>Создание конечной точки службы Jenkins

Конечная точка службы позволяет Team Services подключаться к Jenkins.

1. Откройте страницу **Службы** в Team Services, откройте список **Новая конечная точка службы**, а затем выберите **Jenkins**.

   ![Добавление конечной точки Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)

1. Введите имя, которое будет использоваться для ссылок на это подключение.

1. Введите URL-адрес сервера Jenkins и выберите параметр **Принимать ненадежные SSL-сертификаты**.

1. Введите имя пользователя и пароль для учетной записи Jenkins.

1. Щелкните **Проверить подключение**, чтобы проверить правильность указанных данных.

1. Нажмите кнопку **ОК**, чтобы создать конечную точку службы.

## <a name="create-a-deployment-group"></a>Создание группы развертывания

[Группа развертывания](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) необходима для хранения виртуальных машин.

1. Откройте вкладку **Выпуски** элемента **Сборка и выпуск**, затем откройте вкладку **Группы развертывания** и нажмите кнопку **+ Создать**.

1. Введите имя группы развертывания и необязательное описание.
   Щелкните **Создать**.

Задача развертывания группы ресурсов Azure создает и регистрирует виртуальные машины, когда она выполняется с помощью шаблона Azure Resource Manager.
В этом случае не нужно создавать и регистрировать виртуальные машины самостоятельно.

## <a name="create-a-release-definition"></a>Создание определения выпуска

Определение выпуска задает процесс, выполняемый Team Services для развертывания приложения.
Вот как можно создать определение выпуска в Team Services.

1. Откройте вкладку **Выпуски** элемента **Сборка и выпуск**, щелкните раскрывающийся список **+** в списке определений выпуска, а затем выберите **Создание определения выпуска**. 

1. Выберите шаблон **Пустой** и нажмите кнопку **Далее**.

1. В разделе **Артефакты** щелкните **Link an Artifact** (Привязать артефакт) и выберите **Jenkins**. Выберите подключение к конечной точке службы Jenkins. Затем выберите исходное задание Jenkins и щелкните **создать**. 

1. В разделе нового определения выпуска щелкните **+ Add tasks** (+ Добавить задачи) и добавьте задачу **Развертывание группы ресурсов Azure** в среду по умолчанию.

1. Щелкните стрелку раскрывающегося списка рядом со ссылкой **+ Add tasks** (+ Добавить задачи) и добавьте в определение этап группы развертывания.

   ![Добавление этапа группы развертывания](media/tutorial-build-deploy-jenkins/deployment-group-phase-in-release-definition.png) 

1. В каталоге задач откройте раздел **Служебное** и добавьте экземпляр задачи **Сценарий оболочки**.

1. Шаблон параметров, используемый в задаче развертывания группы ресурсов Azure, задает пароль администратора для подключения к виртуальным машинам.
   Для указания этого пароля используется переменная **$(adminpassword)**.
   
   - Откройте вкладку **Переменные** и в разделе **Переменные** введите имя `adminpassword`.

   - Введите пароль администратора.

   - Щелкните значок замка рядом с текстовым полем значения, чтобы защитить пароль. 

## <a name="configure-the-azure-resource-group-deployment-task"></a>Настройка задачи развертывания группы ресурсов Azure

Задача **Развертывание группы ресурсов Azure** используется для создания группы развертывания. Настройте его следующим образом.

* **Подписка Azure**: выберите подключение из списка в разделе **Доступные подключения к службе Azure**. 
  Если подключения отсутствуют, щелкните **Управление**, выберите **Создать конечную точку службы**, щелкните **Диспетчер ресурсов Azure** и следуйте инструкциям на экране.
  Вернитесь к определению выпуска, обновите список **Подписка AzureRM** и выберите подключение, которое вы создали.

* **Группа ресурсов**: введите имя группы ресурсов, созданной ранее.

* **Расположение**: выберите регион для развертывания.

  ![Создание новой группы ресурсов](media/tutorial-build-deploy-jenkins/provision-web-server.png)

* **Расположение шаблона**: `URL of the file`.

* **Ссылка на шаблон**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.json`.

* **Ссылка на параметры шаблона**: `{your-git-repo}/ARM-Templates/UbuntuWeb1.parameters.json`.

* **Переопределить параметры шаблона**: список значений для переопределения, например `-location {location} -virtualMachineName {machine] -virtualMachineSize Standard_DS1_v2 -adminUsername {username} -virtualNetworkName fabrikam-node-rg-vnet -networkInterfaceName fabrikam-node-websvr1 -networkSecurityGroupName fabrikam-node-websvr1-nsg -adminPassword $(adminpassword) -diagnosticsStorageAccountName fabrikamnodewebsvr1 -diagnosticsStorageAccountId Microsoft.Storage/storageAccounts/fabrikamnodewebsvr1 -diagnosticsStorageAccountType Standard_LRS -addressPrefix 172.16.8.0/24 -subnetName default -subnetPrefix 172.16.8.0/24 -publicIpAddressName fabrikam-node-websvr1-ip -publicIpAddressType Dynamic`.<br />Вставьте собственные значения вместо {заполнителей}. 

* **Включить необходимые компоненты**: `Configure with Deployment Group agent`.

* **Конечная точка TFS или VSTS**: щелкните **Добавить**, затем в диалоговом окне "Add new Team Foundation Server/Team Services Connection" (Добавление нового подключения к Team Foundation Server или Team Services) выберите **Проверка подлинности на основе токена**. Введите имя подключения и URL-адрес командного проекта. Затем создайте и введите [личный маркер доступа (PAT)]( https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate) для аутентификации подключения к командному проекту.

  ![Создание личного маркера доступа](media/tutorial-build-deploy-jenkins/create-a-pat.png)

* **Командный проект**: выберите свой текущий проект.

* **Группа развертывания**: введите имя группы развертывания, указанное для параметра **Группа ресурсов**.

По умолчанию задача развертывания группы ресурсов Azure создает или обновляет ресурс поэтапно. Она создает виртуальные машины при первом запуске, а затем только обновляет их.

## <a name="configure-the-shell-script-task"></a>Настройка задачи сценария оболочки

Задача **Сценарий оболочки** используется для предоставления конфигурации для сценария, выполняемого на каждом сервере для установки Node.js и запуска приложения. Настройте его следующим образом.

* **Путь к скрипту**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh`.

* **Указать рабочий каталог**: `Checked`.

* **Рабочий каталог**: `$(System.DefaultWorkingDirectory)/Fabrikam-Node`.
   
## <a name="rename-and-save-the-release-definition"></a>Переименование и сохранение определения выпуска

1. Измените имя определения выпуска, указав имя, введенное на вкладке **Post-build Actions** (Действия после сборки) для сборки в Jenkins. Jenkins необходимо это имя, чтобы активировать новый выпуск при обновлении исходных артефактов.

1. При необходимости измените имя среды, щелкнув его. 

1. Щелкните **Сохранить** и нажмите кнопку **ОК**.

## <a name="start-a-manual-deployment"></a>Запуск развертывания вручную

1. Щелкните **+ Release** (+ Выпуск) и выберите **Создать выпуск**.

1. Щелкните созданную сборку в выделенном раскрывающемся списке и выберите **Создать**.

1. Перейдите по ссылке на выпуск во всплывающем сообщении. Пример: Создан выпуск **Release-1**.

1. Откройте вкладку **Журналы**, чтобы просмотреть выходные данные консоли для выпуска.

1. Откройте в браузере URL-адрес одного из серверов, добавленных в группу развертывания. Например, введите `http://{your-server-ip-address}`.

## <a name="start-a-cicd-deployment"></a>Запуск непрерывной интеграции или непрерывного развертывания

1. В определении выпуска снимите флажок **Включено** в разделе **Параметры управления** задачи развертывания группы ресурсов Azure.
   Для будущих развертываний в существующую группу развертывания не нужно будет повторно выполнять эту задачу.

1. Перейдите в исходный репозиторий Git и измените содержимое заголовка **h1** в файле [app/views/index.jade](https://github.com/azooinmyluggage/fabrikam-node/blob/master/app/views/index.jade).

1. Зафиксируйте изменения.

1. Через несколько минут вы увидите созданный выпуск на странице **Выпуски** в Team Services или TFS. Откройте этот выпуск, чтобы увидеть, как выполняется развертывание. Поздравляем!

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы автоматизировали развертывание приложения в Azure, используя сборку Jenkins и Team Services для выпуска. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * Выполнение сборки приложения в Jenkins.
> * Настройка Jenkins для интеграции с Team Services.
> * Создание группы развертывания для виртуальных машин Azure.
> * Создание определения выпуска, которое настраивает виртуальные машины и развертывает приложение.

Перейдите к следующему руководству, чтобы узнать, как развернуть стек LAMP (Linux, Apache, MySQL и PHP).

> [!div class="nextstepaction"]
> [Развертывание стека LAMP](tutorial-lamp-stack.md)
