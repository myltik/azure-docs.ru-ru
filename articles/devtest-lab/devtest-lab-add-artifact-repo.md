---
title: "Добавление репозитория артефактов Git в лабораторию в Azure DevTest Labs | Документация Майкрософт"
description: "Добавление репозитория Git GitHub или Visual Studio Team Services, предназначенного для источника пользовательских артефактов, в Azure DevTest Labs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 55b8b97a8cabedf86e2b92d9490be74c72a5fb09
ms.contentlocale: ru-ru
ms.lasthandoff: 04/27/2017


---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates-for-use-in-azure-devtest-labs"></a>Добавление репозитория Git для хранения пользовательских артефактов и шаблонов Azure Resource Manager в Azure DevTest Labs

Если вы хотите [создать пользовательские артефакты](devtest-lab-artifact-author.md) для виртуальных машин в лаборатории или [использовать шаблоны Azure Resource Manager для создания настраиваемой тестовой среды](devtest-lab-create-environment-from-arm.md), нужно также добавить частный репозиторий Git для артефактов или шаблонов Azure Resource Manager, создаваемых вашей группой. Репозиторий артефактов может находиться в [GitHub](https://github.com) или [Visual Studio Team Services (VSTS)](https://visualstudio.com).

Мы предоставили [репозиторий Github для артефактов](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), который можно развернуть в исходном виде либо настроить для своей лаборатории. При настройке или создании артефактов их нельзя сохранять в общедоступном репозитории — нужно создать собственный частный репозиторий. 

При создании виртуальной машины можно сохранить шаблон Azure Resource Manager, при необходимости настроить его, а затем использовать для создания дополнительных виртуальных машин. Нежно создать собственный частный репозиторий для хранения настраиваемых шаблонов Azure Resource Manager.  

* Сведения о создании репозитория GitHub см. в [этой статье](https://help.github.com/categories/bootcamp/).
* Сведения о создании проекта Team Services с репозиторием Git см. в статье [Connect to Visual Studio Team Services from Eclipse, Xcode, Visual Studio, and more](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online) (Подключение к Visual Studio Team Services из Eclipse, Xcode, Visual Studio и других служб).

Вот как может выглядеть содержащий артефакты репозиторий в GitHub:  
![Пример репозитория артефактов GitHub](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Получение сведений о репозитории и его учетных данных
Чтобы добавить репозиторий в лабораторию, сначала нужно получить из репозитория определенные сведения. В следующем разделе приводятся инструкции по получению таких сведений для репозиториев, размещенных на портале GitHub и в Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Получение URL-адреса клона и персонального маркера доступа из репозитория GitHub
Для получения URL-адреса клона и личного маркера доступа из репозитория GitHub, выполните следующие действия:

1. Перейдите на домашнюю страницу репозитория GitHub, который содержит определения артефактов или шаблонов Azure Resource Manager.
2. Выберите **Clone or download**(Клонировать или скачать).
3. Нажмите кнопку, чтобы скопировать **URL-адрес клона HTTPS** в буфер обмена, и сохраните URL-адрес для последующего использования.
4. Выберите изображение профиля в правом верхнем углу GitHub и щелкните **Параметры**.
5. В меню **Личные параметры** в левой части экрана выберите **Личные маркеры доступа**.
6. Выберите **Создать новый маркер**.
7. На странице **New personal access token** (Новый личный маркер доступа) введите данные в поле **Token description** (Описание маркера), примите элементы по умолчанию в разделе **Выберите области** и щелкните **Создать токен**.
8. Сохраните созданный маркер — он вам потребуется позднее.
9. Теперь GitHub можно закрыть.   
10. Перейдите к разделу [Подключение лаборатории к репозиторию](#connect-your-lab-to-the-repository).

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Получение URL-адреса клона и личного маркера доступа из репозитория Visual Studio Team Services
Для получения URL-адреса клона и личного маркера доступа из репозитория Visual Studio Team Services выполните следующие действия:

1. Откройте домашнюю страницу групповой коллекции (например, `https://contoso-web-team.visualstudio.com`) и выберите свой проект.
2. На домашней странице проекта выберите **Код**.
3. Чтобы увидеть URL-адрес клона, на странице **Код** проекта выберите **Клон**.
4. Сохраните URL-адрес, так как он потребуется при дальнейшей работе с данным учебником.
5. Для создания персонального маркера доступа выберите в раскрывающемся меню учетной записи пользователя **Мой профиль** .
6. На странице сведений о профиле выберите **Безопасность**.
7. На вкладке **Безопасность** щелкните **Добавить**.
8. На странице **Создание персонального маркера данных** :

   * Введите **описание** маркера.
   * Выберите пункт **180 дней** в списке **Срок действия истекает через**.
   * Выберите пункт **Все доступные учетные записи** в списке **Учетные записи**.
   * Выберите параметр **Все области** .
   * Выберите пункт **Создать маркер**.
9. Новый маркер появится в списке **Личные маркеры доступа** . Выберите **Скопировать маркер**и сохраните значение маркера, так как оно вам потребуется позднее.
10. Перейдите к разделу [Подключение лаборатории к репозиторию](#connect-your-lab-to-the-repository).

## <a name="connect-your-lab-to-the-repository"></a>Подключение лаборатории к репозиторию
1. Войдите на [портал Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Щелкните **Больше служб**, а затем выберите в списке **DevTest Labs**.
3. Из списка лабораторий выберите нужную лабораторию.   
4. В колонке лаборатории выберите **Конфигурация и политики**.
5. В колонке **Конфигурация и политики** лаборатории выберите **Репозитории**.
6. В колонке **Репозитории** выберите **+ Добавить**.

    ![Кнопка добавления репозитория](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. Во второй колонке **Репозитории** укажите следующие сведения:

   * **Имя** — введите имя репозитория.
   * **URL-адрес клона Git** — введите URL-адрес клона HTTPS Git, скопированный ранее из GitHub или Visual Studio Team Services.
   * **Ветвь** — укажите ветвь для получения определений.
   * **Личный маркер доступа** — укажите личный маркер доступа, полученный ранее из GitHub или Visual Studio Team Services.
   * **Путь к папке** — введите по меньшей мере один путь к папке относительно URL-адреса клона, содержащего определения артефактов или шаблонов Azure Resource Manager. При указании подкаталога обязательно включите косую черту в путь к папке.

     ![Колонка репозиториев](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. Щелкните **Сохранить**.

## <a name="next-steps"></a>Дальнейшие действия
После создания частного репозитория Git в зависимости от потребностей можно выполнить одно или оба из следующих действий:
* Сохраните [пользовательские артефакты](devtest-lab-artifact-author.md), которые можно использовать позднее для создания виртуальных машин.
* [Создайте среды с множеством виртуальных машин и ресурсов PaaS с помощью шаблонов Azure Resource Manager](devtest-lab-create-environment-from-arm.md), а затем сохраните шаблоны в частном репозитории.

При создании виртуальной машины вы убедитесь, что артефакты или шаблоны добавляются в ваш репозиторий Git. Они сразу же появятся в списке артефактов или шаблонов, при этом в столбце источника отображается имя частного репозитория. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>Связанные записи в блогах
* [How to troubleshoot failing Artifacts in AzureDevTestLabs (Способы устранения сбоя артефактов в лабораториях для разработки и тестирования Azure)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Присоединение виртуальной машины к существующему домену AD с помощью шаблона ARM в лаборатории для разработки и тестирования Azure)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

