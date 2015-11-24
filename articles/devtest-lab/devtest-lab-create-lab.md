    <properties
	pageTitle="Create a DevTest Lab | Microsoft Azure"
	description="Create a new DevTest Lab lab for virtual machines"
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
	ms.topic="get-started-article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

# Создание лаборатории для разработки и тестирования Azure

## Предварительные требования

Чтобы создать лабораторию для разработки и тестирования, вам потребуется:

- Подписка Azure. Дополнительные сведения о вариантах приобретения Azure см. на странице [Как приобрести Azure](http://azure.microsoft.com/pricing/purchase-options/) или [Бесплатный ознакомительный период в один месяц](https://azure.microsoft.com/pricing/free-trial/). Для создания лаборатории необходимо быть владельцем подписки.
- Группа ресурсов Azure для лаборатории. См. статьи [Общие сведения о диспетчере ресурсов Azure](resource-group-overview.md) и [Управление доступом к ресурсам](./azure-portal/resource-group-rbac.md).


## Создание лаборатории

1. Войдите на [портал предварительной версии Azure](http://portal.azure.com).

1. Нажмите кнопку **Обзор**.

1. В списке выберите пункт **Лаборатории для разработки и тестирования**.

1. В колонке **Лаборатории для разработки и тестирования** нажмите кнопку **Добавить**.

    ![Добавление лаборатории для разработки и тестирования](./media/devtest-lab-create-lab/add-lab-button.png)

1. В колонке **Создание лаборатории для разработки и тестирования** сделайте следующее:

    1. Введите **имя лаборатории**.
    1. Выберите **подписку**, которую необходимо связать с лабораторией.
    1. Выберите **расположение**, в котором будет храниться лаборатория.
    1. Нажмите кнопку **Создать**.

    ![Колонка «Создание лаборатории для разработки и тестирования»](./media/devtest-lab-create-lab/create-devtestlab-blade.png)

## Дальнейшие действия

Создав лабораторию, можно выполнить дальнейшие действия, приведенные ниже.

- [Безопасный доступ к лаборатории для разработки и тестирования](devtest-lab-add-devtest-user.md).

- [Определение политик лаборатории](devtest-lab-set-lab-policy.md).

- [Создание шаблона лаборатории](devtest-lab-create-template.md).

- [Создание пользовательских артефактов для виртуальных машин](devtest-lab-artifact-author.md).

- [Добавление виртуальной машины с артефактами в лабораторию для разработки и тестирования Azure](devtest-lab-add-vm-with-artifacts.md).

<!---HONumber=Nov15_HO4-->