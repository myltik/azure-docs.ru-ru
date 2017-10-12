---
title: "Роуминг и совместная работа в Azure Machine Learning Workbench | Документация Майкрософт"
description: "Список известных проблем и руководство по устранению неполадок"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Роуминг и совместная работа в Azure Machine Learning Workbench
В этом документе вы узнаете, как Azure Machine Learning Workbench может помочь перемещать проекты на разных компьютерах, а также обеспечить совместную работу с коллегами. 

При создании проекта службы "Машинное обучение Azure" с помощью удаленной ссылки репозитория Git метаданные и моментальные снимки проекта хранятся в облаке. По ссылке облака можно получить доступ к проекту с другого компьютера (в роуминге). Вы можете предоставить доступ сотрудникам в организации, обеспечив совместную работу. 

## <a name="prerequisites"></a>Предварительные требования
Сначала необходимо установить Azure Machine Learning Workbench с доступом к учетной записи службы "Экспериментирование". Дополнительные сведения см. в [руководстве по установке](quickstart-installation.md).

Затем получите доступ к [Visual Studio Team System](https://www.visualstudio.com) и создайте репозиторий, чтобы подключиться к проекту. Подробные сведения о репозитории Git см. в статье [Using Git repository with an Azure Machine Learning Workbench project](using-git-ml-project.md) (Использование репозитория Git с помощью проекта Azure Machine Learning Workbench).

## <a name="create-a-new-azure-machine-learning-project"></a>Создание проекта в службе "Машинное обучение Azure"
Запустите Azure Machine Learning Workbench и создайте проект (например, _iris_). Введите в текстовое поле **Visualstudio.com GIT Repository URL** (URL-адрес репозитория GIT для VisualStudio.com) допустимый URL-адрес репозитория Git для VSTS. 
>[!IMPORTANT]
>Создание проекта завершится ошибкой, если у вас нет доступа к чтению и записи в репозитории Git и если репозиторий уже имеет главную ветвь.

После создания проекта отправьте несколько потоковых выполнений на любой скрипт в рамках проекта. Это действие фиксирует состояние проекта в удаленной ветви журнала выполнений репозитория Git. 

После настройки проверки подлинности Git можно также создать ветвь или работать в главной ветви. 

Например: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Роуминг
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Открытие Azure Machine Learning Workbench на другом компьютере
После выполнения привязки репозитория Git для VSTS с проектом можно получить доступ к проекту _iris_ с любого компьютера, на котором установлена служба Azure Machine Learning Workbench. 

Чтобы получить доступ к проекту iris на другом компьютере, необходимо войти в приложение с теми же учетными данными, которые использовались при создании проекта. Кроме того, необходимо перейти на ту же учетную запись и рабочую область службы "Экспериментирование". Проект _iris_ находится в списке проектов в рабочей области (составленном в алфавитном порядке). 

### <a name="download-project-on-second-machine"></a>Скачивание проекта на другой компьютер
При открытии рабочей области на другом компьютере значок возле проекта _iris_ отличается от типичного значка папки. Значок скачивания указывает, что содержимое проекта находится в облаке и его необходимо скачать на этот компьютер. 

![создание проекта](./media/roaming-and-collaboration/downloadable-project.png)

Щелкнув проект _iris_, вы начнете действие скачивания. Через некоторое время после завершения скачивания можно получить доступ к проекту на другом компьютере. 

В Windows это `C:\Users\<username>\Documents\AzureML`.

На macOS — `/home/<username>/Documents/AzureML`.

В следующем выпуске мы планируем расширить функциональность, чтобы можно было выбрать папку назначения. 

>Обратите внимание, что если в каталоге Azure ML есть папка, имя которой совпадает с именем проекта, скачивание завершится сбоем. Чтобы решить эту проблему, необходимо переименовать имеющеюся папку.


### <a name="work-on-the-downloaded-project"></a>Работа со скачанным проектом 
В скачанном проекте отображается состояние проекта на момент его последнего запуска. Моментальный снимок состояния проекта автоматически фиксируется в ветви журнала выполнения репозитория Git для VSTS при каждом запуске. Моментальный снимок, связанный с последним запуском, используется при создании проекта на другом компьютере. 
 

## <a name="collaboration"></a>Совместная работа
В проектах, связанных с репозиторием Git для VSTS, можно работать совместно з коллегами. Вы можете назначить разрешения пользователям учетной записи, рабочей области и проекта службы "Экспериментирование". Сейчас можно выполнять команды Azure Resource Manager с помощью Azure CLI. Кроме того, можно использовать [портал Azure](https://portal.azure.com). Этот процесс описан в [следующем разделе](#portal).    

### <a name="using-command-line-to-add-users"></a>Использование командной строки для добавления пользователей
Давайте воспользуемся примером. Предположим, что Алиса является владельцем проекта iris и ей нужно предоставить Бобу доступ к проекту. 

Алиса щелкает меню **Файл** и выбирает значок меню **Command Prompt** (Командная строка), чтобы запустить командную строку, настроенную для проекта _iris_. Затем Алиса решает, какой уровень доступа она хочет предоставить Бобу, выполнив следующие команды.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

После назначения роли напрямую или путем наследования в списке проектов рабочей среды для Боба отобразится этот проект. Чтобы проект отобразился в списке, возможно, приложение потребуется перезагрузить. Затем Боб может загрузить проект, как описано в разделе [Роуминг](#roaming), и работать совместно с Алисой. 

Журнал выполнения для всех пользователей, совместно работающих над проектом, зафиксирован для того же удаленного репозитория Git. Если Алиса выполняет запуск, Боб может увидеть это в разделе журнала выполнения проекта в приложении для рабочей среды. Боб может восстановить состояние выполнения проекта, включая выполнения, проведенные Алисой. 

Благодаря совместному использованию удаленного репозитория Git для проекта Алиса и Боб также могут совместно работать в главной ветви. При необходимости они также могут создать пользовательские ветви и использовать запросы на получение и объединение Git для совместной работы. 

### <a name="using-azure-portal-to-add-users"></a>Добавление пользователей с помощью портала Azure
<a name="portal"></a>

Учетные записи, рабочие области и проекты службы "Экспериментирование в Машинном обучении Azure" являются ресурсами Azure Resource Manager. Для назначения ролей можно использовать ссылку управления доступом на [портале Azure](https://portal.azure.com). 

Из представления "Все ресурсы" найдите ресурс, в который необходимо добавить пользователей. Щелкните ссылку на управление доступом (IAM) в пределах страницы. Добавление пользователей 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

