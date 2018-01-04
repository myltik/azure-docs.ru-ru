## <a name="set-up-the-development-environment"></a>Настройка среды разработки

В этом разделе описывается настройка среды разработки, включая создание приложения ASP.NET MVC, Добавление подключения подключенные службы, добавление контроллера и Указание директивы требуемое пространство имен.

### <a name="create-an-aspnet-mvc-app-project"></a>Создание проекта приложения ASP.NET MVC

1. Откройте Visual Studio.

1. В главном меню последовательно выберите **Файл -> Создать -> Проект**.

1. В диалоговом окне **Создание проекта** задайте параметры, как показано на следующем рисунке.

    ![Создание проекта ASP.NET](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Нажмите кнопку **ОК**.

1. В диалоговом окне **Новый проект ASP.NET** задайте параметры, как показано на следующем рисунке.

    ![Указание параметров для проекта MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Нажмите кнопку **ОК**.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Подключение к учетной записи хранения Azure с помощью подключенных служб

1. В **обозревателе решений** щелкните проект правой кнопкой мыши и выберите в контекстном меню **Добавить > Подключенная служба**.

1. На **добавление подключенной службы** диалогового окна выберите **облачного хранилища с хранилищем Azure**, а затем выберите **Настройка**.

    ![Диалоговое окно подключенной службы](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. На **хранилища Azure** диалогового окна выберите учетную запись хранилища Azure, используемый для этого учебника.  Чтобы создать новую учетную запись хранилища Azure, щелкните **создать новую учетную запись хранения** и заполните форму.  После выбора существующей учетной записи хранения или создавать новый, нажмите кнопку **добавить**.  Visual Studio установит пакет NuGet для хранилища Azure и строки подключения хранилища для **Web.config**.

> [!TIP]
> Чтобы узнать, как создать учетную запись хранилища с [портал Azure](https://portal.azure.com), в разделе [создать учетную запись хранилища](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Учетная запись хранилища Azure можно также создать с помощью [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), или [оболочки облако Azure](../articles/cloud-shell/overview.md).

