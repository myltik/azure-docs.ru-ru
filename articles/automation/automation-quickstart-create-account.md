---
title: Краткое руководство Azure. Создание учетной записи службы автоматизации Azure | Документация Майкрософт
description: Узнайте, как создать учетную запись службы автоматизации Azure и запустить модуль runbook.
services: automation
author: csand-msft
ms.author: csand
ms.date: 12/13/2017
ms.topic: quickstart
ms.service: automation
ms.component: process-automation
ms.custom: mvc
ms.openlocfilehash: 3accd62415cd7d3a8210b7a05aa1edfd7a380edc
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2018
ms.locfileid: "34055160"
---
# <a name="create-an-azure-automation-account"></a>Создание учетной записи службы автоматизации Azure

Учетные записи службы автоматизации Azure можно создавать с помощью Azure. В этом случае для создания и настройки учетных записей службы автоматизации и связанных ресурсов Azure используется пользовательский интерфейс на основе браузера. В этом кратком руководстве содержатся инструкции по созданию учетной записи службы автоматизации и запуске в ней модуля runbook.

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) Azure, прежде чем начинать работу.

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите в Azure: https://portal.azure.com

## <a name="create-automation-account"></a>Создание учетной записи службы автоматизации

1. Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.

1. Нажмите **Мониторинг и управление** и выберите **Служба автоматизации**.

1. Введите сведения об учетной записи. Для параметра **Создать учетную запись запуска от имени Azure** выберите **Да**, чтобы артефакты, которые упрощают аутентификацию в Azure, включались автоматически. По завершении нажмите кнопку **Создать**, чтобы начать развертывание учетной записи службы автоматизации.

    ![Ввод сведений об учетной записи службы автоматизации на странице](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

1. Учетная запись службы автоматизации закрепляется на панели мониторинга Azure. Когда развертывание завершится, автоматически откроется страница общих сведений об учетной записи службы автоматизации.

    ![Обзор учетной записи службы автоматизации](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Запуск модуля runbook

Запустите один из учебных модулей runbook.

1. Щелкните **Модули Runbook** в разделе **Автоматизация процессов**. Отобразится список модулей runbook. По умолчанию в учетной записи включено несколько учебных модулей runbook.

    ![Список модулей runbook учетной записи службы автоматизации](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Выберите модуль runbook **AzureAutomationTutorialScript**. Откроется страница общих сведений о runbook.

    ![Общие сведения о runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Щелкните **Запустить** и на странице **Запуск Runbook** нажмите кнопку **ОК**, чтобы запустить runbook.

    ![Страница задания runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Когда в строке **Состояние задания** отобразится значение **Выполняется**, щелкните **Вывод** или **All Logs** (Все журналы), чтобы просмотреть выходные данные задания runbook. Для этого учебного модуля runbook выводится список ресурсов Azure.

## <a name="clean-up-resources"></a>Очистка ресурсов

Ставшие ненужными группу ресурсов, учетную запись службы автоматизации и все связанные ресурсы можно удалить. Для этого выберите группу ресурсов для учетной записи службы автоматизации и щелкните **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы развернули учетную запись службы автоматизации, запустили задание runbook и просмотрели его результаты. Дополнительные сведения о службе автоматизации Azure см. в следующем кратком руководстве по созданию первого модуля runbook.

> [!div class="nextstepaction"]
> [Краткое руководство по службе автоматизации. Создание runbook](./automation-quickstart-create-runbook.md)
