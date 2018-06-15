---
title: Управление приложениями логики в Visual Studio с помощью Azure Logic Apps | Документация Майкрософт
description: Узнайте, как управлять приложениями логики и другими ресурсами Azure с помощью Visual Studio с помощью Visual Studio Cloud Explorer.
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 03/15/2018
ms.author: estfan; LADocs
ms.openlocfilehash: b4d7f557923a67ae0c9fc513cd2b4fe7555241be
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301122"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Управление приложениями логики в Visual Studio

Хотя вы можете создавать, редактировать, развертывать приложения логики и управлять ими на <a href="https://portal.azure.com" target="_blank">портале Azure</a>, вы также можете использовать Visual Studio, когда нужно добавить эти приложения в систему управления версиями, опубликовать разные версии и создать шаблоны [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) для разных сред развертывания. С помощью Visual Studio Cloud Explorer вы можете найти приложения логики и другие ресурсы Azure и управлять ими. Например, вы можете открывать, загружать, редактировать, запускать, просматривать журнал запусков, а также отключать и включать приложения логики, которые уже развернуты на портале Azure. Если вы ранее не работали с Azure Logic Apps в Visual Studio, [узнайте о создании приложений логики с помощью Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

> [!IMPORTANT]
> Развертывание или публикация приложения логики из Visual Studio перезаписывает версию этого приложения на портале Azure. Поэтому, если вы вносите изменения на портале Azure, которые нужно сохранить, [обновите приложение логики в Visual Studio](#refresh) с портала Azure перед следующим развертыванием или публикацией из Visual Studio.

<a name="requirements"></a>

## <a name="prerequisites"></a>предварительным требованиям

* Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>.

* Скачайте и установите эти средства, если вы еще этого не сделали: 

  * <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2017 или Visual Studio 2015 — выпуски Community или выше</a>. 
  В этом кратком руководстве используется бесплатная версия Visual Studio Community 2017.

  * <a href="https://azure.microsoft.com/downloads/" target="_blank">Пакет SDK Azure (2.9.1 или более поздней версии)</a> и <a href="https://github.com/Azure/azure-powershell#installation" target="_blank">Azure PowerShell</a>.

  * <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551" target="_blank">Средства Azure Logic Apps для Visual Studio 2017</a> или <a href="https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio" target="_blank">Visual Studio 2015</a>. 
  
    Вы можете скачать и установить средства Azure Logic Apps напрямую из Visual Studio Marketplace или узнать, <a href="https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions" target="_blank">как установить это расширение из Visual Studio</a>. 
    После завершения установки перезагрузите Visual Studio.

* Доступ к Интернету при использовании встроенного конструктора Logic Apps

  Конструктору требуется подключение к Интернету, чтобы создать ресурсы в Azure и считать свойства и данные из соединителей в приложении логики. 
  Например, если вы используете соединитель Dynamics CRM Online, конструктор проверяет экземпляр CRM и получает информацию для отображения доступных свойств по умолчанию и пользовательских свойств.

<a name="find-logic-apps-vs"></a>

## <a name="find-your-logic-apps"></a>Поиск приложений логики

В Visual Studio с помощью Cloud Explorer можно найти все приложения логики, которые связаны с подпиской Azure и развернуты на портале Azure.

1. Откройте Visual Studio. В меню **Представление** выберите **Cloud Explorer**.

2. В Cloud Explorer выберите **Управление учетными записями**. Выберите подписку Azure, связанную с приложениями логики, а затем нажмите кнопку **Применить**. Например: 

   ![Выбор параметра "Управление учетными записями"](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

2. В зависимости от выполняемого поиска (по **группам ресурсов** или **их типам**), выполните действия ниже.

   * **Группы ресурсов**: Cloud Explorer отображает все группы ресурсов, связанные с выбранной подпиской Azure. 
   Разверните группу ресурсов, которая содержит ваше приложение логики, а затем выберите его.

   * **Типы ресурсов**: после выбора своей подписки Azure разверните **Приложения логики**. Когда Cloud Explorer отобразит все развернутые приложения логики, связанные с подпиской, выберите свое приложение логики.

<a name="open-designer"></a>

## <a name="open-in-visual-studio"></a>Открытие в Visual Studio

Visual Studio позволяет открыть приложения логики, которые были созданы и развернуты напрямую через портал Azure или в виде проектов Azure Resource Manager с помощью Visual Studio.

1. Откройте Cloud Explorer и найдите свое приложение логики. 

2. В контекстном меню приложения логики выберите **Открыть в редакторе приложений логики**.

   В этом примере показаны приложения логики по типу ресурсов, поэтому они будут отображаться в разделе **Приложения логики**.

  ![Открытие развернутого приложения логики с портала Azure](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Когда приложение логики откроется в конструкторе Logic Apps, в нижней части конструктора можно выбрать **Представление кода**, чтобы просмотреть базовую структуру определения приложения логики. 
   Если вы хотите создать шаблон развертывания для приложения логики, узнайте о [загрузке шаблона Azure Resource Manager](#download-logic-app) для этого приложения. Узнайте больше о [шаблонах Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Скачивание из Azure

Вы можете загрузить приложения логики с <a href="https://portal.azure.com" target="_blank">портала Azure</a> и сохранить их в виде шаблонов [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Затем вы можете локально изменить шаблоны в Visual Studio и настроить приложения логики для разных сред развертывания. При загрузке приложений логики автоматически *параметризуются* их определения внутри [шаблонов Resource Manager](../azure-resource-manager/resource-group-overview.md#template-deployment), которые также используют формат JSON.

1. В Visual Studio откройте Cloud Explorer, затем найдите и выберите приложение логики, которое нужно загрузить с портала Azure.

2. В контекстном меню этого приложения логики выберите **Открыть в редакторе приложений логики**.

   Откроется конструктор приложений логики, отображая соответствующее приложение. 
   Чтобы просмотреть базовое определение и структуру приложения логики, в нижней части конструктора выберите **Представление кода**. 

3. На панели инструментов конструктора щелкните **Загрузить**.

   ![Выбор команды "Загрузить"](./media/manage-logic-apps-with-visual-studio/download-logic-app.png)

4. Когда появится запрос указать расположение, перейдите к нему и сохраните шаблон Resource Manager для определения этого приложения логики в виде JSON-файла. 

Определение вашего приложения логики появится в подразделе `resources` внутри шаблона Resource Manager. Теперь вы можете изменить определение приложения логики и шаблон Resource Manager с помощью Visual Studio. Вы также можете добавить шаблон как проект Azure Resource Manager в решение Visual Studio. Ознакомьтесь с [проектами Resource Manager для приложений логики в Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md). 

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Обновление из Azure

Если вы измените приложение логики на портале Azure, чтобы сохранить эти изменения, необходимо обновить версию этого приложения с изменениями в Visual Studio. 

* В Visual Studio на панели инструментов конструктора приложений логики выберите **Обновить**.

  -или-

* В Cloud Explorer Visual Studio откройте контекстное меню приложения логики и выберите **Обновить**. 

![Обновление приложения логики с изменениями](./media/manage-logic-apps-with-visual-studio/refresh-logic-app.png)

## <a name="publish-logic-app-updates"></a>Публикация обновлений приложения логики

Когда вы будете готовы развернуть обновления приложения логики из Visual Studio в Azure, на панели инструментов конструктора приложений логики выберите **Опубликовать**.

![Публикация обновленного приложения логики](./media/manage-logic-apps-with-visual-studio/publish-logic-app.png)

## <a name="manually-run-your-logic-app"></a>Запуск приложения логики вручную

В Visual Studio вы можете вручную запустить приложение логики, развернутое в Azure. На панели инструментов конструктора приложений логики выберите **Запустить триггер**.

![Запуск приложения логики вручную](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Просмотр журнала выполнения

Чтобы проверить состояние приложения логики и диагностировать проблемы, связанные с запуском этого приложения, можно просмотреть подробные сведения для запусков в Visual Studio, например входные и выходные данные.

1. В Cloud Explorer откройте контекстное меню приложения логики и выберите **Открыть журнал запусков**.

   ![Выбор параметра "Открыть журнал запусков"](./media/manage-logic-apps-with-visual-studio/view-run-history.png)

2. Чтобы просмотреть сведения об определенном запуске, дважды щелкните его. Например: 

   ![Подробный журнал запусков](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Чтобы отсортировать таблицу по свойству, выберите заголовок столбца для этого свойства. 

3. Разверните шаги, входные и выходные данные которых нужно просмотреть. Например: 

   ![Просмотр входных и выходных данных для каждого шага](./media/manage-logic-apps-with-visual-studio/run-inputs-outputs.png)

## <a name="disable-or-enable-logic-app"></a>Отключение и включение приложения логики

Остановить запуск триггера в следующий раз, когда будет выполнено его условие, можно без удаления приложения логики. Отключение приложения логики предотвращает создание и запуск обработчиком Logic Apps будущих экземпляров рабочего процесса для этого приложения.
В Cloud Explorer откройте контекстное меню приложения логики и выберите **Отключить**.

![Отключение приложения логики](./media/manage-logic-apps-with-visual-studio/disable-logic-app.png)

Когда вы будете готовы возобновить работу приложения логики, вы можете повторно активировать его. В Cloud Explorer откройте контекстное меню приложения логики и выберите **Включить**.

![Включение приложения логики](./media/manage-logic-apps-with-visual-studio/enable-logic-app.png)

## <a name="delete-your-logic-app"></a>Удаление приложения логики

Чтобы удалить приложение логики с портала Azure, в Cloud Explorer откройте его контекстное меню и выберите **Удалить**.

![Удаление приложения логики](./media/manage-logic-apps-with-visual-studio/delete-logic-app.png)

## <a name="next-steps"></a>Дополнительная информация

Из этой статьи вы узнали, как управлять развернутыми приложениями логики в Visual Studio. Теперь ознакомьтесь с настройкой определений приложения логики для развертывания:

> [!div class="nextstepaction"]
> [Создание определений для приложений логики с помощью JSON](../logic-apps/logic-apps-author-definitions.md)
