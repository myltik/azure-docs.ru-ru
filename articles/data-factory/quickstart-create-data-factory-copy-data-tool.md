---
title: "Копирование данных с помощью средства копирования данных Azure | Документация Майкрософт"
description: "Создание фабрики данных Azure и применение средства копирования данных для копирования данных из одной папки в другую папку в хранилище BLOB-объектов Azure."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: c17e738e3db18503f7cdda24a5f01ceb78e4e6a3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/18/2018
---
# <a name="use-copy-data-tool-to-copy-data"></a>Использование средства копирования данных для копирования данных 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1 — общедоступная](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Версия 2 — предварительная](quickstart-create-data-factory-copy-data-tool.md)

В этом руководстве вы создадите фабрику данных с помощью портала Azure. После этого вы примените средство копирования данных, чтобы создать конвейер для копирования данных из одной папки в другую папку в хранилище BLOB-объектов Azure. 

> [!NOTE]
> Если вы еще не работали с фабрикой данных Azure, ознакомьтесь со статьей [Введение в фабрику данных Azure](data-factory-introduction.md) перед изучением этого руководства. 
>
> Эта статья относится к версии 2 фабрики данных, которая в настоящее время доступна в предварительной версии. Если вы используете общедоступную версию 1 службы фабрики данных, см. статью о [начале работы со службой фабрики данных версии 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Создание фабрики данных

1. В меню слева щелкните **Создать**, выберите **Данные+аналитика** и щелкните **Фабрика данных**. 
   
   ![Создать -> Фабрика данных](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. На странице **Новая фабрика данных** введите **ADFTutorialDataFactory** в поле **Имя**. 
      
     ![Страница "Новая фабрика данных"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Имя фабрики данных Azure должно быть **глобально уникальным**. Если вы увидите следующую ошибку для поля имени, введите другое имя фабрики данных (например, ваше_имя_ADFTutorialDataFactory) и снова попробуйте создать ее. Ознакомьтесь со статьей [Фабрика данных Azure — правила именования](naming-rules.md), чтобы узнать правила именования для артефактов службы "Фабрика данных".
  
     ![Ошибка, связанная с недоступностью имени](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Выберите **подписку** Azure, в рамках которой вы хотите создать фабрику данных. 
4. Для **группы ресурсов** выполните одно из следующих действий.
     
      - Выберите **Использовать существующую**и укажите существующую группу ресурсов в раскрывающемся списке. 
      - Выберите **Создать новую**и укажите имя группы ресурсов.   
         
      Сведения о группах ресурсов см. в статье, где описывается [использование групп ресурсов для управления ресурсами Azure](../azure-resource-manager/resource-group-overview.md).  
4. Выберите **V2 (Preview)** (V2 (предварительная версия)) для **версии**.
5. Укажите **расположение** фабрики данных. В раскрывающемся списке отображаются только поддерживаемые расположения. Хранилища данных (служба хранилища Azure, база данных SQL Azure и т. д.) и вычисления (HDInsight и т. д.), используемые фабрикой данных, могут располагаться в других расположениях и (или) регионах.
6. Кроме того, установите флажок **Закрепить на панели мониторинга**.     
7. Нажмите кнопку **Создать**.
8. На панели мониторинга вы увидите приведенный ниже элемент с состоянием **Deploying data factory** (Развертывание фабрики данных). 

    ![Элемент Deploying data factory (Развертывание фабрики данных)](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Когда завершится создание, откроется страница **Фабрика данных**, как показано на рисунке ниже.
   
   ![Домашняя страница фабрики данных](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)
10. Щелкните плитку **Создание и мониторинг**, чтобы открыть на отдельной вкладке пользовательский интерфейс фабрики данных Azure. 

## <a name="launch-copy-data-tool"></a>Запуск средства копирования данных

1. На странице начала работы щелкните плитку **Копировать данные**, чтобы запустить средство копирования данных. 

   ![Плитка средства копирования данных](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. На странице **свойств** для средства копирования данных щелкните **Далее**. На этой странице вы можете указать имя и описание конвейера. 

    ![Средство копирования данных — страница свойств](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. На странице **исходного хранилища данных** выберите **хранилище BLOB-объектов Azure** и щелкните **Next** (Далее).

    ![Страница исходного хранилища данных](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. На странице **Specify the Azure Blob storage account** (Указание учетной записи хранилища BLOB-объектов Azure) выберите **имя учетной записи хранения** из раскрывающегося списка и щелкните Next (Далее). 

    ![Выбор учетной записи хранилища BLOB-объектов](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. На странице **Choose the output file or folder** (Выбор файла или папки входных данных) выполните следующие действия.

    1. Перейдите к папке **adftutorial/input**. 
    2. Выберите файл **emp.txt**.
    3. Щелкните **Choose** (Выбрать). Вместо этого можно просто дважды щелкнуть файл **emp.txt**. 
    4. Нажмите кнопку **Далее**. 

    ![Выбор файла или папки входных данных](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. На странице **File format settings** (Параметры формата файла) проверьте, что средство правильно автоматически определило разделители столбцов и строк, и щелкните **Next** (Далее). Также на этой странице вы можете просмотреть данные и схему входных данных. 

    ![Страница параметров формата файла](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. На странице **целевого хранилища данных** выберите **хранилище BLOB-объектов Azure** и нажмите кнопку **Next** (Далее). 

    ![Страница целевого хранилища данных](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. На странице **Specify the Azure Blob storage account** (Указание учетной записи хранилища BLOB-объектов Azure) выберите имя учетной записи хранилища и щелкните **Next** (Далее). 

    ![Страница выбора хранилища данных в качестве приемника](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. На странице **Choose the output file or folder** (Выбор файла или папки выходных данных) выполните следующие действия. 

    1. Введите **adftutorial/output** в поле **пути к папке**.
    2. Введите **emp.txt** в поле **имени файла**. 
    3. Нажмите кнопку **Далее**. 

    ![Выбор файла или папки выходных данных](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. На странице **File format settings** (Параметры формата файла) щелкните **Next** (Далее). 

    ![Страница параметров формата файла](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Щелкните **Далее** на странице **Параметры**. 

    ![Страница "Дополнительные параметры"](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Проверьте все параметры на странице **Summary** (Сводка) и щелкните Next (Далее). 

    ![Страница "Сводка"](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. На странице **Deployment complete** (Развертывание завершено) щелкните **Monitor** (Мониторинг), чтобы отслеживать созданный конвейер. 

    ![Страница развертывания](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. Приложение переключится на вкладку **Мониторинг**. На этой странице вы увидите состояние конвейера. Щелкните **Обновить**, чтобы обновить список. 
    
    ![Страница мониторинга запусков конвейера](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Щелкните ссылку **View Activity Runs** (Просмотр запусков действий) в столбце Actions (Действия). Наш конвейер содержит только одно действие типа **Копирование**. 

    ![Страница 	"Выполнения действий"](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Чтобы просмотреть сведения об операции копирования, щелкните ссылку **Сведения** (изображение очков) в столбце **Действия**. Дополнительные сведения о свойствах см. в статье [Действие копирования в фабрике данных Azure](copy-activity-overview.md). 

    ![Сведения об операции копирования](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Убедитесь, что файл **emp.txt** создан в папке **output** в контейнере **adftutorial**. Если указанная папка выходных данных не существует, служба фабрики данных автоматически создаст ее. 
18. Перейдите на вкладку **Изменить**, чтобы изменить связанные службы, наборы данных и конвейеры. Дополнительные сведения об их изменении в пользовательском интерфейсе фабрики данных см. в статье [Создание фабрики данных с помощью портала Azure](quickstart-create-data-factory-portal.md).

    ![Вкладка редактирования](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Дополнительная информация
В этом примере конвейер копирует данные из одного расположения в другое в хранилище BLOB-объектов Azure. Перейдите к [руководствам](tutorial-copy-data-portal.md), чтобы узнать об использовании фабрики данных в различных сценариях. 