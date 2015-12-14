<properties
	pageTitle="Установка и настройка Azure PowerShell"
	description="Сведения об установке и настройке Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="coreyp"/>

# Установка и настройка Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Интерфейс командной строки Azure">Интерфейс командной строки Azure</a></div>

##Что такое Azure PowerShell?#
Azure PowerShell — это модуль, предоставляющий командлеты для управления Azure с помощью Windows PowerShell. Эти командлеты можно использовать для создания, тестирования, развертывания решений и служб на платформе Azure и управления ими. В большинстве случаев командлеты можно использовать для выполнения тех же задач, что и портал управления Azure, например, для создания и настройки облачных служб, виртуальных машин, виртуальных сетей и веб-приложений.

Модуль и исходный код доступны для скачивания в общедоступном репозитории.

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Исходный код Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## Шаг 1. Установка
Скачайте и установите [Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi). <a id="Connect"></a>

## Шаг 2. Запуск
При установке модуля также устанавливается специальная консоль для Azure PowerShell. Командлеты можно выполнять в стандартной консоли Windows PowerShell или в консоли Azure PowerShell.

## Шаг 3. Подключение
Командлетам необходима ваша подписка для управления службами. Вы можете купить подписку Azure, если у вас ее еще нет. Указания см. в разделе [Приступая к работе с Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Введите Add-AzureAccount.

2. Введите электронный адрес и пароль, связанные с вашей учетной записью. Azure выполняет проверку подлинности и сохраняет учетные данные, а затем закрывает окно.

ИЛИ

Войдите с помощью рабочей или учебной учетной записи.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	>
	> [AZURE.NOTE] This non-interactive login method only works with a work or school account. A work or school account is a user that is managed by your work or school, and defined in the Azure Active Directory instance for your work or school. If you do not currently have a work or school account, and are using a Microsoft account to log in to your Azure subscription, you can easily create one using the following steps.
	>
	> 1. Login to the [Azure Management Portal](https://manage.windowsazure.com), and click on **Active Directory**.
	>
	> 2. If no directory exists, select **Create your directory** and provide the requested information.
	>
	> 3. Select your directory and add a new user. This new user can sign in using a work or school account.
	>
	>     During the creation of the user, you will be supplied with both an e-mail address for the user and a temporary password. Save this  information as it is used in another step.
	>
	> 4. From the management portal, select **Settings** and then select **Administrators**. Select **Add**, and add the new user as a co-administrator. This allows the work or school account to manage your Azure subscription.
	>
	> 5. Finally, log out of the Azure portal and then log back in using the work or school account. If this is the first time logging in with this account, you will be prompted to change the password.
	>
	>For more information on signing up for Microsoft Azure with a work or school account, see [Sign up for Microsoft Azure as an Organization](sign-up-organization.md).


### Просмотр сведений учетной записи и подписки

У вас может быть несколько учетных записей и подписок, доступных для использования в Azure PowerShell. Можно добавить несколько учетных записей, выполнив командлет **Add-AzureAccount** несколько раз.

Чтобы просмотреть доступные учетные записи Azure, введите:

	Get-AzureAccount

Чтобы просмотреть подписки Azure, введите:

	Get-AzureSubscription








## Шаг 4. Проверка<a id="Ex"></a>


После установки модуля и настройки компьютера для подключения к вашей подписке вы можете создать веб-приложение Azure, чтобы проверить, правильно ли все работает.

1. Запустите консоль Azure PowerShell.

2. Выберите имя для веб-приложения. Необходимо использовать имя, соответствующее правилам именования DNS. Допустимые имена могут содержать только буквы a–z, цифры от 0 до 9 и дефис ("-").

	Имя веб-приложения должно быть уникальным в рамках Azure. В примере мы будем использовать mySite, но постарайтесь выбрать другое имя, например имя учетной записи с номером.

	После выбора имени введите команду, аналогичную следующей. Замените mySite именем своего веб-приложения.

		New-AzureWebsite mySite

	Командлет создает веб-приложение и возвращает объект, который представляет новое веб-приложение. В свойства этого объекта входит полезная информация о веб-приложении.

3. Чтобы получить информацию о веб-приложении, введите такую команду. Она возвращает информацию обо всех веб-приложениях в подписке, в том числе и о только что созданном.

		Get-AzureWebsite

4. Для получения дополнительной информации о веб-приложении укажите в команде имя веб-приложения. Обязательно укажите имя своего веб-приложения вместо mySite.

		Get-AzureWebsite -Name mySite

5. Веб-приложения запускаются после их создания. Чтобы остановить веб-приложение, введите эту команду и имя своего веб-приложения.

		Stop-AzureWebsite -Name mySite

6. Чтобы убедиться, что сайт в состоянии "остановлен", запустите команду Get-AzureWebsite еще раз.

		Get-AzureWebsite

7. Для завершения этого теста удалите веб-приложение. Тип:

		Remove-AzureWebsite -Name mySite

7. Чтобы завершить задачу, удалите веб-приложение.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Получение справки##

В этих ресурсах вы найдете справку для определенных командлетов:


-   В консоли можно использовать встроенную справку. Командлет **Get-Help** предоставляет доступ к этой системе. 



- Справочные данные о командлетах в модуле Azure PowerShell также доступны в библиотеке Azure. Дополнительную информацию см. в [справочнике по командлетам Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

На этих популярных форумах можно получить помощь от сообщества:

- [Форум Azure на MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1203_2015-->