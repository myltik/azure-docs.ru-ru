<properties
   pageTitle="Единый интерфейс Office 365 на всех устройствах"
   description="Узнайте, как опубликовать приложение Office 365 для своих пользователей с помощью RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/14/2015"
   ms.author="guscatal"/>


Единый интерфейс Office 365 на всех устройствах
===================


В этой статье рассматривается развертывание средств Office 365 на устройствах организации. У ваших пользователей будет единый интерфейс и набор функциональных возможностей на устройствах Android, Apple и Windows.

Это становится возможным за счет использования платформы Azure RemoteApp, в рамках которой среда Office 365 размещается на масштабируемых виртуальных машинах Azure, к которым подключаются пользователи. Такой набор виртуальных машин называется облачной коллекцией.

----------


Создание облачной коллекции
-------------
Создав учетную запись Azure, перейдите в раздел RemoteApp, щелкнув ссылку слева. ![Демонстрация Azure RemoteApp на портале Azure](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/1-menu.png)

Затем щелкните «Создать» внизу и выберите режим быстрого создания коллекции. Укажите имя, регион, подписку, план и образ «Office профессиональный 2013», который мы вам предлагаем. ![Создание диалогового окна](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/2-quickcreate.PNG)

После заполнения формы начнется создание коллекции. Эта процедура может занять около часа.

![Ожидание](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/3-waiting.PNG)

После ее завершения вы увидите примерно следующее: Если щелкнуть «Публикация», вы увидите, что основная часть приложений Office уже опубликована. ![Созданная коллекция](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/4-done.PNG)

![Опубликованные приложения](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/5-publish.PNG)

На этом этапе можно добавить пользователей, которым будет доступна эта коллекция (для этого щелкните «Доступ пользователя»). ![настройка доступа пользователей](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/6-user.PNG)

Теперь попытаемся подключиться к Office 365.

Подключение к Office 365
-------------
Перейдите в раздел https://www.remoteapp.windowsazure.com/ и щелкните «Установить клиент», чтобы установить клиент Azure RemoteApp на своем устройстве. Снимки экрана ниже приведены для системы Windows.

После запуска приложения вам будет предложено войти со своим идентификатором Live ID (используйте тот же идентификатор, что и в своей учетной записи Azure). После входа вы увидите уведомление о новых приглашениях. Если его щелкнуть, вы увидите список наподобие представленной ниже. Примите приглашение для адреса электронной почты владельца вашей учетной записи Azure.

![Новое приглашение](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/7-araclient.PNG)

Экран с новыми приглашениями.

![Принятие приглашения](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/8-invitation.PNG)

Приняв приглашение, вы увидите все приложения Office в клиенте Azure RemoteApp.

![Список приложений](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/9-work.PNG)

Если щелкнуть любое из них, оно запустится на виртуальной машине Azure, и все будет готово к работе. Вот и все!

![запуск](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/10-arastart.PNG)

![PowerPoint](https://raw.githubusercontent.com/guscatalano/azure-content/master/articles/media/remoteapp-tutorial-o365anywhere/11-pp.PNG)

<!--HONumber=52-->
 