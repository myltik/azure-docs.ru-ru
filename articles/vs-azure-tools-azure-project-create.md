---
title: Создание проекта облачной службы Azure в Visual Studio | Документация Майкрософт
description: Узнайте, как создать проект облачной службы Azure в Visual Studio.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: ec580df7-3dcc-45a9-a1d9-8c110678dfb5
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 41c2bd0dc1f609b6b7b9cfda43c2378feb1c3b6c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/30/2018
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
