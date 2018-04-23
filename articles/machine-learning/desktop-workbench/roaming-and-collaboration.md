---
title: Роуминг и совместная работа в Azure Machine Learning Workbench | Документация Майкрософт
description: Узнайте, как настроить роуминг и совместную работу в Azure Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: b587f5dcc9558ec52b85e4b53dae0e31ad475a4e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Роуминг и совместная работа в Azure Machine Learning Workbench
В этой статье описывается, как можно использовать Azure Machine Learning Workbench, чтобы настроить проекты для перемещения между компьютерами и совместной работы с участниками группы. 

При создании проекта службы "Машинное обучение Azure", в котором имеется удаленная ссылка репозитория Git, метаданные и моментальные снимки проекта хранятся в облаке. Вы можете использовать ссылку облака для получения доступа к проекту с другого компьютера (в роуминге). Вы также можете совместно работать с участниками группы, предоставив им доступ к проекту. 

## <a name="prerequisites"></a>предварительным требованиям
1. Установите приложение Azure Machine Learning Workbench. Убедитесь, что имеете доступ к учетной записи службы "Экспериментирование в Машинном обучении Azure". Дополнительные сведения см. в статье [Создание учетной записи для предварительной версии службы "Машинное обучение Azure" и установка Azure Machine Learning Workbench](../service/quickstart-installation.md).

2. Получите доступ к [Visual Studio Team Services](https://www.visualstudio.com) (Team Services) и создайте репозиторий, чтобы подключиться к проекту. Дополнительные сведения см. в статье [Использование репозитория Git в проекте Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Создание проекта в службе "Машинное обучение"
Откройте Machine Learning Workbench и создайте проект (например, проект с названием iris). В поле **URL-адреса репозитория GIT Visualstudio.com** введите допустимый URL-адрес для репозитория Git в Team Services. 

> [!IMPORTANT]
> При выборе пустого шаблона проекта в выбранном репозитории Git уже может быть главная ветвь. Служба "Машинное обучение" просто клонирует главную ветвь локально. Она добавляет папку aml_config и другие файлы метаданных проекта в папку локального проекта. 
>
> Но при выборе любого другого шаблона проекта в репозитории Git *не может* быть главной ветви. Если такая ветвь есть, появится сообщение об ошибке. В качестве альтернативы можно с помощью команды `az ml project create` создать проект с параметром `--force`. После этого файлы в исходной главной ветви удаляются и заменяются новыми файлами в выбранном шаблоне.

После создания проекта отправьте несколько потоковых выполнений на любой скрипт в рамках проекта. Это действие фиксирует состояние проекта для удаленной ветви журнала выполнений репозитория Git. 

> [!NOTE] 
> Только запуски скрипта активируют фиксации в ветвь журнала выполнения. Выполнение подготовки данных и запуски Notebook не активируют создание моментальных снимков проекта в ветви журнала выполнения.

После настройки проверки подлинности Git можно работать в главной ветви. Или же создать ветвь. 

Пример: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Роуминг
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Открытие Machine Learning Workbench на другом компьютере
После привязки репозитория Git для Team Services с проектом можно получить доступ к проекту iris с любого компьютера, на котором установлена служба Machine Learning Workbench. 

Чтобы получить доступ к проекту iris на другом компьютере, необходимо войти в приложение с теми же учетными данными, которые использовались при создании проекта. Кроме того, нужно использовать ту же учетную запись и рабочую область службы "Экспериментирование в Машинном обучении". Проект iris находится в списке проектов в рабочей области (составленном в алфавитном порядке). 

### <a name="download-the-project-on-a-second-computer"></a>Загрузка проекта на другом компьютере
При открытии рабочей области на другом компьютере значок возле проекта iris отличается от типичного значка папки. Значок скачивания указывает, что содержимое проекта находится в облаке и его можно скачать на этот компьютер. 

![Создание проекта](./media/roaming-and-collaboration/downloadable-project.png)

Выберите проект iris, чтобы начать загрузку. После скачивания можно получить доступ к проекту на другом компьютере. 

В Windows проект находится в папке C:\Users\\<имя_пользователя\>\Documents\AzureML.

В macOS проект находится в папке /home/\<имя_пользователя\>/Documents/AzureML.

В следующем выпуске мы планируем расширить функциональность, чтобы можно было выбрать папку назначения. 

> [!NOTE]
> Если в каталоге машинного обучения есть папка, имя которой совпадает с именем проекта, скачивание завершится сбоем. Чтобы обойти эту проблему, временно переименуйте имеющуюся папку.


### <a name="work-on-the-downloaded-project"></a>Работа со скачанным проектом 
В скачанном проекте отображается состояние проекта на момент его последнего запуска. Моментальный снимок состояния проекта автоматически фиксируется в ветви журнала выполнения репозитория Git для Team Services при каждом запуске. Моментальный снимок, связанный с последним запуском, используется при создании экземпляра проекта на другом компьютере. 
 

## <a name="collaboration"></a>Совместная работа
Вы можете совместно работать с участниками группы над проектами, связанными с репозиторием Git в Team Services. Вы можете назначить пользователям разрешения для учетной записи, рабочей области и проекта службы "Экспериментирование в Машинном обучении". Сейчас можно выполнять команды Azure Resource Manager с помощью Azure CLI. Кроме того, можно использовать [портал Azure](https://portal.azure.com). Дополнительные сведения см. в разделе [Использование портала Azure для добавления пользователей](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Использование командной строки для добавления пользователей
Например, Элис имеет роль владельца проекта iris и ей нужно предоставить Бобу доступ к проекту. 

Элис выбирает меню **Файл**, а затем пункт меню **Command Prompt** (Командная строка). Открывается окно командной строки с проектом iris. Затем Элис может решить, какой уровень доступа она хочет предоставить Бобу. Она предоставляет разрешения, выполнив следующие команды.  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

После назначения роли напрямую или путем наследования в списке проектов Machine Learning Workbench для Боба отобразится этот проект. Чтобы проект отобразился в списке, Бобу может понадобиться перезагрузить приложение. Затем Боб может загрузить проект, как описано в разделе [Роуминг](#roaming), и начать совместную работу с Элис. 

Журнал выполнения для всех пользователей, совместно работающих над проектом, зафиксирован для того же удаленного репозитория Git. Если Элис выполняет запуск, Боб может увидеть это в разделе журнала выполнения проекта в приложении Machine Learning Workbench. Боб также может восстановить состояние выполнения проекта, включая выполнения, проведенные Элис. 

Совместно используя удаленный репозиторий Git для проекта, Элис и Боб также могут совместно работать в главной ветви. При необходимости они также могут создать пользовательские ветви и использовать запросы на вытягивание и объединение Git для совместной работы. 

### <a name="use-the-azure-portal-to-add-users"></a>Добавление пользователей с помощью портала Azure
<a name="portal"></a>

Учетные записи, рабочие области и проекты службы "Экспериментирование в Машинном обучении" являются ресурсами Azure Resource Manager. Для назначения ролей можно использовать ссылку **управления доступом** на [портале Azure](https://portal.azure.com). 

В представлении **Все ресурсы** найдите ресурс, в который необходимо добавить пользователей. Выберите ссылку **управления доступом (IAM)**, а затем **Add users** (Добавить пользователей). 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Пример рабочего процесса совместной работы
Рассмотрим пример, демонстрирующий рабочий процесс совместной работы. Сотрудники Contoso Элис и Боб хотят совместно работать над проектом обработки и анализа данных, используя Machine Learning Workbench. Их удостоверения принадлежат одному и тому же клиенту Contoso Azure Active Directory (Azure AD). Ниже приведены действия, которые выполнили Элис и Боб.

1. Сначала Элис создает пустой репозиторий Git в проекте Team Services. Проект Team Services должен находиться в подписке Azure, созданной в клиенте Contoso Azure AD. 

2. Затем Элис создает на своем компьютере учетную запись и рабочую область службы "Экспериментирование в Машинном обучении", а также проект Machine Learning Workbench. При создании проекта она вводит URL-адрес репозитория Git в Team Services.

3. Элис начинает работать над проектом. Она создает несколько скриптов и выполняет несколько запусков. Во время каждого запуска в ветвь журнала выполнения репозитория Git в проекте Team Services, созданного Machine Learning Workbench, автоматически помещается моментальный снимок всей папки проекта репозитория для фиксации изменений.

4. Элис довольна ходом работы. Она хочет зафиксировать изменения в локальной главной ветви и передать их в главную ветвь репозитория Git в проекте Team Services. Открыв проект в Machine Learning Workbench, она открывает окно командной строки, а затем вводит следующие команды.
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Элис добавляет в рабочую область Боба в качестве участника. Она может сделать это на портале Azure или с помощью команды `az role assignment`, как было показано ранее. Элис также предоставляет Бобу разрешения на чтение и запись для репозитория Git в Team Services.

6. Боб входит в Machine Learning Workbench на своем компьютере. Он видит рабочую область, в которой может совместно работать с Элис. В этой рабочей области он видит проект iris. 

7. Боб выбирает имя проекта. Проект загружается на его компьютер.
    * Загруженные файлы проекта представляют собой копии моментального снимка последнего запуска, записанного в журнал выполнения. Они не являются последней фиксацией в главную ветвь.
    * Локальная папка проекта задается в главной ветви с неиндексированными изменениями.

8. Боб может просматривать запуски, которые выполнила Элис. Он может также восстановить моментальные снимки любого из предыдущих запусков.

9. Бобу нужно получить последние изменения, выполненные Элис, а затем начать работу в другой ветви. Он открывает окно командной строки в Machine Learning Workbench и выполняет следующие команды.

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Боб вносит изменения в проект и отправляет новые запуски. Изменения вносятся в ветвь bob. Теперь Элис тоже видит запуски Боба.

11. Боб может отправлять свои изменения в удаленный репозиторий Git. Чтобы избежать конфликта с главной ветвью, в которой работает Элис, он отправляет свою работу в новую удаленную ветвь, которая также называется bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Боб может сообщить Элис о новом фокусе в своем коде. Он создает запрос на вытягивание в удаленном репозитории Git из ветви bob в главную ветвь. Затем Элис может присоединить запрос на вытягивание к главной ветви.

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения см. в статье [Использование репозитория Git в проекте Azure Machine Learning Workbench](using-git-ml-project.md).
