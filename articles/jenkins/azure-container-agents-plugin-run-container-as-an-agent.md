---
title: Создание проекта в Azure с использованием Jenkins и службы "Экземпляры контейнеров Azure"
description: Узнайте, как использовать подключаемый модуль агента контейнеров Azure для Jenkins, чтобы выполнить сборку проекта в Azure с помощью службы "Экземпляры контейнеров Azure"
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: a18136afc096f8315310fae134d3f683994b6d0a
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Создание проекта в Azure с использованием Jenkins и службы "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" упрощает работу, избавляя от необходимости подготавливать виртуальные машины или применять службу более высокого уровня. Счета за использование этой службы выставляются на основе посекундной тарификации в зависимости от требуемой емкости. Поэтому она является лучшим вариантом для временных рабочих нагрузок, таких как выполнение сборки.

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Установить и настроить сервер Jenkins в Azure.
> * Установить и настроить подключаемый модуль агентов контейнеров Azure для Jenkins.
> * Использовать службу "Экземпляры контейнеров Azure" для создания [примера приложения Spring PetClinic](https://github.com/spring-projects/spring-petclinic).

## <a name="prerequisites"></a>предварительным требованиям

- **Подписка Azure**. Дополнительные сведения о вариантах приобретения Azure см. на страницах [Как приобрести Azure](https://azure.microsoft.com/pricing/purchase-options/) и [Создайте бесплатную учетную запись Azure уже сегодня](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI 2.0 или Azure Cloud Shell**. Установите один из следующих продуктов, в котором вы будете выполнять команды Azure:

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest). Позволяет выполнять команды Azure из окна командной строки или окна терминала.
    - [Azure Cloud Shell](/azure/cloud-shell/quickstart.md). Оболочка на основе браузера. Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Установка сервера Jenkins в Azure с помощью образа Jenkins из Marketplace

Jenkins поддерживает модель, в которой делегаты сервера Jenkins работают с одним или несколькими агентами, чтобы позволить одной установке Jenkins размещать большое количество проектов или предоставлять различные среды, необходимые для сборки или тестирования. Шаги, представленные в этом разделе, помогут вам установить и настроить сервер Jenkins в Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Подключение к серверу Jenkins, работающем в Azure

Когда вы установите сервер Jenkins в Azure, необходимо подключиться к нему. Ниже приведены шаги по настройке SSH-подключения к виртуальной машине Jenkins, работающей в Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Обновление службы доменных имен (DNS) Jenkins

Jenkins должен знать собственный URL-адрес, когда он создает указывающие на него ссылки. Например, Jenkins нужно использовать URL-адрес, когда он отправляет сообщения электронной почты, содержащие прямые ссылки на результаты сборки. 

В этом разделе описывается настройка URL-адреса Jenkins.

1. В браузере перейдите на панель мониторинга Jenkins по адресу `http://localhost:8080`.

1. Выберите **Manage Jenkins** (Управление Jenkins).

    ![Параметры управления Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **Configure System** (Настройка системы).

    ![Параметр управления подключаемыми модулями на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. В разделе **Jenkins Location** (Расположение Jenkins) введите URL-адрес сервера Jenkins.

1. Щелкните **Сохранить**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Обновление Jenkins с целью разрешить протокол JNLP

> [!NOTE]
> В этом руководстве объясняется, как настроить агент JNLP. [Подключаемый модуль Jenkins Azure Container Agents](https://plugins.jenkins.io/azure-container-agents) начиная с версии 0.4 также поддерживает SSH.
>
>

Агент Jenkins подключается к серверу Jenkins через протокол Java Network Launch Protocol (JNLP). В этом разделе объясняется, как указать порт, используемый агентами JNLP при взаимодействии с сервером Jenkins.

1. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins).

    ![Параметры управления Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **Configure Global Security** (Настройка глобальной безопасности).

    ![Параметр настройки глобальной безопасности на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. В разделе **Agents** (Агенты) выберите **Fixed** (Фиксированный) и введите номер порта. На снимке экрана в качестве примера показано значение порта 12345. Вам нужно указать порт, который подходит для вашей среды.

    ![Обновление параметров глобальной безопасности Jenkins с целью разрешить протокол JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Щелкните **Сохранить**.

1. Чтобы создать правило входящих подключений для вашей группы безопасности сети Jenkins, введите следующую команду в Azure CLI 2.0 или Azure Cloud Shell:

    ```azurecli
    az network nsg rule create  \
    --resource-group JenkinsResourceGroup \
    --nsg-name JenkinsNSG  \
    --name allow-https \
    --description "Allow access to port 12345 for HTTPS" \
    --access Allow \
    --protocol Tcp  \
    --direction Inbound \
    --priority 102 \
    --source-address-prefix "*"  \
    --source-port-range "*"  \
    --destination-address-prefix "*" \
    --destination-port-range "12345"
    ```

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Создание и добавление субъекта-службы Azure в учетные данные Jenkins

Для развертывания в Azure требуется субъект-служба Azure. Ниже приведены шаги по созданию субъекта-службы (если у вас его еще нет) и добавления его для Jenkins.

1. Если у вас уже имеется субъект-служба (и вы знаете его идентификатор подписки, приложения, клиент и пароль), этот шаг можно пропустить. Сведения о создании субъекта безопасности см. в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). При создании субъекта запишите следующие значения: идентификатор подписки, клиент, идентификатор приложения и пароль.

1. Выберите **Credentials** (Учетные данные).

    ![Параметр управления учетными данными на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. В разделе **Credentials** (Учетные данные) выберите **System** (Система).

    ![Параметр управления системными учетными данными на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Выберите **Global credentials (unrestricted)** (Глобальные учетные данные (неограниченные)).

    ![Параметр управления глобальными системными учетными данными на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Выберите **Adding some credentials** (Добавление учетных данных).

    ![Добавление учетных данных на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. Чтобы на странице отобразились поля для добавления субъекта-службы, в раскрывающемся списке **Kind** (Вид) выберите **Microsoft Azure Service Principal** (Субъект-служба Microsoft Azure). Затем укажите необходимые значения следующим образом:

    - **Scope** (Область). Выберите параметр **Global (Jenkins, nodes, items, all child items, etc.)** (Глобальная (Jenkins, узлы, элементы, все дочерние элементы и т. д.)).
    - **Subscription ID** (Идентификатор подписки). Введите идентификатор подписки Azure, указанной при запуске `az account set`.
    - **Client ID** (Идентификатор клиента). Укажите значение `appId`, полученное от `az ad sp create-for-rbac`.
    - **Client Secret** (Секрет клиента). Укажите значение `password`, полученное от `az ad sp create-for-rbac`.
    - **Tenant ID** (Идентификатор клиента). Укажите значение `tenant`, полученное от `az ad sp create-for-rbac`.
    - **Azure Environment** (Среда Azure). Выберите `Azure`.
    - **ID** (Идентификатор). Укажите `myTestSp`. Это значение позже еще раз будет использовано в данном руководстве.
    - **Description** (Описание). (Необязательно.) Введите описание для этого субъекта.

    ![Указание новых свойств субъекта-службы на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. Когда вы введете информацию, определяющую субъект-службу, можно выбрать пункт **Verify Service Principal** (Проверить субъект-службу), чтобы убедиться, что все работает правильно. Если субъект-служба определена правильно, появится сообщение Successfully verified the Microsoft Azure Service Principal (Субъект-служба Microsoft Azure успешно проверена) под полем **Description** (Описание).

1. После этого нажмите кнопку **ОК**, чтобы добавить субъект в Jenkins. На панели мониторинга Jenkins добавленный субъект отобразится на странице **Global Credentials** (Глобальные учетные данные).

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Создание группы ресурсов Azure для экземпляров контейнеров Azure

Экземпляры контейнеров Azure должны находиться в группе ресурсов Azure. Группа ресурсов Azure — это контейнер, содержащий связанные ресурсы для решения Azure.

Чтобы создать группу ресурсов с именем `JenkinsAciResourceGroup` в расположении `eastus`, введите следующую команду в Azure CLI 2.0 или Cloud Shell:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

После этого команда `az group create` выведет результаты, подобные следующим:

```JSON
{
  "id": "/subscriptions/<subscriptionId>/resourceGroups/JenkinsAciResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "JenkinsAciResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Установка подключаемого модуля агентов контейнеров Azure для Jenkins

Если вы уже установили подключаемый модуль агентов контейнеров Azure, этот раздел можно пропустить.

После создания группы ресурсов Azure для агента Jenkins установите подключаемый модуль агентов контейнеров Azure, как показано ниже:

1. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins).

    ![Параметры управления Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **Manage Plugins** (Управление подключаемыми модулями).

    ![Параметр управления подключаемыми модулями на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Выберите **Available** (Доступно).

    ![Параметр просмотра доступных подключаемых модулей Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Введите `Azure Container Agents` в текстовое поле **Filter** (Фильтр). (Список фильтруется по мере ввода текста.)

    ![Фильтрация доступных подключаемых модулей Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Установите флажок рядом с подключаемым модулем **Azure Container Agents** (Агенты контейнеров Azure) и одним из параметров установки. Для этого демонстрационного проекта мы выбрали параметр **Install without restart** (Установка без перезагрузки).

    ![Установка подключаемых модулей агентов контейнеров Azure на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  После выбора параметра для установки нужного подключаемого модуля на панели мониторинга Jenkins отобразится страница с описанием состояния установки.

    ![Состояние установки подключаемых модулей агентов контейнера Azure на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Чтобы вернуться на главную страницу панели мониторинга Jenkins, выберите **Go back to the top page** (Вернуться на главную страницу).

## <a name="configure-the-azure-container-agents-plugin"></a>Настройка подключаемого модуля агентов контейнеров Azure

После установки подключаемого модуля агентов контейнеров Azure настройте подключаемый модуль на панели мониторинга Jenkins, как показано в этом разделе.

1. На панели мониторинга Jenkins выберите **Manage Jenkins** (Управление Jenkins).

    ![Параметры управления Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **Configure System** (Настройка системы).

    ![Параметр управления подключаемыми модулями на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. В нижней части страницы найдите раздел **Cloud**(Облако). Затем в раскрывающемся списке **Add a new cloud** (Добавить новое облако) выберите **Azure Container Instance** (Экземпляр контейнера Azure).

    ![Добавление нового поставщика облачных служб на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. В разделе **Azure Container Instance** (Экземпляр контейнера Azure) укажите следующие значения:

    - **Cloud name** (Имя облака). (Необязательно, так как по умолчанию это значение создается автоматически.) Укажите имя для этого экземпляра. 
    - **Azure Credential** (Учетные данные Azure). Щелкните стрелку раскрывающегося списка, а затем выберите запись `myTestSp`, которая идентифицирует субъект-службу Azure, которую вы создали ранее.
    - **Resource Group** (Группа ресурсов). Щелкните стрелку раскрывающегося списка, а затем выберите запись `JenkinsAciResourceGroup`, которая идентифицирует группу ресурсов экземпляра контейнера Azure, созданную ранее.

    ![Определение свойств экземпляра контейнера Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Щелкните стрелку раскрывающегося списка **Add Container Template** (Добавить шаблон контейнера), а затем выберите **Aci Container Template** (Шаблон контейнера ACI).

1. В разделе **Aci container Template** (Шаблон контейнера ACI) укажите следующие значения:

    - **Name** (Имя). Введите `ACI-container`.
    - **Labels** (Метки). Введите `ACI-container`.
    - **Docker Image** (Образ Docker). Введите `cloudbees/jnlp-slave-with-java-build-tools`.

    ![Определение свойств образа экземпляра контейнера Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Нажмите кнопку **Advanced** (Дополнительно).

1. Щелкните стрелку раскрывающегося списка **Retention Strategy** (Стратегия удержания) и выберите **Container Idle Retention Strategy** (Стратегия удержания при простое контейнера). Если выбрать этот параметр, Jenkins поддерживает работу агента до тех пор, пока на нем не будет выполнено новое задание или не истечет указанное время простоя.

    ![Определение стратегии удержания для экземпляра контейнера Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Щелкните **Сохранить**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Создание задания приложения PetClinic Spring в Jenkins

Ниже приведены шаги по созданию задания Jenkins (в качестве универсального проекта), чтобы выполнить сборку приложения Spring PetClinic.

1. На панели мониторинга Jenkins выберите **New Item**(Создать элемент).

    ![Пункт меню создания элемента на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. В поле имени элемента введите `myPetClinicProject` и выберите **Freestyle project** (Универсальный проект).

    ![Создание универсального проекта на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Нажмите кнопку **ОК**.

1. На вкладке **General**(Общие) укажите следующие значения:

    - **Restrict where project can be run** (Ограничить выполнение проекта). Выберите этот параметр.
    - **Label Expression** (Выражение метки). Введите `ACI-container`. Когда вы выйдете из поля, появится сообщение, подтверждающее обработку метки конфигурацией облака, созданной на предыдущем шаге.

    ![Общие параметры для нового универсального проекта на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Перейдите на вкладку **Source Code Management** (Управление исходным кодом) и укажите следующие значения:

    - **Source Code Management** (Управление исходным кодом). Выберите **Git**.
    - **Repository URL** (URL-адрес репозитория). Введите следующий URL-адрес репозитория GitHub для примера приложения Spring PetClinic: `https://github.com/spring-projects/spring-petclinic.git`.

1. Перейдите на вкладку **Build** (Сборка) и выполните следующие задачи:

    - Щелкните стрелку раскрывающегося списка **Add build step** (Добавить этап сборки) и выберите **Invoke top-level Maven targets** (Вызов целей верхнего уровня Maven).

    - В поле **Goals** (Цели) укажите `package`.

1. Чтобы сохранить определение нового проекта, выберите **Save** (Сохранить).

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Создание задания приложения PetClinic Spring в Jenkins

Пора выполнить сборку проекта! В этом разделе объясняется, как создать проект на панели мониторинга Jenkins.

1. На панели мониторинга Jenkins выберите `myPetClinicProject`.

    ![Выберите проект для создания на панели мониторинга Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Выберите **Build now** (Собрать). 

    ![Создание проекта на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. При запуске сборки в Jenkins она помещается в очередь. Если вы используете агент контейнера Azure, сборка не может быть запущена до тех пор, пока агент не будет запущен и подключен. Если это не будет выполнено, вы увидите сообщение с именем агента и информацией о том, что сборка находится в ожидании. (Этот процесс занимает около пяти минут, однако он необходим только при первом использовании агента для сборки. Последующие сборки будут выполняться гораздо быстрее, так как на тот момент агент будет подключен.)

    ![Пока агент не создан и не подключен, сборка помещается в очередь.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    После запуска сборки появится индикатор выполнения, указывающий, что сборка выполняется:

    ![Если появился индикатор выполнения, значит сборка выполняется.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Когда индикатор выполнения исчезнет, щелкните стрелку рядом с номером сборки. В контекстном меню выберите пункт **Console output** (Выходные данные консоли).

    ![Чтобы просмотреть сведения о сборке, выберите пункт меню Console Log (Журнал консоли).](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Сведения журнала консоли создаются в процессе сборки. Чтобы просмотреть все сведения о сборке (включая сведения о выполнении сборки удаленно на созданном агенте Azure), выберите **Full log** (Полный журнал).

    ![Щелкните ссылку Full log (Полный журнал), чтобы просмотреть более подробные сведения о сборке.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    В полном журнале содержатся более подробные сведения о сборке:

    ![В полном журнале содержатся более подробные сведения о сборке.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Чтобы просмотреть расположение сборки, прокрутите до нижней части журнала.

    ![Расположение сборки отображается в нижней части журнала сборки.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Очистка ресурсов Azure

Следуя указаниям в этом руководстве, вы создали ресурсы, содержащиеся в двух группах ресурсов Azure: 
    - `JenkinsResourceGroup` — ресурсы Azure для сервера Jenkins.
    - `JenkinsAciResourceGroup` — ресурсы Azure для агента Jenkins.
    
Если вам больше не нужно использовать ресурсы в группе ресурсов Azure, вы можете удалить группу ресурсов с помощью команды `az group delete` следующим образом (замените заполнитель &lt;resourceGroup> на имя группы ресурсов, которую нужно удалить):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Jenkins в Azure](https://docs.microsoft.com/azure/jenkins/)