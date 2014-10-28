<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Browse and Edit Your Source Code | Azure" metaKeywords="Visual Studio Online, VSO, git, tfvc, edit, code, commit" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow"></tags>

# Просмотр и изменение исходного кода

На портале предварительной версии Microsoft Azure можно легко просматривать и изменять исходный код командного проекта Visual Studio Online.

После создания командного проекта на портале и регистрации (в системе управления версиями Team Foundation) или фиксации (git) кода можно легко получить доступ у этому коду и просматривать его с помощью портала.

1.  Сначала откройте панель командного проекта, а затем найдите область кода. Затем в исходной части щелкните имя репозитория, который требуется просмотреть (в данном случае он называется "BrowseCode"). Примечание. Если имеется более двух репозиториев, можно щелкнуть номер репозитория (в данном случае 1), чтобы просмотреть полный список.
    ![Область кода][Область кода]
2.  Теперь, когда панель репозитория открыта, можно щелкнуть часть кода, чтобы начать просмотр исходного кода.
    ![Раздел репозитория][Раздел репозитория]
3.  Появится раздел со стандартной навигацией по представлению в виде дерева, в котором доступен весь проект. Можно щелкать узлы, чтобы открыть папку, или щелкнуть конкретный файл (в данном случае Index.cshtml), чтобы просмотреть его или изменить.
    ![Дерево навигации][Дерево навигации]
4.  Чтобы изменить файл, просто нажмите кнопку "Правка" в панели команд. Содержимое файла откроется в режиме редактирования, и можно будет внести изменения. Подсказка. Нажмите кнопку развертывания в верхнем правом углу, чтобы получить полноэкранное представление.
    ![Режим редактирования][Режим редактирования]
5.  Завершив внесение изменений, нажмите кнопку фиксации (или кнопку регистрации) в панели команд. Появится новая панель, в которой можно ввести сообщение фиксации. Нажмите кнопку "ОК", и файл будет зафиксирован в репозитории.
    ![Код фиксации][Код фиксации]
6.  В верхней части панели появится мигающее сообщение об успешности фиксации в репозитории.
    ![Успешная фиксация][Успешная фиксация]

  [Область кода]: ./media/visual-studio-online-browse-edit-source-code/Code-Lens.png
  [Раздел репозитория]: ./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png
  [Дерево навигации]: ./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png
  [Режим редактирования]: ./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png
  [Код фиксации]: ./media/visual-studio-online-browse-edit-source-code/Commit-Code.png
  [Успешная фиксация]: ./media/visual-studio-online-browse-edit-source-code/Commit-Success.png
