---
title: "Развертывание шаблонов в Azure Stack с помощью Visual Studio | Документация Майкрософт"
description: "Узнайте, как развертывать шаблоны в Azure Stack с помощью Visual Studio."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: ecefa8c3695aad0593516cb9ad8812216e163192
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Развертывание шаблонов в Azure Stack с помощью Visual Studio

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

С помощью Visual Studio можно развертывать шаблоны Azure Resource Manager в Пакет средств разработки Azure Stack.

1. [Выполните установку и подключение](azure-stack-install-visual-studio.md) к Azure Stack с помощью Visual Studio.
2. Откройте Visual Studio.
3. Щелкните **Файл**, выберите **Создать** и в диалоговом окне **Новый проект** щелкните **Группа ресурсов Azure**.
4. Введите **имя** нового проекта и нажмите кнопку **ОК**.
5. В диалоговом окне **Выберите шаблон Azure** измените значение раскрывающегося списка *Показывать шаблоны из этого расположения* на **Azure Stack Quickstart** (Быстрый запуск Azure Stack).
6. Выберите **101-create-storage-account**, а затем нажмите кнопку **ОК**.  
7. Вы можете просмотреть список шаблонов, доступных в новом проекте, развернув узел **Шаблоны** в области **обозревателя решений**.
8. В области **обозревателя решений** щелкните правой кнопкой мыши имя проекта, щелкните **Развернуть**, а затем щелкните **Новое развертывание**.
9. В диалоговом окне **Развернуть в группе ресурсов** из раскрывающегося списка **Подписки** выберите свою подписку Microsoft Azure Stack.
10. Выберите существующую группу ресурсов из раскрывающегося списка **Группа ресурсов** или создайте новую.
11. Из списка **Расположение группы ресурсов** выберите расположение, затем нажмите кнопку **Развернуть**.
12. В диалоговом окне **Изменить параметры** введите значения параметров (которые зависят от шаблона), а затем нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация
[Развертывание шаблонов с помощью командной строки](azure-stack-deploy-template-command-line.md)

[Разработка шаблонов для Azure Stack](azure-stack-develop-templates.md)

