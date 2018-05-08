---
title: Диагностика сбоев артефактов на виртуальной машине Azure DevTest Labs | Документация Майкрософт
description: Узнайте, как устранять неполадки при сбоях артефактов в Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: ebc64215683989ce07f4dd88dc352ecaefe184cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Диагностика сбоев артефактов в лаборатории 
После создания артефакта можно проверить, работает ли он. Журналы артефактов в Azure DevTest Labs предоставляют сведения, которые можно использовать для диагностики сбоя артефакта. Есть два способа просмотреть данные журнала артефактов для виртуальной машины Windows:

* На портале Azure
* в виртуальной машине.

> [!NOTE]
> Чтобы можно было правильно идентифицировать и объяснить сбой, важно правильно структурировать артефакт. Сведения о том, как правильно создать артефакт, см. в статье [Создание пользовательских артефактов для виртуальной машины DevTest Labs](devtest-lab-artifact-author.md). Правильно структурированный артефакт можно изучить на примере этого артефакта [тестовых типов параметров](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Устранение неполадок при сбоях артефактов с помощью портала Azure

1. На портале Azure в списке ресурсов выберите свою лабораторию.
2. Выберите виртуальную машину Windows, содержащую анализируемый артефакт.
3. На левой панели в разделе **ОБЩИЕ** выберите **Артефакты**. Появится список артефактов, связанных с этой виртуальной машиной. В нем указаны имя и состояние артефакта.

   ![Состояние артефакта](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Выберите артефакт, отображаемый состояние **Сбой**. Артефакт откроется и отобразится сообщение расширения, содержащее сведения о сбое артефакта.

   ![Сообщение об ошибке артефакта](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Устранение неполадок при сбоях артефактов из виртуальной машины

1. Войдите в виртуальную машину, содержащую артефакт, для которого необходимо выполнить диагностику.
2. Перейдите к расположению C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, где *1.9* — это номер версии расширения пользовательских сценариев Azure.

   ![Файл состояния](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Откройте файл **состояния**.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Связанные записи в блогах
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Присоединение виртуальной машины к имеющемуся домену AD с помощью шаблона ARM в Azure DevTest Labs)

## <a name="next-steps"></a>Дополнительная информация
* Узнайте, как [добавить в лабораторию репозиторий Git](devtest-lab-add-artifact-repo.md).

