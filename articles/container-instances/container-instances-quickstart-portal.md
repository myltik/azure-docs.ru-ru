---
title: Краткое руководство. Создание первого контейнера службы "Экземпляры контейнеров Azure" с помощью портала Azure
description: В этом кратком руководстве при помощи портала Azure вы развернете контейнер в службе "Экземпляры контейнеров Azure".
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: cb0c8c5f5730ae1f7a2e9b38c3ef3e04ee8cde67
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2018
---
# <a name="quickstart-create-your-first-container-in-azure-container-instances"></a>Краткое руководство. Создание первого контейнера в службе "Экземпляры контейнеров Azure"

Служба "Экземпляры контейнеров Azure" упрощает создание контейнеров и управление ими в Azure. В этом кратком руководстве вы создадите контейнер в Azure и предоставите к нему доступ в Интернете по общедоступному IP-адресу. Эту операцию можно выполнить на портале Azure. Всего несколько щелчков, и вы увидите в браузере такое:

![Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][aci-portal-07]

## <a name="log-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="create-a-container-instance"></a>Создание экземпляра контейнера

Последовательно выберите **Создать ресурс** > **Контейнеры** > **Azure Container Instances** (Экземпляры контейнеров Azure).

![Начало создания экземпляра контейнера на портале Azure][aci-portal-01]

Введите следующие значения в текстовые поля **Имя контейнера**, **Container image** (Образ контейнера) и **Группа ресурсов**. Оставьте другие значения по умолчанию, а затем нажмите кнопку **ОК**.

* Имя контейнера: `mycontainer`
* Образ контейнера: `microsoft/aci-helloworld`
* группу ресурсов `myResourceGroup`;

![Настройка основных параметров для нового экземпляра контейнера на портале Azure][aci-portal-03]

Вы можете создать контейнеры Windows и Linux в службе "Экземпляры контейнеров Azure". В этом кратком руководстве мы оставим значение по умолчанию **Linux**, так как на предыдущем шаге мы указали контейнер на базе Linux (`microsoft/aci-helloworld`).

Оставьте значения по умолчанию остальных параметров в разделе **настройки** и нажмите кнопку **ОК** для проверки конфигурации.

![Настройка нового экземпляра контейнера на портале Azure][aci-portal-04]

После завершения проверки будет отображена сводка параметров контейнера. Нажмите кнопку **ОК**, чтобы отправить запрос на развертывание контейнера.

![Сводка параметров для нового экземпляра контейнера на портале Azure][aci-portal-05]

При запуске развертывания на панели мониторинга портала отображается плитка, показывающая ход развертывания. После завершения развертывания плитка обновляется для отображения новой группы контейнеров **mycontainer-myc1**.

![Ход создания нового экземпляра контейнера на портале Azure][aci-portal-08]

Для отображения свойств группы контейнеров выберите группу контейнеров **mycontainer-myc1**. Запишите **IP-адрес** группы контейнеров, а также **состояние** контейнера.

![Обзор группы контейнеров на портале Azure][aci-portal-06]

Когда контейнер перейдет в состояние **Выполняется**, перейдите к IP-адресу, записанному на предыдущем шаге, чтобы отобразить приложение, размещенное в вашем новом контейнере.

![Приложение, развернутое с помощью службы "Экземпляры контейнеров Azure" (просмотр в браузере)][aci-portal-07]

## <a name="delete-the-container"></a>Удаление контейнера
По завершении работы с контейнером выберите группу контейнеров **mycontainer-myc1** и нажмите кнопку **Удалить**.

![Удаление экземпляра контейнера на портале Azure][aci-portal-09]

Отобразится диалоговое окно подтверждения. Нажмите кнопку **Да** при появлении запроса.

![Подтверждение удаления экземпляра контейнера на портале Azure][aci-portal-10]

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
<!--[aci-portal-02]: ./media/container-instances-quickstart-portal/qs-portal-02.png-->
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали экземпляр контейнера Azure из образа в общедоступном репозитории Docker Hub. Если вы хотите выполнить сборку самостоятельно и развернуть контейнер в службе "Экземпляры контейнеров Azure" с помощью реестра контейнеров Azure, продолжайте изучение руководств по этой службе.

> [!div class="nextstepaction"]
> [Руководства по использованию службы "Экземпляры контейнеров Azure"](./container-instances-tutorial-prepare-app.md)