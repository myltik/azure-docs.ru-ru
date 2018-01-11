## <a name="set-up-the-development-environment"></a>Настройка среды разработки

В этом разделе описывается настройка среды разработки. Это включает создание приложения ASP.NET MVC, Добавление подключения подключенных служб, добавление контроллера и Указание директивы требуемое пространство имен.

### <a name="create-an-aspnet-mvc-app-project"></a>Создание проекта приложения ASP.NET MVC

1. Откройте Visual Studio.

1. В главном меню выберите **файл** > **New** > **проекта**.

1. В **новый проект** выберите **Web** > **веб-приложения ASP.NET (.NET Framework)**. В **имя** укажите **StorageAspNet**. Нажмите кнопку **ОК**.

    ![Снимок экрана нового проекта-диалоговое окно](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. В **новое веб-приложение ASP.NET** выберите **MVC**, а затем выберите **ОК**.

    ![Снимок экрана для нового веб-приложение ASP.NET-диалоговое окно](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Использовать для подключения к учетной записи хранилища Azure подключенных служб

1. В **обозревателе решений** щелкните проект правой кнопкой мыши.

2. В контекстном меню выберите **добавить** > **подключенной службы**.

1. В **подключенные службы** установите флажок **облачного хранилища с хранилищем Azure**, а затем выберите **Настройка**.

    ![Снимок экрана подключенные службы-диалоговое окно](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. В **хранилища Azure** установите флажок учетной записи хранилища Azure для использования в этом учебнике. Чтобы создать новую учетную запись хранилища Azure, выберите **создать новую учетную запись хранения**и заполните форму. После выбора существующей учетной записи хранения или создать новую, выберите **добавить**. Visual Studio устанавливает пакет NuGet для хранилища Azure и строки подключения хранилища для **Web.config**.

> [!TIP]
> Чтобы узнать, как создать учетную запись хранилища с [портал Azure](https://portal.azure.com), в разделе [создать учетную запись хранилища](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Можно также создать учетную запись хранилища с помощью [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), или [оболочки облако Azure](../articles/cloud-shell/overview.md).

