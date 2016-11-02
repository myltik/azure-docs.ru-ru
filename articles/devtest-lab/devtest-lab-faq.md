<properties
    pageTitle="Часто задаваемые вопросы об Azure DevTest Labs | Microsoft Azure"
    description="Ответы на часто задаваемые вопросы об Azure DevTest Labs"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>


# <a name="azure-devtest-labs-faq"></a>Часто задаваемые вопросы об Azure DevTest Labs

В этой статье содержатся ответы на некоторые самые распространенные вопросы о Azure DevTest Labs.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Общие сведения
- [Мне не удалось найти ответ на свой вопрос. Что делать?](#what-if-my-question-isnt-answered-here)
- [Почему следует использовать Azure DevTest Labs?](#why-should-i-use-azure-devtest-labs) 
- [Что означает "самообслуживание без проблем"?](#what-does-quotworry-free-self-servicequot-mean)
- [Как можно использовать Azure DevTest Labs?](#how-can-i-use-azure-devtest-labs) 
- [Как выставляются счета за использование Azure DevTest Labs?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Безопасность 
- [Какие уровни безопасности предусмотрены в Azure DevTest Labs?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Как создать роль, позволяющую пользователям выполнять конкретную задачу?](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="ci/cd-integration-&-automation"></a>Интеграция и автоматизация средств непрерывной интеграции и доставки (CI/CD) 
 
- [Можно ли интегрировать Azure DevTest Labs с собственным набором инструментов CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Виртуальные машины 
 
- [Почему в колонке "Виртуальные машины Azure" не отображаются все виртуальные машины, доступные в Azure DevTest Labs?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [В чем разница между пользовательскими образами и формулами?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Как создать несколько виртуальных машин с помощью одного шаблона?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Как переместить имеющиеся виртуальные машины Azure в лабораторию Azure DevTest Labs?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [Можно ли присоединить к виртуальной машине несколько дисков?](#can-i-attach-multiple-disks-to-my-vms) 
- [Как автоматизировать процесс передачи VHD-файлов для создания пользовательских образов?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Как автоматизировать процесс удаления всех виртуальных машин в моей лаборатории?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Артефакты 
 
- [Что такое артефакты?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Настройка лаборатории 
 
- [Как создать лабораторию на основе шаблона Azure Resource Manager?](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Почему виртуальные машины создаются в разных группах ресурсов с произвольными именами? Можно ли переименовать или изменить эти группы ресурсов?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Сколько лабораторий можно создать в рамках одной подписки?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Сколько виртуальных машин можно создать на лабораторию?](#how-many-vms-can-i-create-per-lab)
- [Как предоставить прямую ссылку на мою лабораторию?](#how-do-i-share-a-direct-link-to-my-lab)
- [Что такое учетная запись Майкрософт?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Устранение неполадок 
 
- [Во время создания виртуальной машины произошел сбой артефакта. Как устранить эту проблему?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Почему имеющаяся виртуальная сеть не сохраняется должным образом?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isn't-answered-here?"></a>Мне не удалось найти ответ на свой вопрос. Что делать?
Если вашего вопроса нет в списке, сообщите нам об этом, и мы поможем найти ответ.

- Опубликуйте вопрос в [дискуссии Disqus](#comments) в конце раздела часто задаваемых вопросов и обсудите данную статью с командой разработчиков кэша Azure и другими участниками сообщества.
- Чтобы охватить большую аудиторию, опубликуйте вопрос на [форуме MSDN по Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)и обсудите его с командой разработчиков Azure DevTest Labs и другими участниками сообщества.
- Чтобы сделать запрос на функцию, отправьте свои запросы и предложения на [форуме пользователей Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs?"></a>Почему следует использовать Azure DevTest Labs? 
Azure DevTest Labs позволяет сэкономить время и сократить затраты рабочей группы. Разработчики могут создавать собственные среды с применением нескольких разных основ и использовать артефакты для быстрого развертывания и настройки приложений. Благодаря пользовательским образам и формулам виртуальные машины можно сохранять в качестве шаблонов, которые позже можно легко воспроизвести. Кроме того, лаборатории поддерживают несколько настраиваемых политик, которые позволяют администраторам лаборатории сократить издержки и управлять средами группы. К таким политикам относятся автоматическое завершение работы, пороговое значение затрат, максимальное количество виртуальных машин для каждого пользователя и максимальные размеры виртуальных машин. Прочитайте дополнительные сведения об Azure DevTest Labs в [обзоре](devtest-lab-overview.md) или просмотрите [вводное видео](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-"worry-free,-self-service"-mean?"></a>Что означает "самообслуживание без проблем"?
"Самообслуживание без проблем" означает, что разработчики и тестировщики при необходимости могут создавать собственные среды. Кроме того, администраторы могут быть уверены, что Azure DevTest Labs заботится о минимизации потерь и контроле затрат. Администраторы могут указать доступные размеры и максимальное количество виртуальных машин, а также определить время их запуска и завершения работы. Azure DevTest Labs упрощает процесс контроля расходов и настройки получения оповещений о потреблении ресурсов в лаборатории. 

### <a name="how-can-i-use-azure-devtest-labs?"></a>Как можно использовать Azure DevTest Labs? 
Azure DevTest Labs — это удобная служба, позволяющая быстро создавать среды разработки и тестирования и/или применять к ним политики сокращения расходов. 

Ниже приведено несколько сценариев использования Azure DevTest Labs. 

- Централизованное управление средами разработки и тестирования, применение политик для сокращения расходов и пользовательских образов для совместного использования сборок в группе.
- Разработка приложений с помощью пользовательских образов для сохранения состояния диска на протяжении развертывания.
- Отслеживание расходов, связанных с ходом выполнения. 
- Создание массовых сред тестирования для гарантии качества.
- Использование артефактов и формул для удобной настройки и воспроизведения приложений в разных средах. 
- Распределение виртуальных машин для интенсивной работы группы (совместная работа над разработкой или тестированием) с последующим удобным отзывом этих машин после окончания события. 

### <a name="how-am-i-billed-for-azure-devtest-labs?"></a>Как выставляются счета за использование Azure DevTest Labs? 
Azure DevTest Labs — это бесплатная служба, то есть за создание лабораторий, настройку политик, шаблонов и артефактов платить не нужно. Плата взимается только за ресурсы Azure, используемые в лабораториях, например виртуальные машины, учетные записи хранения и виртуальные сети. Дополнительные сведения о ценах на ресурсы лаборатории см. на странице [цен на Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs?"></a>Какие уровни безопасности предусмотрены в Azure DevTest Labs?  
Доступ к DevTest Labs определяется [службой управления доступом на основе ролей Azure (RBAC)](../active-directory/role-based-access-built-in-roles.md). Чтобы понять особенности доступа, нужно определить разницу между разрешением, ролью и областью в соответствии с RBAC.

- **Разрешение** — это определенный доступ на выполнение конкретного действия. Например, доступ на чтение ко всем виртуальным машинам. 
- **Роль** — это набор разрешений, которые могут быть сгруппированы и назначены пользователю. Например, роль "Владелец подписки" имеет доступ ко всем ресурсам в подписке. 
- **Область** — это уровень в иерархии ресурсов Azure. Например, область может быть группой ресурсов, отдельной лабораторией или целой подпиской. 
 
Azure DevTest Labs предусматривает два типа ролей для предоставления пользователю разрешений в рамках области: владелец и пользователь лаборатории.

- **Владелец лаборатории** — это роль, которая имеет доступ к любым ресурсам в лаборатории. Это означает, что он может изменять политики, читать и записывать любые виртуальные машины, изменять параметры виртуальной сети и т. д. 
- **Пользователь лаборатории** — это роль, которая позволяет просматривать все ресурсы лаборатории, такие как виртуальные машины, политики и виртуальные сети, но не дает возможности изменять политики или управлять виртуальными машинами, созданными другими пользователями. В Azure DevTest Labs также можно создать настраиваемые роли. Дополнительные сведения см. в статье [Предоставление пользователю разрешений для определенных политик лаборатории](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Области имеют иерархическую структуру, поэтому когда пользователю назначаются разрешения на определенном уровне, они автоматически применяются к областям более низкого уровня. Например, если пользователю назначена роль владельца подписки, он имеет доступ ко всем ресурсам в подписке. Эти ресурсы включают все виртуальные машины, виртуальные сети и лаборатории. Таким образом, владелец подписки автоматически наследует роль владельца лаборатории. Однако владелец лаборатории не может наследовать роль владельца подписки, так как он имеет доступ к лаборатории, которая находится ниже уровня подписки в рамках иерархии. Поэтому владелец лаборатории не может просматривать виртуальные машины, виртуальные сети и другие ресурсы, находящиеся вне лаборатории. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task?"></a>Как создать роль, позволяющую пользователям выполнять конкретную задачу?
Сведения о создании настраиваемых ролей и назначении для них разрешений см. в статье "Предоставление пользователю разрешений для определенных политик лаборатории". Ниже приведен пример сценария для создания роли "Пользователь Advanced DevTest Labs" с разрешениями на запуск и завершение работы виртуальных машин в лаборатории.
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-ci/cd-toolchain?"></a>Можно ли интегрировать Azure DevTest Labs с собственным набором инструментов CI/CD? 
При использовании VSTS пользователям предоставляется расширение [Задачи Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) , которое позволяет автоматизировать конвейер выпуска в Azure DevTest Labs. Это расширение можно использовать в следующих целях:

- автоматическое создание и развертывание виртуальной машины, а также ее настройка с помощью последней сборки с использованием задачи "Копирование файлов Azure" или задачи PowerShell VSTS; 
- автоматическая запись состояния виртуальной машины после тестирования с целью воспроизведения ошибки на таком же экземпляре для дальнейшего исследования; 
- удаление виртуальной машины во время завершения работы конвейера выпуска, если она больше не нужна. 

Дополнительные сведения и руководство по использованию расширения VSTS см. в следующих записях блога:
 
- [Azure DevTest Labs – VSTS extension (Azure DevTest Labs: расширение VSTS)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Deploying a new VM in an existing AzureDevTestLab from VSTS (Развертывание из VSTS новой виртуальной машины в имеющейся лаборатории Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (Использование управления выпусками VSTS для непрерывного развертывания в Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Для других наборов инструментов CI/CD все указанные ранее сценарии, осуществляемые с использованием задач VSTS, можно также реализовать путем развертывания [шаблонов Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) с помощью [командлетов Azure PowerShell](../resource-group-template-deploy.md) и [пакетов SDK для .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Для интеграции DevTest Labs с набором инструментов также можно использовать [REST API для DevTest Labs](http://aka.ms/dtlrestapis) .  

### <a name="why-can't-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs?"></a>Почему в колонке "Виртуальные машины Azure" не отображаются все виртуальные машины, доступные в Azure DevTest Labs?
После создания виртуальной машины в Azure DevTest Labs пользователю предоставляется разрешение на доступ к ней. Она будет отображаться в колонках "Labs" (Лаборатории) и **Виртуальные машины** . Пользователи с ролью DevTest Labs могут видеть список всех виртуальных машин, созданных в лаборатории, в колонке **All Virtual Machines** (Все виртуальные машины). Однако пользователи DevTest Labs автоматически не получают доступ на чтение ресурсов виртуальной машины, созданных другими пользователями. Поэтому такие виртуальные машины не отображаются в колонке **Виртуальные машины** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas?"></a>В чем разница между пользовательскими образами и формулами? 
Пользовательский образ — это виртуальный жесткий диск (VHD), а формула — настраиваемый образ, для которого можно задать дополнительные параметры, а затем сохранить их для воспроизведения в будущем. Пользовательский образ рекомендуется использовать для быстрого создания нескольких сред с одинаковой базой. А формула лучше подходит, если необходимо воспроизвести конфигурацию виртуальной машины с последними обновлениями, виртуальной сетью или подсетью, а также с определенным размером. Дополнительные сведения см. в статье [Сравнение пользовательских образов и формул в DevTest Labs](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once?"></a>Как создать несколько виртуальных машин с помощью одного шаблона? 
Чтобы создать несколько виртуальных машин, используйте [задачи VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) или во время создания виртуальной машины [создайте шаблон Azure Resource Manager](devtest-lab-add-vm-with-artifacts.md#save-arm-template) и [разверните его из Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab?"></a>Как переместить имеющиеся виртуальные машины Azure в лабораторию Azure DevTest Labs? 
Мы уже разрабатываем решение для перемещения виртуальных машин непосредственно в Azure DevTest Labs. Пока что вы можете скопировать имеющиеся виртуальные машины в Azure DevTest Labs, выполнив следующие действия: 

1. Скопируйте VHD-файл имеющейся виртуальной машины с помощью этого [сценария Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [пользовательский образ](devtest-lab-create-template.md) . 
1. Затем создайте на его основе виртуальную машину. 
 
### <a name="can-i-attach-multiple-disks-to-my-vms?"></a>Можно ли присоединить к виртуальной машине несколько дисков? 
Такая возможность поддерживается.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images?"></a>Как автоматизировать процесс передачи VHD-файлов для создания пользовательских образов? 
Существуют два варианта:

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) .
- [Обозреватель службы хранилища Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) , работающее на платформе Windows, OSX и Linux.   
 
Чтобы найти целевую учетную запись хранения, связанную с лабораторией, выполните следующие действия:

1. Выполните вход на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. На панели слева выберите **Группы ресурсов** . 
1. Найдите и выберите группу ресурсов, связанную с вашей лабораторией. 
1. В колонке **Обзор** выберите одну из учетных записей хранения. 
1. Выберите **Большие двоичные объекты**.
1. Просмотрите список отправленных файлов. Если файлы отсутствуют, вернитесь к шагу 4 и выберите другую учетную запись хранения.
1. В команде AzCopy введите **URL-адрес** в качестве места назначения.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab?"></a>Как автоматизировать процесс удаления всех виртуальных машин в моей лаборатории?

Помимо удаления виртуальных машин из лаборатории на портале Azure, можно удалить все виртуальные машины в лаборатории с помощью сценария PowerShell. В следующем примере просто измените значения параметров под комментарием **Values to change** (Изменяемые значения). Вы можете узнать значения `subscriptionId`, `labResourceGroup` и `labName` в колонке лаборатории на портале Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts?"></a>Что такое артефакты? 
Артефакты — это настраиваемые элементы, используемые для развертывания последних обновлений или средств разработки на виртуальной машине. Во время создания артефакты легко прикрепляются к виртуальной машине, а после подготовки виртуальной машины они используются для ее развертывания и настройки. В [общедоступном репозитории GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) находятся несколько созданных ранее артефактов, но вы также можете без труда [создать собственные](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template?"></a>Как создать лабораторию на основе шаблона Azure Resource Manager? 
Мы предоставили [в GitHub репозиторий шаблонов Azure Resource Manager Azure для лабораторий](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates), которые можно развернуть "как есть" или изменить, чтобы создать пользовательские шаблоны для своих лабораторий. Каждый из этих шаблонов содержит ссылку, которую можно щелкнуть, чтобы развернуть лабораторию "как есть" в собственной подписке Azure. Можно также настроить шаблон и [развернуть его с помощью PowerShell или интерфейса командной строки Azure (Azure CLI)](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names?-can-i-rename-or-modify-these-resource-groups?"></a>Почему виртуальные машины создаются в разных группах ресурсов с произвольными именами? Можно ли переименовать или изменить эти группы ресурсов? 
Такая особенность групп ресурсов позволяет Azure DevTest Labs управлять разрешениями пользователя и доступом к виртуальным машинам. Вы можете переместить виртуальную машину в другую группу ресурсов с другим именем, однако это не рекомендуется. Мы работаем над тем, чтобы исправить эту особенность и предоставить большую гибкость.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription?"></a>Сколько лабораторий можно создать в рамках одной подписки? 
Определенного ограничения на количество лабораторий, которые можно создать в рамках одной подписки, нет. Но используемые ресурсы ограничены для каждой лаборатории. Дополнительные сведения см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md) и записи блога [об увеличении предельных значений ограничений](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab?"></a>Сколько виртуальных машин можно создать на лабораторию? 
Определенного ограничения на количество виртуальных машин, которые можно создать на лабораторию, нет. Но в данный момент лаборатория поддерживает только 40 виртуальных машин, запущенных одновременно, в стандартном хранилище и 25 в хранилище класса "Премиум". В настоящее время мы работаем над увеличением этих предельных значений. 

### <a name="how-do-i-share-a-direct-link-to-my-lab?"></a>Как предоставить прямую ссылку на мою лабораторию?

Чтобы предоставить пользователям прямую ссылку на лабораторию, можно выполнить следующую процедуру.

1. Перейдите к лаборатории на портале Azure.
2. Скопируйте URL-адрес лаборатории из браузера и предоставьте его пользователям лаборатории. 

>[AZURE.NOTE] Если лабораторию используют внешние пользователи с [учетными записями MSA](#what-is-a-microsoft-account) , не входящие в каталог Active Directory вашей организации, то они могут получить сообщение об ошибке при переходе по предоставленной ссылке. В этом случае предложите им щелкнуть свое имя в правом верхнем углу портала Azure и из меню **Каталог** выбрать каталог, в котором расположена лаборатория.

### <a name="what-is-a-microsoft-account?"></a>Что такое учетная запись Майкрософт?

Учетная запись Майкрософт используется практически для всех действий с устройствами и службами Майкрософт. Это электронный адрес и пароль, используемые для входа в Skype, Outlook.com, OneDrive, Windows Phone и Xbox LIVE. Это означает, что ваши файлы, фотографии, контакты и параметры доступны на любом вашем устройстве. 

>[AZURE.NOTE] Учетная запись Майкрософт раньше называлась Windows Live ID.
 
### <a name="my-artifact-failed-during-vm-creation.-how-do-i-troubleshoot-it?"></a>Во время создания виртуальной машины произошел сбой артефакта. Как устранить эту проблему? 
Дополнительные сведения о получении журналов с записями об этом артефакте см. в записи блога [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Способы устранения сбоя артефактов в лабораториях для разработки и тестирования Azure). Эта запись создана одним из наших специалистов со статусом MVP. 
 
### <a name="why-isn't-my-existing-virtual-network-saving-properly?"></a>Почему имеющаяся виртуальная сеть не сохраняется должным образом?  
Причина этого может заключаться в том, что в имени виртуальной сети содержатся точки. Если это так, удалите точки или замените их дефисами, а затем повторите попытку.



<!--HONumber=Oct16_HO2-->


