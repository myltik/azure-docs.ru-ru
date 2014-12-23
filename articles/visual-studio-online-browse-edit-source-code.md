<properties urlDisplayName="Browse and Edit Your Source Code" pageTitle="Просмотр и редактирование исходного кода | MetaKeywords Azure"="Visual Studio Online, VSO, git, tfvc, редактирование, код, фиксация" description="Learn how to edit your source code." metaCanonical="" services="visual-studio-online" documentationCenter="" title="Browse and Edit Your Source Code" authors="ehollow" solutions="" manager="kamrani" editor="" />

<tags ms.service="visual-studio-online" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="ehollow" />

# Просмотр и изменение исходного кода

На портале предварительной версии Microsoft Azure можно легко просматривать и изменять исходный код командного проекта Visual Studio Online.


После создания командного проекта на портале и регистрации (в системе управления версиями Team Foundation) или фиксации (git) кода можно легко получить доступ у этому коду и просматривать его с помощью портала.


1. Сначала откройте панель проекта группы, затем найдите область кода.  Затем в исходной части щелкните имя репозитория, который требуется просмотреть (в данном случае он называется "BrowseCode").  Примечание. Если репозиториев больше двух, можно щелкнуть номер репозитория (в данном случае 1), чтобы просмотреть полный список.
![Code Lens](./media/visual-studio-online-browse-edit-source-code/Code-Lens.png)
2. Теперь, когда панель репозитория открыта, можно начать просмотр исходного кода, щелкнув в части кода.
![Repository Blade](./media/visual-studio-online-browse-edit-source-code/Repo-Blade.png)
3. Появится панель со стандартной навигацией в виде дерева, в котором будет доступен весь проект.  Можно щелкнуть узлы, чтобы открыть папку, либо щелкнуть конкретный файл (в данном случае Index.cshtml), чтобы просмотреть или изменить его.
![Tree Navigation](./media/visual-studio-online-browse-edit-source-code/Tree-Nav.png)
4. Чтобы изменить файл, просто нажмите кнопку "Правка" в панели команд.  Содержимое файла откроется в режиме редактирования, и можно будет внести изменения. Подсказка. Нажмите кнопку развертывания в верхнем правом углу для редактирования в полноэкранном режиме.
![Edit Mode](./media/visual-studio-online-browse-edit-source-code/Edit-Mode.png)
5. Завершив внесение изменений, нажмите кнопку фиксации (или кнопку регистрации) в панели команд.  Появится новая панель, в которой можно ввести сообщение фиксации.  Когда закончите работу, нажмите кнопку "ОК", и файл будет зафиксирован в репозитории.
![Commit Code](./media/visual-studio-online-browse-edit-source-code/Commit-Code.png)
6. В верхней части панели появится мигающее сообщение об успешной фиксации в репозитории.
![Commit Success](./media/visual-studio-online-browse-edit-source-code/Commit-Success.png)







