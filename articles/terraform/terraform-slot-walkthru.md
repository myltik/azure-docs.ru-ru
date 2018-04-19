---
title: Слот развертывания поставщика Terraform с Azure
description: Руководство по слотам развертывания поставщика Terraform с Azure
keywords: terraform, devops, virtual machine, Azure, deployment slots
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Использование Terraform для подготовки инфраструктуры со слотами развертывания Azure

[Слоты развертывания Azure](/azure/app-service/web-sites-staged-publishing) позволяют переключаться между различными версиями приложения (например, рабочей и промежуточной) для минимизации влияния нарушенных развертываний. В этой статье показан пример использования слотов развертывания. Мы рассмотрим развертывание двух приложений через GitHub и Azure. Одно приложение размещается в "рабочем", а второе в "промежуточном" слоте. (Названия "рабочий" и "промежуточный" произвольны и их можно изменить в зависимости от сценария.) После настройки слотов развертывания при необходимости можно использовать Terraform для переключения между двумя слотами.

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure.** Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

- **Учетная запись GitHub**. Учетная запись [GitHub](http://www.github.com) необходима для разветвления и использования тестового репозитория GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Создание и применение плана Terraform

1. Войдите на [портал Azure](http://portal.azure.com).

1. Откройте [Azure Cloud Shell](/azure/cloud-shell/overview) и выберите **Bash** в качестве среды (если это еще не сделано).

    ![Командная строка Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Перейдите в каталог `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Создайте каталог с именем `deploy`.

    ```bash
    mkdir deploy
    ```

1. Создайте каталог с именем `swap`.

    ```bash
    mkdir swap
    ```

1. Убедитесь, что оба каталога были успешно созданы, используя команду Bash `ls`.

    ![Cloud Shell после создания каталогов](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Перейдите в каталог `deploy`.

    ```bash
    cd deploy
    ```

1. С помощью [редактора VI](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html) создайте файл с именем `deploy.tf`, в котором будет содержаться [конфигурация Terraform](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Войдите в режим вставки, нажав клавишу `i`.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Нажмите клавишу **&lt;Esc>**, чтобы выйти из режима вставки.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду, а затем нажмите клавишу **&lt;ВВОД>**.

    ```bash
    :wq
    ```

1. После создания файла можно проверить его содержимое.

    ```bash
    cat deploy.tf
    ```

1. Инициализируйте Terraform.

    ```bash
    terraform init
    ```

1. Создайте план Terraform.

    ```bash
    terraform plan
    ```

1. Подготовьте ресурсы, определенные в файле конфигурации `deploy.tf`. (Чтобы подтвердить действие, введите `yes` в ответ на запрос.)

    ```bash
    terraform apply
    ```

1. Закройте окно Cloud Shell.

1. В главном меню на портале Azure выберите **Группы ресурсов**.

    ![Группы ресурсов на портале Azure](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. На вкладке **Группы ресурсов** выберите **slotDemoResourceGroup**.

    ![Группа ресурсов, созданная в Terraform.](./media/terraform-slot-walkthru/resource-group.png)

По окончании можно просмотреть все ресурсы, созданные в Terraform.

![Ресурсы, созданные в Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Разветвление тестового проекта

Прежде чем начать тестирование создания и переключения слотов развертывания, необходимо создать разветвление тестового проекта на сайте GitHub.

1. Перейдите к [репозиторию awesome-terraform на GitHub](https://github.com/Azure/awesome-terraform).

1. Выполните разветвление **репозитория awesome-terraform**.

    ![Разветвление репозитория GitHub awesome-terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. Выполните все запрашиваемые действия для создания разветвления в среде.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Развертывание из GitHub в слоты развертывания

После разветвления репозитория тестового проекта настройте слоты развертывания, выполнив следующие действия:

1. В главном меню на портале Azure выберите **Группы ресурсов**.

1. Выберите **slotDemoResourceGroup**.

1. Выберите **slotAppService**.

1. Щелкните пункт **Варианты развертывания**.

    ![Варианты развертывания для ресурса службы приложений](./media/terraform-slot-walkthru/deployment-options.png)

1. На вкладке **Вариант развертывания** выберите **Выбор источника**, а затем щелкните **GitHub**.

    ![Выбор источника развертывания](./media/terraform-slot-walkthru/select-source.png)

1. Когда Azure установит подключение и отобразятся все параметры, выберите **Авторизация**.

1. На вкладке **Авторизация** выберите **Авторизовать** и укажите учетные данные, необходимые Azure для получения доступа к вашей учетной записи GitHub. 

1. После того как Azure проверит учетные данные GitHub, отобразится сообщение, указывающее, что процесс авторизации завершен. Нажмите кнопку **ОК**, чтобы закрыть вкладку **Авторизация**.

1. Щелкните **Choose your organization** (Выбор организации) и выберите вашу организацию.

1. Щелкните **Выберите проект**.

1. На вкладке **Выбор проекта** щелкните проект **awesome-terraform**.

    ![Выбор проекта awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Щелкните **Выберите ветвь**.

1. На вкладке **Выбор ветви** щелкните **master** (главная).

    ![Выбор главной ветви](./media/terraform-slot-walkthru/choose-branch-master.png)

1. На вкладке **Вариант развертывания** нажмите кнопку **ОК**.

На этом этапе развертывание рабочего слота выполнено. Для развертывания промежуточного слота выполните все предыдущие шаги в этом разделе с учетом следующих изменений.

- На шаге 3 выберите ресурс **slotAppServiceSlotOne**.

- На шаге 13 выберите "рабочую" ветвь вместо главной.

    ![Выбор рабочей ветви](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Тестирование развертываний приложения

В предыдущих разделах вы настроили два слота (**slotAppService** и **slotAppServiceSlotOne**), чтобы выполнять развертывание из разных ветвей в GitHub. Чтобы проверить, что веб-приложения были успешно развернуты, их нужно предварительно просмотреть.

Выполните следующие действия два раза. При первом выполнении на шаге 3 выберите **slotAppService**, а при втором выполнении — **slotAppServiceSlotOne**.

1. В главном меню на портале Azure выберите **Группы ресурсов**.

1. Выберите **slotDemoResourceGroup**.

1. Выберите **slotAppService** или **slotAppServiceSlotOne**.

1. На странице обзора выберите **URL-адрес**.

    ![Выбор URL-адреса на вкладке обзора для отображения приложения](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Процесс создания и развертывания сайта в Azure через GitHub может занять несколько минут.
>
>

Для веб-приложения **slotAppService** отобразится синяя страница с заголовком **Slot Demo App 1** (Демонстрационное приложение слота 1). Для веб-приложения **slotAppServiceSlotOne** отобразится зеленая страница с заголовком **Slot Demo App 2** (Демонстрационное приложение слота 2).

![Предварительный просмотр приложений для проверки правильного развертывания](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Переключение между двумя слотами развертывания

Чтобы протестировать переключение между двумя слотами развертывания, сделайте следующее:
 
1. Перейдите на вкладку браузера, запустив **slotAppService** (приложение с синей страницей). 

1. Откройте портал Azure в отдельной вкладке.

1. Откройте Cloud Shell.

1. Перейдите в каталог **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. С помощью редактора VI создайте файл с именем `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Войдите в режим вставки, нажав клавишу `i`.

1. Скопируйте приведенный ниже код и вставьте его в редактор.

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Нажмите клавишу **&lt;Esc>**, чтобы выйти из режима вставки.

1. Сохраните файл и закройте редактор VI. Для этого введите приведенную ниже команду, а затем нажмите клавишу **&lt;ВВОД>**.

    ```bash
    :wq
    ```

1. Инициализируйте Terraform.

    ```bash
    terraform init
    ```

1. Создайте план Terraform.

    ```bash
    terraform plan
    ```

1. Подготовьте ресурсы, определенные в файле конфигурации `swap.tf`. (Чтобы подтвердить действие, введите `yes` в ответ на запрос.)

    ```bash
    terraform apply
    ```

1. После завершения процесса переключения слотов в Terraform перейдите в браузер, в котором открыто веб-приложение **slotAppService**, и обновите страницу. 

Веб-приложение в промежуточном слоте **slotAppServiceSlotOne** было переведено на рабочий слот и теперь отображается зеленым цветом. 

![Слоты развертывания переключены](./media/terraform-slot-walkthru/slots-swapped.png)

Чтобы вернуться к исходной рабочей версии приложения, повторно примените план Terraform, созданный с помощью файла конфигурации `swap.tf`.

```bash
terraform apply
```

После переключения отобразится исходная конфигурация.