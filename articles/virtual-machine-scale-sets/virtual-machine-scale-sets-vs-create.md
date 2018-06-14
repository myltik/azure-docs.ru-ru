---
title: Развертывание масштабируемого набора виртуальных машин с помощью Visual Studio | Документация Майкрософт
description: Развертывание набора масштабирования виртуальных машин с помощью Visual Studio и шаблона Resource Manager
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73454abc11a832a1b7f4131bf13699bd0a94edea
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26782152"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Как создать масштабируемый набор виртуальных машин с помощью Visual Studio
В этой статье описывается развертывание шаблонов масштабируемых наборов виртуальных машин с использованием развертывания группы ресурсов Visual Studio.

[Масштабируемые наборы виртуальных машин Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) — это вычислительный ресурс Azure для развертывания коллекций похожих виртуальных машин с автоматическим масштабированием и балансировкой нагрузки, а также для управления ею. Масштабируемые наборы виртуальных машин можно подготавливать и развертывать с помощью [шаблонов Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Шаблоны Azure Resource Manager можно развертывать с помощью Azure CLI, PowerShell, REST, а также непосредственно из Visual Studio. Visual Studio предоставляет набор примеров шаблонов, которые можно развернуть в рамках проекта развертывания группы ресурсов Azure.

Развертывания группы ресурсов Azure позволяют сгруппировать несколько связанных ресурсов Azure и опубликовать их в одной операции развертывания. Дополнительные сведения о них можно получить в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Предварительные требования
Чтобы приступить к развертыванию шаблонов масштабируемых наборов виртуальных машин в Visual Studio, вам потребуется следующее:

* Visual Studio 2013 или более поздней версии
* Пакет SDK Azure версии 2.7, 2.8 или 2.9

>[!NOTE]
>При выполнении этих инструкций предполагается, что вы используете Visual Studio с [пакетом SDK Azure версии 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Создание проекта
1. Создайте проект в Visual Studio, выбрав **Файл | Создать | Проект**.
   
    ![Создание файла][file_new]

2. В разделе **Visual C# | Облако** выберите **Azure Resource Manager**, чтобы создать проект для развертывания шаблона Azure Resource Manager.
   
    ![Создание проекта][create_project]

3. В списке шаблонов выберите шаблон масштабируемых наборов виртуальных машин для Linux или Windows.
   
   ![Выбор шаблона][select_Template]

4. После создания проекта вы увидите сценарии развертывания PowerShell, шаблон Azure Resource Manager и файл параметров для масштабируемого набора виртуальных машин.
   
    ![обозревателе решений][solution_explorer]

## <a name="customize-your-project"></a>Настройка проекта
Теперь можно изменить шаблон, настроив его в соответствии с потребностями своего приложения, например, добавить свойства расширения виртуальной машины или изменить правила балансировки нагрузки. По умолчанию шаблоны масштабируемого набора виртуальных машин настроены для развертывания расширения AzureDiagnostics, которое позволяет легко добавлять правила автоматического масштабирования. Кроме того, развертывается подсистема балансировки нагрузки с общедоступным IP-адресом, для которого настроены правила NAT для входящего трафика. 

Подсистема балансировки нагрузки позволяет подключаться к экземплярам виртуальных машин по протоколу SSH (Linux) или RDP (Windows). Диапазон портов клиента начинается с 50000. В случае с Linux это означает, что при подключении к порту 50000 по протоколу SSH, вы будете перенаправлены на порт 22 первой виртуальной машины в наборе, а при подключении к порту 50001 вы будете перенаправлены на порт 22 второй виртуальной машины и т. д.

 Удобный способ изменения шаблонов с помощью Visual Studio — использовать структуру JSON для организации параметров, переменных и ресурсов. Понимание схемы Visual Studio позволит обнаружить ошибки в шаблоне перед его развертыванием.

![Обозреватель JSON][json_explorer]

## <a name="deploy-the-project"></a>Развертывание проекта
1. Чтобы создать ресурс масштабируемого набора виртуальных машин, разверните шаблон Azure Resource Manager. Щелкните узел проекта правой кнопкой мыши и выберите **Развернуть | Новое развертывание**.
   
    ![Развертывание шаблона][5deploy_Template]
    
2. Выберите свою подписку в диалоговом окне "Развертывание в группе ресурсов".
   
    ![Развертывание шаблона][6deploy_Template]

3. В нем можно создать группу ресурсов Azure, в которую следует развернуть шаблон.
   
    ![Создание группы ресурсов][new_resource]

4. Затем нажмите кнопку **Изменить параметры**, чтобы ввести параметры, которые будут переданы шаблону. Укажите имя пользователя и пароль для операционной системы, которые требуются для создания развертывания. Если инструменты PowerShell для Visual Studio не установлены, мы рекомендуем установить флажок **Save passwords** (Сохранить пароли), чтобы избежать скрытого запроса окна командной строки PowerShell, или использовать [поддержку keyvault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Изменение параметров][edit_parameters]

5. Щелкните **Развернуть**. В окне **Вывод** отображается ход развертывания. Обратите внимание, что действие выполняет сценарий **Deploy-AzureResourceGroup.ps1**.
   
   ![Окно вывода][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Изучение масштабируемого набора виртуальных машин
После завершения развертывания можно просмотреть новый масштабируемый набор виртуальных машин в Visual Studio **Cloud Explorer** (обновите список). Обозреватель облака позволяет управлять ресурсами Azure в Visual Studio при разработке приложений. Масштабируемый набор виртуальных машин также можно просмотреть на [портале Azure](https://portal.azure.com) и в [обозревателе ресурсов Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 С помощью портала удобнее всего визуально управлять инфраструктурой Azure в браузере, а с помощью обозревателя ресурсов Azure — просматривать и отлаживать ресурсы Azure. Обозреватель ресурсов позволяет просматривать состояние экземпляров и использовать для отображаемых ресурсов команды PowerShell.

## <a name="next-steps"></a>Дополнительная информация
После успешного развертывания масштабируемых наборов виртуальных машин с помощью Visual Studio можно выполнить дальнейшую настройку проекта в соответствии с требованиями приложения. Например, можно настроить автоматическое масштабирование, добавив ресурс **Insights**, добавить инфраструктуру в шаблон в качестве автономных виртуальных машин или развернуть приложения с помощью расширения пользовательского сценария. Хорошим примером шаблонов является репозиторий [Шаблоны быстрого запуска Azure](https://github.com/Azure/azure-quickstart-templates) в GitHub (введите vmss в строке поиска).

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
