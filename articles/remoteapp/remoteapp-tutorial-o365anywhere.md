---
title: "Единый интерфейс Office 365 на всех устройствах c Azure RemoteApp | Документация Майкрософт"
description: "Узнайте, как опубликовать приложение Office 365 для своих пользователей с помощью Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 0c971ce9-7d45-4cfb-9737-15b6706047e8
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: guscatal;elizapo
translationtype: Human Translation
ms.sourcegitcommit: 300ee477a00cdd463a2abf87dd73550777fd9e05
ms.openlocfilehash: 13634bec8abbefdf941066dece8390f2fd064422


---
# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Единый интерфейс Office 365 на всех устройствах c Azure RemoteApp
> [!IMPORTANT]
> Мы выводим удаленное приложение Azure RemoteApp из эксплуатации. Дополнительные сведения см. в [объявлении](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

В этой статье рассматривается развертывание средств Office 365 на устройствах организации. У ваших пользователей будет единый интерфейс и набор функциональных возможностей на устройствах Android, Apple и Windows.

Это достигается с помощью платформы Azure RemoteApp — она позволяет размещать Office 365 на масштабируемых виртуальных машинах Azure, к которым подключаются пользователи. Такой набор виртуальных машин называется «облачной коллекцией».

## <a name="create-a-cloud-collection"></a>Создание облачной коллекции
Создав учетную запись Azure, перейдите в раздел **RemoteApp** , щелкнув ссылку слева.
![Демонстрация Azure RemoteApp на портале Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Затем нажмите внизу **Создать** и выберите режим быстрого создания коллекции. Укажите имя, регион, подписку, план и образ "Office профессиональный 2013", который мы вам предлагаем.
![Создание диалогового окна](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

После заполнения формы начнется создание коллекции. Эта процедура может занять около часа.

![Ожидание](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

После ее завершения вы увидите примерно следующее: Если щелкнуть **Публикация**, вы увидите, что основная часть приложений Office уже опубликована.
![Созданная коллекция](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Опубликованные приложения](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

На этом этапе можно добавить пользователей, которым будет доступна эта коллекция. Для этого щелкните **Доступ пользователя**.
![настройка доступа пользователей](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Теперь попытаемся подключиться к Office 365.

## <a name="connect-to-office-365"></a>Подключение к Office 365
Откройте страницу [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), прокрутите ее вниз и нажмите кнопку **Скачать клиент**, чтобы установить клиент Azure RemoteApp на свое устройство. Снимки экрана ниже приведены для системы Windows.

После запуска приложения вам будет предложено выполнить вход по учетной записи Майкрософт (которая раньше называлась идентификатором Live ID). Она совпадает с вашей учетной записью Azure. Выполнив вход, вы увидите уведомление о новых приглашениях, щелкнув которое, вы откроете список представленного ниже вида. Примите приглашение, соответствующее вашему адресу электронной почты владельца учетной записи Azure.

![Новое приглашение](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Экран с новыми приглашениями.

![Принятие приглашения](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Приняв приглашение, вы увидите все приложения Office в клиенте Azure RemoteApp.

![Список приложений](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Если щелкнуть любое из них, оно запустится на виртуальной машине Azure, и все будет готово к работе. Так просто!

![запуск](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)




<!--HONumber=Dec16_HO1-->


