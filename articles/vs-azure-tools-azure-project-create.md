---
title: Создание проекта облачной службы Azure в Visual Studio | Документация Майкрософт
description: Узнайте, как создать проект облачной службы Azure в Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 06213ecabf3669bf3b8cf2b8d73a4e8def359536
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31791568"
---
# <a name="creating-an-azure-cloud-service-project-with-visual-studio"></a>Создание проекта облачной службы Azure в Visual Studio
Инструменты Azure для Visual Studio предоставляют шаблон проекта, который позволяет создать облачную службу Azure. Когда проект создан, Visual Studio дает возможность настроить облачную службу, выполнить ее отладку и развернуть ее в Azure.

## <a name="steps-to-create-an-azure-cloud-service-project-in-visual-studio"></a>Шаги по созданию проекта облачной службы Azure в Visual Studio
В этом разделе описывается процесс создания проекта облачной службы Azure в Visual Studio с применением одной или нескольких веб-ролей.  

1. Запустите Visual Studio от имени администратора.

1. В главном меню выберите **Файл** > **Создать** > **Проект**.

1. В узлах шаблонов проектов Visual C# или Visual Basic выберите **Облако**, а затем в списке шаблонов выберите **Облачная служба Azure**.

    ![Новая облачная служба Azure](./media/vs-azure-tools-azure-project-create/new-project-wizard-for-cloud-service.png)

1. Укажите, какую версию платформы .NET Framework вы хотите использовать для разработки проекта.

1. Введите имя проекта, его расположение и имя решения. 

1. Нажмите кнопку **ОК**.

1. В диалоговом окне **Новая облачная служба Microsoft Azure** выберите роли, которые хотите добавить, и нажмите стрелку вправо, чтобы добавить их в решение.

    ![Выбор ролей для новой облачной службы Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service.png)

1. Чтобы переименовать добавленную роль, наведите указатель мыши на роль в диалоговом окне **Новая облачная служба Microsoft Azure** и в контекстном меню выберите **Переименовать**. Вы также можете переименовать роль в решении (с помощью **обозревателя решений**) после ее добавления.

    ![Переименование роли облачной службы Azure](./media/vs-azure-tools-azure-project-create/new-cloud-service-rename.png)

Проект Azure в Visual Studio связан с проектами ролей в решении. Проект также содержит *файл определения службы* и *файл конфигурации службы*:

- **файл определения службы** определяет параметры среды выполнения для приложения, в том числе требуемые роли, конечные точки и размер виртуальной машины; 
- **файл конфигурации службы** (CSCFG-файл) задает количество выполняемых экземпляров роли, а также значения параметров, определенных для роли. 

Дополнительные сведения об этих файлах см. в статье [Настройка ролей для облачной службы Azure в среде Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

## <a name="next-steps"></a>Дополнительная информация
- [Управление ролями в проектах облачных служб Azure с помощью Visual Studio](./vs-azure-tools-cloud-service-project-managing-roles.md)
