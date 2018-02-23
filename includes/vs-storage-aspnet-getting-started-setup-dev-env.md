## <a name="set-up-the-development-environment"></a>Настройка среды разработки

В этом разделе описаны шаги по настройке среды разработки, включая создание приложения ASP.NET MVC, добавление подключения к подключенным службам, добавление контроллера и указание требуемых директив пространства имен.

### <a name="create-an-aspnet-mvc-app-project"></a>Создание проекта приложения ASP.NET MVC

1. Откройте Visual Studio.

1. В главном меню выберите **Файл** > **Создать** > **Проект**.

1. В диалоговом окне **Новый проект** выберите **Веб** > **Веб-приложение ASP.NET (.NET Framework)**. В поле **Имя** введите **StorageAspNet**. Нажмите кнопку **ОК**.

    ![Снимок экрана: диалоговое окно нового проекта](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. В диалоговом окне **Создание веб-приложения ASP.NET** выберите **MVC** и нажмите кнопку **ОК**.

    ![Снимок экрана: диалоговое окно "Создание веб-приложения ASP.NET"](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Подключение к учетной записи хранения Azure с помощью подключенных служб

1. В **обозревателе решений** щелкните проект правой кнопкой мыши.

2. В контекстном меню выберите **Добавить** > **Подключенная служба**.

1. В диалоговом окне **Подключенная служба** выберите **Облачное хранилище в службе хранения Azure**, а затем щелкните **Настройка**.

    ![Снимок экрана: диалоговое окно "Подключенные службы"](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. В диалоговом окне **Служба хранилища Azure** выберите учетную запись хранения Azure, с которой вы будете работать в рамках этого руководства. Чтобы создать учетную запись хранения Azure, выберите **Создание новой учетной записи хранения** и заполните форму. Создав учетную запись или выбрав имеющуюся, нажмите кнопку **Добавить**. После этого Visual Studio установит пакет NuGet для службы хранилища Azure и создаст строку подключения хранилища к **Web.config**.

> [!TIP]
> Сведения о создании учетной записи хранения на [портале Azure](https://portal.azure.com) см. в [этом разделе](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Учетную запись хранения можно также создать с помощью [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md) или [Azure Cloud Shell](../articles/cloud-shell/overview.md).

