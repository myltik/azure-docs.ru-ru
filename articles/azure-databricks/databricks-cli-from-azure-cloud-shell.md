---
title: Использование интерфейса командной строки Databricks через Azure Cloud Shell | Документация Майкрософт
description: Сведения об использовании интерфейса командной строки Databricks через Azure Cloud Shell.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: c20ad02f962fbee22bb16653c5eab351d9f3de17
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598731"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Использование интерфейса командной строки Databricks через Azure Cloud Shell

Сведения об использовании интерфейса командной строки Databricks через Azure Cloud Shell при работе в Databricks.

## <a name="prerequisites"></a>предварительным требованиям

* Рабочее пространство и кластер в Azure Databricks. Инструкции см. в статье [Начало работы с Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Настройте личный маркер доступа в Databricks. Инструкции см. в разделе об [управлении маркерами](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Использование Azure Cloud Shell

1. Войдите на [портал Azure](https://portal.azure.com).
 
2. В правом верхнем углу выберите значок **Cloud Shell**.

   ![Запустить Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Запустить ODBC из Excel")

3. Убедитесь, что для среды Cloud Shell была выбрана **Bash**. Как показано на следующем снимке экрана, эту среду можно выбрать в раскрывающемся списке.

   ![Запустить Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Запустить ODBC из Excel") 

4. Создайте виртуальную среду, в которую можно будет установить интерфейс командной строки Databtricks. Используя фрагмент кода ниже, создайте виртуальную среду, которая называется `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Переключитесь на созданную вами виртуальную среду.

       source databrickscli/bin/activate

6. Установите интерфейс командной строки Databricks.

       pip install databricks-cli

7. Установите проверку подлинности с помощью Databricks, используя созданный маркер доступа, который был перечислен в списке необходимых компонентов. Используйте следующую команду:

       databricks configure --token

    Перед вами отобразятся следующие запросы.

    * Появится запрос на указание узла Databricks. Укажите значение в формате `https://eastus2.azuredatabricks.net`. Здесь **East US 2** — это регион в Azure, в котором была создана рабочая область Azure Databricks.

    * Появится запрос на ввод имени пользователя. Введите **маркер**.

    * Наконец, появится запрос на ввод пароля. Введите маркер, который был создан ранее.

После выполнения этих действий можно начинать использовать интерфейс командной строки Databricks в Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Использование интерфейса командной строки Databricks

Теперь интерфейс командной строки Databricks можно использовать. Выполните, к примеру, следующую команду, чтобы вывести список всех кластеров Databricks, которые присутствуют в вашей рабочей области.

    databricks clusters list

Чтобы получить доступ к файловой системе Databricks (DBFS), можно использовать следующую команду:

    databricks fs ls


Полный справочник по командам см. в статье [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Интерфейс командной строки Databricks).


## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения об интерфейсе командной строки Azure см. в статье [Обзор Azure Cloud Shell](../cloud-shell/overview.md)
* Список команд для интерфейса командной строки Azure см. в статье [Azure CLI reference](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) (Справочные материалы по интерфейсу командной строки Azure)
* Список команд для интерфейса командной строки Databricks см.в статье [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Интерфейс командной строки Databricks)


