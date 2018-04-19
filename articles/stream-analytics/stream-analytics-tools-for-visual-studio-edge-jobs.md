---
title: Задания Edge в инструментах Azure Stream Analytics для Visual Studio
description: Из этой статьи вы узнаете, как использовать инструменты Stream Analytics для Visual Studio для разработки, создания и отладки заданий Edge Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/13/2018
ms.openlocfilehash: ec916ea2104df9d694aad2462df7ca5a487017f2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2018
---
# <a name="develop-stream-analytics-edge-jobs-using-visual-studio-tools"></a>Разработка заданий Edge Stream Analytics с помощью средств Visual Studio

В этом руководстве вы узнаете, как использовать средства Stream Analytics для Visual Studio для разработки, создания и отладки заданий Edge Stream Analytics. После создания и тестирования задания можно перейти на портал Azure, чтобы развернуть его на устройствах. 

## <a name="prerequisites"></a>предварительным требованиям

Для работы с данным руководством вам потребуется:

* Установите [Visual Studio 2017](https://www.visualstudio.com/downloads/), [Visual Studio 2015](https://www.visualstudio.com/vs/older-downloads/) или [Visual Studio 2013 с обновлением 4](https://www.microsoft.com/download/details.aspx?id=45326). Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается.  

* Следуйте [инструкциям по установке](stream-analytics-tools-for-visual-studio-edge-jobs.md), чтобы установить средства Stream Analytics для Visual Studio.
 
## <a name="create-a-stream-analytics-edge-project"></a>Создание проекта Stream Analytics Edge 

В Visual Studio выберите **Файл** > **Создать** > **Проект**. Перейдите к списку **Шаблоны** слева, разверните **Azure Stream Analytics** > **Stream Analytics Edge** > **Azure Stream Analytics Edge Application** (Приложение Azure Stream Analytics Edge). Укажите имя, расположение и имя решения для вашего проекта, а затем нажмите кнопку **ОК**.

![Новый проект Edge](./media/stream-analytics-tools-for-visual-studio-edge-jobs/new-edge-project.png)

После создания проекта перейдите в **обозреватель решений**, чтобы просмотреть иерархию папок.

![Представление обозревателя решений](./media/stream-analytics-tools-for-visual-studio-edge-jobs/edge-project-in-solution-explorer.png)

 
## <a name="choose-the-correct-subscription"></a>Выбор соответствующей подписки

1. В меню **Вид** в Visual Studio выберите **Обозреватель серверов**.  

2. Щелкните правой кнопкой мыши **Azure**, выберите **Подключиться к подписке Microsoft Azure** и войдите в систему с помощью учетной записи Azure.

## <a name="define-inputs"></a>Определение входных данных

1. В **обозревателе решений** разверните узел **входных данных**. Должен появиться файл входных данных с именем **EdgeInput.json**. Дважды щелкните его, чтобы просмотреть параметры.  

2. В поле "Тип источника" задайте значение **Поток данных**, в поле "Источник" — **Центр Edge**, в поле "Формат сериализации событий" — **Json**, а в поле "Кодировка" — **UTF8**. При желании вы также можете переименовать **псевдоним входных данных** (в этом примере оставим указанное значение). В случае переименования псевдонима входных данных используйте имя, указанное при определении запроса. Нажмите кнопку **Сохранить**, чтобы сохранить параметры.  
   ![Конфигурация входных данных](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-input-configuration.png)
 


## <a name="define-outputs"></a>Определение выходных данных

1. В **обозревателе решений** разверните узел **выходных данных**. Должен появиться файл выходных данных с именем **EdgeOutput.json**. Дважды щелкните его, чтобы просмотреть параметры.  

2. В поле "Приемник" задайте значение **Центр Edge**, в поле "Формат сериализации событий" — **Json**, в поле "Кодировка" — **UTF8**, а в поле "Формат" — **Массив**. При желании вы также можете переименовать **псевдоним выходных данных** (в этом примере оставим значение по умолчанию). В случае переименования псевдонима выходных данных используйте имя, указанное при определении запроса. Нажмите кнопку **Сохранить**, чтобы сохранить параметры. 
   ![Конфигурация выходных данных](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-output-configuration.png)
 
## <a name="define-the-transformation-query"></a>Определение запроса преобразования

Задания Stream Analytics, развернутые в средах Edge, поддерживают большинство операций, приведенных в [справочнике по языку запросов Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/stream-analytics-query-language-reference?f=255&MSPPError=-2147217396). Однако следующие операции еще не поддерживаются заданиями Edge. 


|**Категория**  | **Команда**  |
|---------|---------|
|Геопространственные операторы |<ul><li>CreatePoint;</li><li>CreatePolygon;</li><li>CreateLineString;</li><li>ST_DISTANCE;</li><li>ST_WITHIN;</li><li>ST_OVERLAPS;</li><li>ST_INTERSECTS.</li></ul> |
|Другие операторы | <ul><li>PARTITION BY;</li><li>TIMESTAMP BY OVER</li><li>DISTINCT</li><li>Параметр выражения в операторе COUNT</li><li>Микросекунды в функциях даты и времени</li><li>JavaScript UDA (эта функция находится в режиме предварительной версии для заданий, развернутых в облаке)</li></ul>   |

При создании задания Edge на портале компилятор автоматически предупредит вас, если вы используете неподдерживаемый оператор.

В Visual Studio определите следующий запрос преобразования в редакторе запросов (**script.asaql file**).

```sql
SELECT * INTO EdgeOutput
FROM EdgeInput 
```

## <a name="test-the-job-locally"></a>Локальное тестирование задания

Чтобы протестировать запрос локально, необходимо передать образец данных. Образец данных можно получить, скачав регистрационные данные из [репозитория GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/Registration.json) и сохранив их на локальном компьютере. 

1. Чтобы передать образец данных, щелкните правой кнопкой мыши файл **EdgeInput.json** и выберите **Add Local Input** (Добавить локальные входные данные).  

2. Во всплывающем окне **перейдите** к примеру данных из локальной папки и нажмите кнопку **Сохранить**.
   ![Конфигурация локальных входных данных](./media/stream-analytics-tools-for-visual-studio-edge-jobs/stream-analytics-local-input-configuration.png)
 
3. Файл **local_EdgeInput.json** будет автоматически добавлен в папку входных данных.  
4. Вы можете запустить его локально или отправить в Azure. Чтобы выполнить тестирование запроса, щелкните **Run Locally** (Запустить локально).  
   ![Параметры запуска](./media/stream-analytics-tools-for-visual-studio-edge-jobs/run-options.png)
 
5. В окне командной строки показано состояние задания. Если задание выполняется успешно, в папке проекта "Visual Studio 2015\Projects\MyASAEdgejob\MyASAEdgejob\ASALocalRun\2018-02-23-11-31-42" создается папка, например, "2018-02-23-11-31-42". Перейдите к этой папке, чтобы просмотреть результаты в локальной папке:

   Вы также можете войти на портал Azure и убедиться, что задание создано. 

   ![Папка результатов](./media/stream-analytics-tools-for-visual-studio-edge-jobs/result-folder.png)

## <a name="submit-the-job-to-azure"></a>Отправка задания в Azure

1. Прежде чем отправлять задание, необходимо подключиться к подписке Azure. Откройте **обозреватель сервера**, щелкните правой кнопкой мыши **Azure** > **Подключиться к подписке Microsoft Azure** и войдите в вашу подписку Azure.  

2. Чтобы отправить задание в Azure, перейдите к редактору запросов и выберите **Отправить в Azure**.  

3. Откроется всплывающее окно, в котором можно создать задание Edge или обновить имеющееся. Если обновить имеющееся задание, будут заменены все параметры задания и в этом сценарии будет опубликовано новое задание. Выберите **Create a New Azure Stream Analytics Job** (Создать задание Azure Stream Analytics), введите имя для этого задания, например **MyASAEdgeJob**, выберите необходимые **подписку**, **группу ресурсов** и **расположение**, а затем нажмите кнопку **Отправить**.

   ![Отправка в Azure](./media/stream-analytics-tools-for-visual-studio-edge-jobs/submit-to-azure.png)
 
   После создания задания Stream Analytics Edge можно перейти к [руководству по запуску заданий в IoT Edge](stream-analytics-edge.md), чтобы узнать, как развертывать задания на устройствах. 

## <a name="manage-the-job"></a>Управление заданием 

Вы можете просмотреть состояние и схему заданий в обозревателе сервера. В **обозревателе сервера**  >  выберите **Stream Analytics**, разверните подписку и группу ресурсов, в которых развернуто задание Edge, и вы увидите задание MyASAEdgejob с состоянием **Создано**. Разверните узел задания и дважды щелкните его, чтобы открыть представление задания.

![Параметры обозревателя сервера](./media/stream-analytics-tools-for-visual-studio-edge-jobs/server-explorer-options.png)
 
В окне представления задания можно выполнить операции, такие как обновление, удаление задания, открытие задания с портала Azure и т. д.

![Схема задания и другие параметры](./media/stream-analytics-tools-for-visual-studio-edge-jobs/job-diagram-and-other-options.png) 

## <a name="next-steps"></a>Дополнительная информация

* [Что такое Azure IoT Edge (предварительная версия)](../iot-edge/how-iot-edge-works.md)
* [Deploy Azure Stream Analytics as an IoT Edge module - preview](../iot-edge/tutorial-deploy-stream-analytics.md) (Развертывание Azure Stream Analytics в качестве модуля IoT Edge (предварительная версия))
* [Отправьте отзыв команде с помощью этого опроса](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2czagZ-i_9Cg6NhAZlH9ypUMjNEM0RDVU9CVTBQWDdYTlk0UDNTTFdUTC4u) 
