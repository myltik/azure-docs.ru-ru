---
title: "Построение проекта в Azure с помощью Jenkins и экземпляров контейнера Azure"
description: "Сведения об использовании для построения проекта в Azure с экземплярами Azure контейнера подключаемый модуль агента контейнера Azure для Jenkins"
services: multiple
documentationcenter: 
author: tomarcher
manager: rloutlaw
editor: 
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/11/2017
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 269e936cb79ba4138285f5dbd326413d70d5924d
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/13/2017
---
# <a name="build-a-project-in-azure-using-jenkins-and-azure-container-instances"></a>Построение проекта в Azure с помощью Jenkins и экземпляров контейнера Azure

Экземпляры контейнером Azure позволяет легко получения и выполнение без подготовки виртуальных машин и применять более высокого уровня службы. Экземпляры контейнером Azure предоставляет секунду выставления счетов на основе мощности, что нужно; сделать привлекательным вариантом для временных рабочих нагрузок, например выполнение сборки.

Вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * Установка и настройка сервера Jenkins в Azure
> * Установка и настройка агентов контейнера Azure подключаемый модуль для Jenkins
> * Использовать для создания экземпляров контейнера Azure [Spring PetClinic примера приложения](https://github.com/spring-projects/spring-petclinic)

## <a name="prerequisites"></a>Технические условия

- **Подписка Azure** : чтобы получить сведения о вариантах приобретения Azure см. в разделе [приобретение Azure](https://azure.microsoft.com/pricing/purchase-options/) или [бесплатной пробной версии один месяц](https://azure.microsoft.com/pricing/free-trial/).

- **Azure CLI 2.0 или Azure облачной оболочку** -установить один из следующих продуктов, в который необходимо вводить команды Azure:

    - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest) -позволяет выполнять команды Azure из команды или окно терминала.
    - [Azure облачной оболочки](/azure/cloud-shell/quickstart.md) — на основе браузера оболочки взаимодействия. Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure.

## <a name="install-a-jenkins-server-on-azure-using-the-jenkins-marketplace-image"></a>Установка сервера Jenkins на Azure с помощью образа Jenkins Marketplace

Jenkins поддерживает модели, где Jenkins, сервер делегирует работу одного или нескольких агентов один Jenkins установки для размещения большом количестве проектов или для предоставления различных средах, необходимые для построения или тесты. В этом разделе представлены инструкции по установке и настройке сервера Jenkins в Azure.

[!INCLUDE [jenkins-install-from-azure-marketplace-image](../../includes/jenkins-install-from-azure-marketplace-image.md)]

## <a name="connect-to-the-jenkins-server-running-on-azure"></a>Подключиться к серверу Jenkins, работающих в Azure

После установки Jenkins в Azure, необходимо подключиться к Jenkins. Следующие шаги содержат пошаговые инструкции по настройке SSH-подключения к виртуальной машине Jenkins, работающих в Azure. 

[!INCLUDE [jenkins-connect-to-jenkins-server-running-on-azure](../../includes/jenkins-connect-to-jenkins-server-running-on-azure.md)]

## <a name="update-jenkins-dns"></a>Обновление DNS Jenkins

Jenkins должен знать свой URL-адрес при создании ссылок, указывающих обратно на себя. Например URL-адрес должен использоваться при Jenkins отправляет сообщения электронной почты, содержащее прямые ссылки на результаты построения. 

В этом разделе описывается задание Jenkins URL-адреса.

1. В браузере перейдите к панели мониторинга Jenkins на `http://localhost:8080`.

1. Выберите **управления Jenkins**.

    ![Управление параметрами Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **настройки системы**.

    ![Управление Jenkins подключаемых модулей параметр на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. В разделе **Jenkins расположение**, введите URL-адрес сервера Jenkins.

1. Щелкните **Сохранить**.

## <a name="update-jenkins-to-allow-java-network-launch-protocol-jnlp"></a>Обновление Jenkins, чтобы разрешить протокол запуска сети Java (JNLP)

Jenkins агент подключается к серверу Jenkins через Java сети запуска протокола (JNLP). В этом разделе описывается Указание порта для JNLP агентов, используемых при взаимодействии с сервера Jenkins.

1. Выберите в панели мониторинга Jenkins **управления Jenkins**.

    ![Управление параметрами Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **Настройка глобальных безопасности**.

    ![Настройка глобальных безопасности в панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-global-security.png)

1. В разделе **агенты**выберите **Fixed**и введите порт. На снимке экрана показано значение порта 12345 в качестве примера. Следует указать порт, который подходит для вашей среды.

    ![Обновить параметры безопасности глобального Jenkins, чтобы разрешить JNLP](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-set-jnlp.png)

1. Щелкните **Сохранить**.

1. С помощью Azure CLI 2.0 или оболочки облака, введите следующую команду, чтобы создать правило для вашей группе безопасности сети Jenkins:

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

## <a name="create-and-add-an-azure-service-principal-to-the-jenkins-credentials"></a>Создание и Добавление участника службы Azure Jenkins учетные данные

Для развертывания в Azure требуется субъект-служба Azure. Следующие шаги описывают процесс создания участника службы (если у вас еще нет один) и обновление Jenkins с субъектом-службой.

1. Если уже имеется участника службы (и знать идентификатор подписки, клиента, appId и пароль), этот шаг можно пропустить. Если необходимо создать субъект безопасности, обратитесь к статье [создании субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). При создании основного сервера, запишите значения для идентификатора подписки, клиента, appId и пароль.

1. Выберите **учетные данные**.

    ![Управление параметр учетные данные на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials.png)

1. В разделе **учетные данные**выберите **системы**.

    ![Управление системный параметр учетные данные на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-system.png)

1. Выберите **глобальные учетные данные (без ограничений)**.

    ![Управление глобальной системе параметр учетные данные на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-credentials-global.png)

1. Выберите **Добавление некоторые учетные данные**.

    ![Добавьте учетные данные на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-adding-credentials.png)

1. В **вид** раскрывающегося списка выберите **субъекта-службы Microsoft Azure** заставить страница для отображения поля, присущие Добавление участника службы. Затем укажите необходимые значения следующим образом:

    - **Область** -выберите параметр для **Global (Jenkins, узлы, элементы, все дочерние элементы и т. д.)** .
    - **Идентификатор подписки** -использовать идентификатор подписки Azure, указанной при запуске `az account set`.
    - **Идентификатор клиента** -используйте `appId` значение, возвращенное `az ad sp create-for-rbac`.
    - **Секрет клиента** -используйте `password` значение, возвращенное `az ad sp create-for-rbac`.
    - **Идентификатор клиента** -используйте `tenant` значение, возвращенное `az ad sp create-for-rbac`.
    - **Среду Azure** — выберите `Azure`.
    - **Идентификатор** -введите `myTestSp`. Это значение используется позже в этом учебнике.
    - **Описание** (необязательно) введите описание значение для этого участника.

    ![Укажите новые свойства участника службы на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-principal-properties.png)

1. После ввода сведения, указывающие основного сервера, при необходимости можно выбрать **проверить участника-службы** чтобы убедиться, что все работает правильно. Если участника службы к правильно определен, появится сообщение о том, «успешно проверена субъекта-службы Microsoft Azure.» ниже **описание** поля.

1. Когда вы закончите, выберите **ОК** для добавления участника к Jenkins. На панели мониторинга Jenkins отображается вновь добавленный участника на **учетные данные глобального** страницы.

## <a name="create-an-azure-resource-group-for-your-azure-container-instances"></a>Создание группы ресурсов Azure для экземпляров контейнера Azure

Экземпляры контейнером Azure должны находиться в группе ресурсов Azure. Группа ресурсов Azure — это контейнер, содержащий связанные ресурсы для решения Azure.

С помощью Azure CLI 2.0 или оболочки облака, введите следующую команду, чтобы создать группу ресурсов под названием `JenkinsAciResourceGroup` в расположении `eastus`:

```azurecli
az group create --name JenkinsAciResourceGroup --location eastus
```

После завершения `az group create` команда отображает результаты, аналогичные следующему примеру:

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

## <a name="install-the-azure-container-agents-plugin-for-jenkins"></a>Установите агенты Azure контейнера подключаемый модуль для Jenkins

Если подключаемый модуль агенты контейнера Azure уже установлены, этот раздел можно пропустить.

При наличии группы ресурсов Azure, созданные для агента Jenkins, ниже показано, как установить подключаемый модуль Azure контейнера агенты:

1. Выберите в панели мониторинга Jenkins **управления Jenkins**.

    ![Управление параметрами Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **управление подключаемыми модулями**.

    ![Управление Jenkins подключаемых модулей параметр на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-plugins.png)

1. Выберите **доступных**.

    ![Просмотр доступных параметров Jenkins подключаемых модулей в панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-view-available-plugins.png)

1. Введите `Azure Container Agents` в **фильтра** текстовое поле. (Список фильтров при вводе текста).

    ![Фильтрация доступных подключаемых модулей Jenkins, на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-filter-available-plugins.png)

1. Установите флажок рядом с **агенты контейнера Azure** подключаемого модуля и один из параметров установки. В целях этой демонстрации я выбрал **установка без перезапуска** параметр.

    ![Установка подключаемых модулей агентов контейнера Azure с помощью панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin.png)

1.  Выбрав параметр для установки требуемой подключаемые модули, панели мониторинга Jenkins отображает страницу с подробным описанием состояния выполняется установка.

    ![Состояние установки установки подключаемых модулей агентов контейнера Azure из панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-install-aks-agent-plugin-confirmation.png)

    Чтобы вернуться на главную страницу панели мониторинга Jenkins, выберите **вернитесь на страницу верхнего**.

## <a name="configure-the-azure-container-agents-plugin"></a>Настроить подключаемый модуль Azure контейнера агентов

После установки агентов Azure контейнер подключаемого модуля в этом разделе поможет настроить подключаемый модуль в пределах панели мониторинга Jenkins.

1. Выберите в панели мониторинга Jenkins **управления Jenkins**.

    ![Управление параметрами Jenkins на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-manage-jenkins.png)

1. Выберите **настройки системы**.

    ![Управление Jenkins подключаемых модулей параметр на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-configure-system.png)

1. Найдите **облака** раздел в нижней части страницы, а также из **Добавление нового облака** раскрывающегося списка выберите **экземпляр контейнера Azure**.

    ![Добавить новый поставщик облака на информационной панели Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-cloud-provider.png)

1. В **экземпляр контейнера Azure** статьи, укажите следующие значения:

    - **Имя облака** (необязательно, как это значение по умолчанию для автоматически создаваемого имени.) Укажите имя для данного экземпляра. 
    - **Учетных данных Azure** — щелкните стрелку раскрывающегося списка, а затем выберите `myTestSp` запись, которая идентифицирует участника службы Azure было создано ранее.
    - **Группа ресурсов** — щелкните стрелку раскрывающегося списка, а затем выберите `JenkinsAciResourceGroup` запись, которая идентифицирует группу ресурсов экземпляр контейнера Azure было создано ранее.

    ![Определение свойств экземпляр контейнера Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-properties.png)

1. Выберите **Добавление шаблона контейнера** стрелку раскрывающегося списка и выберите **Aci контейнера шаблона**.

1. В **Aci контейнера шаблона** статьи, укажите следующие значения:

    - **Имя** -введите `ACI-container`.
    - **Метки** -введите `ACI-container`.
    - **Образ docker** -ввод`cloudbees/jnlp-slave-with-java-build-tools`

    ![Определение свойств изображения экземпляр контейнера Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-image-properties.png)

1. Нажмите кнопку **Advanced** (Дополнительно).

1. Выберите **стратегии хранения** стрелку раскрывающегося списка и выберите **стратегии хранения простоя контейнер**. При выборе этого параметра, Jenkins сохраняет агент резервного копирования до нового задания не выполняется на агенте и по истечении указанного времени простоя.

    ![Определение стратегии хранения экземпляр контейнера Azure](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-aci-retention-strategy.png)

1. Щелкните **Сохранить**.

## <a name="create-the-spring-petclinic-application-job-in-jenkins"></a>Создать задание приложения PetClinic Spring в Jenkins

Следующие шаги пошаговый процесс создания задание Jenkins — как проект свободном стиле — для построения приложения PetClinic Spring.

1. Выберите в панели мониторинга Jenkins **новый элемент**.

    ![Новый параметр элемента меню в панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-item.png)

1. Введите `myPetClinicProject` имя элемента, а затем выберите **проекта в свободном стиле**.

    ![Новый проект в свободном стиле, в панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project.png)

1. Нажмите кнопку **ОК**.

1. Выберите **Общие** и укажите следующие значения:

    - **Ограничения, где проект может работать** -выберите этот параметр.
    - **Метка выражение** -введите `ACI-container`. При выходе из поля, отображается сообщение, подтверждающее, что метка обслуживается конфигурации облака, созданные на предыдущем шаге.

    ![Общие параметры для нового проекта в свободном стиле на панели мониторинга Jenkins](./media/azure-container-agents-plugin-run-container-as-an-agent/jenkins-dashboard-new-freestyle-project-general.png)

1. Выберите **управление исходным кодом** и укажите следующие значения:

    - **Исходный код управления** — выберите **Git**.
    - **URL-адрес репозитория** -введите следующий URL-адрес для Spring PetClinic образца приложения в репозитории GitHub: `https://github.com/spring-projects/spring-petclinic.git`.

1. Выберите **построения** вкладку, а также выполнять следующие задачи:

    - Выберите **добавить шаг сборки** стрелку раскрывающегося списка и выберите **вызова целевых объектов верхнего уровня Maven**.

    - Для **целей**, введите `package`.

1. Выберите **Сохранить** сохранить новое определение проекта.

## <a name="build-the-spring-petclinic-application-job-in-jenkins"></a>Задание приложения PetClinic Spring в Jenkins сборки

Пора выполнить сборку проекта! В этом разделе объясняется, как сборка проекта с помощью панели мониторинга Jenkins.

1. В панели мониторинга Jenkins выберите `myPetClinicProject`.

    ![Выберите проект для сборки с помощью панели мониторинга Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/select-project-to-build.png)

1. Выберите **сборки теперь**. 

    ![Постройте проект с помощью панели мониторинга Jenkins.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-project.png)

1. При запуске сборки в Jenkins сборка помещается в очередь. В случае использования агента контейнера Azure сборки нельзя выполнить до работы и перевести в оперативный режим агент контейнера Azure. До тех пор появится сообщение, указывающее имя агента и тот факт, что ожидается сборки. (Этот процесс занимает около пяти минут, но необходим только при первом использовании агента для сборки. Последующие построения будут гораздо быстрее, так как агент находится в оперативном режиме на данный момент.)

    ![Построение помещается в очередь, пока агент будет создана и перевести в оперативный режим.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-pending.png)

    После начала построения, этот компонент полюса индикатор указывает на выполнение построения:

    ![Как только вы увидите этот компонент полюса индикатор выполнение построения.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-running.png)

1. Когда исчезнет индикатор выполнения полюса этот компонент, щелкните стрелку рядом с номером сборки. В контекстном меню выберите **вывод на консоль**.

    ![Пункт меню журнала консоли для просмотра сведений о сборке.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-menu.png)

1. Сведения журнала консоли, создаваемые в процессе построения. Чтобы просмотреть все сведения о сборке (включая сведения о построении выполняется удаленно на агенте Azure, вы создали), выберите **полного журнала**.

    ![Щелкните ссылку переполнения журнала, чтобы просмотреть более подробные сведения о сборке.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log.png)

    Полный журнал отображаются более подробные сведения о сборке:

    ![Переполнения журнала отображаются более подробные сведения о сборке.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-console-log-full.png)
    
1. Чтобы просмотреть расположения сборки, прокрутите вниз журнала.

    ![Отображает расположение сборки в нижней части журнала построения.](./media/azure-container-agents-plugin-run-container-as-an-agent/build-disposition.png)

## <a name="clean-up-azure-resources"></a>Очистка ресурсов Azure

В этом учебнике вы создали ресурсы, содержащиеся в две группы ресурсов Azure: 
    - `JenkinsResourceGroup`— Содержит ресурсы Azure для сервера Jenkins.
    - `JenkinsAciResourceGroup`— Содержит ресурсы Azure для агента Jenkins.
    
Если больше не нужно использовать какие-либо ресурсы в группе ресурсов Azure, можно удалить группу ресурсов с помощью `az group delete` следующим образом (замена &lt;группа ресурсов > заполнитель с именем для группы ресурсов DELETE):

```azurecli
az group delete -n <resourceGroup>
```

## <a name="next-steps"></a>Дальнейшие действия
> [!div class="nextstepaction"]
> [Посетите Jenkins в центр Azure, чтобы увидеть последние статьи и образцы](https://docs.microsoft.com/en-us/azure/jenkins/)