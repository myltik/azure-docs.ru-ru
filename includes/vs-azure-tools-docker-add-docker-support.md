1. В **обозревателе решений** Visual Studio щелкните проект правой кнопкой мыши и выберите в контекстном меню **Добавить > Docker Support** (Поддержка Docker).
   
    ![Команда добавления поддержки Docker в контекстном меню](media/vs-azure-tools-docker-add-docker-support/docker-support-context-menu.png)
2. Добавление поддержки Docker в веб-проект ASP.NET Core приводит к добавлению в проект нескольких связанных с Docker файлов, включая файлы Docker-Compose, сценарии развертывания Windows PowerShell и файлы свойств Docker. 
   
    ![Файлы Docker, добавленные в проект](media/vs-azure-tools-docker-add-docker-support/docker-files-added.png)

> [!NOTE]
> При использовании [бета-версии Docker для Windows](https://beta.docker.com) откройте файл Properties\Docker.props и удалите значение по умолчанию, а затем перезапустите Visual Studio, чтобы значение вступило в силу.
> 
> ```
> <DockerMachineName Condition="'$(DockerMachineName)'=='' "></DockerMachineName>
> ```
> 



<!--HONumber=Nov16_HO3-->


