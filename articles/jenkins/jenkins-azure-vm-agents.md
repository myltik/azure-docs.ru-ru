---
title: "Использование агентов виртуальных машин для непрерывной интеграции с Jenkins."
description: "Агенты виртуальных машин Azure в качестве подчиненных модулей Jenkins."
services: multiple
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: Human Translation
ms.sourcegitcommit: 1e6f2b9de47d1ce84c4043f5f6e73d462e0c1271
ms.openlocfilehash: 5f2df414b4d0e8798b7ed6d90d0ea0fb79d42fc2
ms.contentlocale: ru-ru
ms.lasthandoff: 06/21/2017

---
# <a name="use-azure-vm-agents-for-continuous-integration-with-jenkins"></a>Использование агентов виртуальных машин для непрерывной интеграции с Jenkins.

В этом кратком руководстве объясняется, как с помощью подключаемого модуля агентов виртуальных машин Azure для Jenkins создать в Azure агент Linux (Ubuntu) по запросу.

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с руководством.

* Если главный модуль Jenkins еще не создан, можно начать с [шаблона решения](install-jenkins-solution-template.md) 
* Если у вас еще нет субъекта-службы Azure, см. статью [Создание субъекта-службы Azure с помощью Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json).

## <a name="install-azure-vm-agents-plugin"></a>Установка подключаемого модуля агентов виртуальных машин

Если вы начали с [шаблона решения](install-jenkins-solution-template.md), подключаемый модуль агентов виртуальных машин Azure устанавливается в главном модуле Jenkins.

Если нет, установите подключаемый модуль **агентов виртуальных машин** с панели мониторинга Jenkins.

## <a name="configure-the-plugin"></a>Настройка подключаемого модуля

* На панели мониторинга Jenkins последовательно выберите элементы **Manage Jenkins (Управление Jenkins) -> Configure System (Настройка системы) ->**. Прокрутите до нижней части страницы и найдите раздел с раскрывающимся списком **Add new cloud** (Добавление нового облака). В меню выберите **Microsoft Azure VM Agents** (Агенты виртуальных машин Microsoft Azure)
* Выберите существующую учетную запись из раскрывающегося списка учетных данных Azure.  Чтобы добавить новый **субъект-службу Microsoft Azure**, введите следующие значения: идентификатор подписки, идентификатор клиента, секрет клиента и конечная точка маркера OAuth 2.0.

![Учетные данные Azure](./media/jenkins-azure-vm-agents/service-principal.png)

* Нажмите кнопку **Verify configuration** (Проверить конфигурацию), чтобы убедиться в правильности конфигурации профиля.
* Сохраните конфигурацию и перейдите к следующему шагу.

## <a name="template-configuration"></a>Конфигурация шаблона

### <a name="general-configuration"></a>Общая конфигурация
Настройте шаблон для определения агента виртуальных машин Azure. 

* Нажмите кнопку **Добавить**, чтобы добавить шаблон. 
* Введите имя нового шаблона. 
* В поле метки введите ubuntu. Эта метка используется во время настройки задания.
* Выберите нужный регион в поле со списком.
* Выберите нужный размер виртуальной машины.
* Укажите имя учетной записи хранения Azure или оставьте это поле пустым, чтобы по умолчанию использовать значение jenkinsarmst.
* Укажите период удержания в минутах. Этот параметр определяет время ожидания (в минутах) Jenkins перед автоматическим удалением неактивного агента. Укажите 0, если не хотите, чтобы неактивные агенты удалялись автоматически.

![Общая конфигурация](./media/jenkins-azure-vm-agents/general-config.png)

### <a name="image-configuration"></a>Конфигурация образа

Чтобы создать агент Linux (Ubuntu), выберите **Image reference** (Ссылка на образ) и используйте в качестве примера указанную ниже конфигурацию. Последние поддерживаемые образы Azure см. в [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1).

* Издатель образа: Canonical
* Предложение образа: UbuntuServer
* Номер SKU образа: 14.04.5-LTS
* Версия образа виртуальной машины: последняя
* Тип ОС: Linux
* Метод запуска: SSH
* Укажите учетные данные администратора
* При использовании скрипта инициализации виртуальной машины введите:
```
# Install Java
sudo apt-get -y update
sudo apt-get install -y openjdk-7-jdk
sudo apt-get -y update --fix-missing
sudo apt-get install -y openjdk-7-jdk
```
![Конфигурация образа](./media/jenkins-azure-vm-agents/image-config.png)

* Нажмите кнопку **Verify Template** (Проверить шаблон) для проверки конфигурации.
* Щелкните **Сохранить**.

## <a name="create-a-job-in-jenkins"></a>Создание задания в Jenkins

* На панели мониторинга Jenkins щелкните элемент **New Item**(Создать элемент). 
* Введите имя, выберите **Freestyle project** (Универсальный проект) и нажмите кнопку **ОК**.
* На вкладке **General** (Общие сведения) выберите Restrict where project can be run (Ограничения для запуска проекта) и введите для выражения метки значение ubuntu. Теперь в раскрывающемся списке отображается надпись ubuntu.
* Щелкните **Сохранить**.

![Настройка задания](./media/jenkins-azure-vm-agents/job-config.png)

## <a name="build-your-new-project"></a>Сборка нового проекта

* Вернитесь на панель мониторинга Jenkins.
* Щелкните правой кнопкой мыши созданное задание и выберите команду **Build now** (Собрать). Сборка запущена. 
* По завершении сборки перейдите к окну **Console output** (Вывод на консоль). Вы увидите, что сборка выполнена в Azure удаленно.

![Вывод на консоль](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="reference"></a>Справочные материалы

* Пятничное видео Azure: [Continuous Integration with Jenkins Using Azure VM Agents](https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents) (Непрерывная интеграция с Jenkins при помощи агентов виртуальных машин Azure)
* Сведения о поддержке и параметры конфигурации: [вики-сайт по использованию подключаемого модуля Jenkins с агентами виртуальных машин Azure](https://wiki.jenkins-ci.org/display/JENKINS/Azure+VM+Agents+Plugin) 


