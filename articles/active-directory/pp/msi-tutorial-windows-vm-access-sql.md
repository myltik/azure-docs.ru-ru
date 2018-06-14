---
title: Доступ к SQL Azure с помощью управляемого удостоверения службы виртуальной машины Windows
description: В рамках этого руководства вы узнаете, как получить доступ к службе SQL Azure с помощью управляемого удостоверения службы (MSI) виртуальной машины Windows.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 682998bb979c9b155b7b1389d8f605018ae135b6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31589532"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Доступ к SQL Azure с помощью управляемого удостоверения службы виртуальной машины Windows

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве описывается доступ к серверу SQL Azure с помощью управляемого удостоверения службы (MSI) виртуальной машины Windows. Управляемыми удостоверениями службы автоматически управляет Azure. Они позволяют проходить проверку подлинности в службах, поддерживающих аутентификацию Azure AD, без указания учетных данных в коде. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Активация MSI на виртуальной машине Windows. 
> * Предоставление виртуальной машине доступа к серверу SQL Azure.
> * Получение маркера доступа с использованием идентификатора виртуальной машины и отправка запроса на сервер SQL Azure с его помощью.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Создание виртуальной машины Windows в новой группе ресурсов

В рамках этого руководства мы создадим виртуальную машину Windows.  Вы также можете активировать MSI на имеющейся виртуальной машине.

1.  Щелкните **Создать ресурс** в верхнем левом углу окна портала Azure.
2.  Выберите **Вычисления**, а затем — **Windows Server 2016 Datacenter**. 
3.  Введите сведения о виртуальной машине. **Имя пользователя** и **пароль**, созданные здесь, используются для входа на виртуальную машину.
4.  В раскрывающемся списке выберите нужную **подписку** для виртуальной машины.
5.  Чтобы выбрать новую **группу ресурсов**, в которой вы хотите создать виртуальную машину, щелкните **Создать**. По завершении нажмите кнопку **ОК**.
6.  Выберите размер виртуальной машины. Чтобы просмотреть дополнительные размеры, выберите **Просмотреть все** или измените фильтр **Supported disk type** (Поддерживаемые типы диска). На странице параметров оставьте значения по умолчанию и нажмите кнопку **OK**.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Активация MSI на виртуальной машине 

MSI на виртуальной машине позволяет получить маркеры доступа из Azure AD без необходимости указывать в коде учетные данные. Активация MSI указывает Azure создать управляемое удостоверение для виртуальной машины. На самом деле активация MSI необходима для установки расширения виртуальной машины MSI и непосредственно активации MSI в Azure Resource Manager.

1.  Выберите **виртуальную машину**, на которой нужно активировать MSI.  
2.  В левой области навигации щелкните **Конфигурация**. 
3.  Появится страница **Managed Service Identity** (Управляемое удостоверение службы). Чтобы зарегистрировать и активировать MSI, нажмите кнопку **Да**. Чтобы удалить удостоверение, нажмите кнопку "Нет". 
4.  Нажмите кнопку **Сохранить**, чтобы сохранить конфигурацию.  
    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Если вы хотите проверить расширения на этой виртуальной машине, щелкните **Расширения**. Если удостоверение MSI активировано, вы увидите в списке **ManagedIdentityExtensionforWindows**.

    ![Замещающий текст](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Предоставление виртуальной машине доступа к базе данных на сервере SQL Azure

Теперь вы можете предоставлять виртуальной машине доступ к базе данных на сервере SQL Azure.  Для выполнения этого шага можно использовать имеющийся сервер SQL или создать новый.  Чтобы создать сервер и базу данных с помощью портала Azure, следуйте указаниям в статье [Создание базы данных SQL Azure на портале Azure](~/articles/sql-database/sql-database-get-started-portal.md). В [документации по SQL Azure](~/articles/sql-database/index.yml) также есть краткие руководства, описывающие использование Azure CLI и Azure PowerShell.

Для предоставления виртуальной машине доступа к базе данных нужно выполнить три шага:
1.  Создать группу в Azure AD и сделать MSI виртуальной машины ее участником.
2.  Включить аутентификацию Azure AD для сервера SQL.
3.  Создать в базе данных **автономного пользователя**, представляющего группу Azure AD.

> [!NOTE]
> Обычно создается автономный пользователь, который сопоставляется напрямую с MSI виртуальной машины.  Сейчас SQL Azure не поддерживает субъект-службу Azure AD, представляющий MSI виртуальной машины, которое нужно сопоставить с автономным пользователем.  В качестве поддерживаемого обходного решения сделайте MSI виртуальной машины участником группы Azure AD, а затем создайте в базе данных автономного пользователя, представляющего группу.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Создание группы в Azure AD и назначение MSI виртуальной машины участником группы

Можно использовать существующую группу Azure AD или создать новую с помощью Azure AD PowerShell.  

Сначала установите модуль [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Затем войдите с помощью `Connect-AzureAD` и выполните следующую команду, чтобы создать группу и сохранить ее в переменной:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

Выходные данные выглядят следующим образом. В них содержатся подробные сведения о значении переменной.

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

Затем добавьте в группу MSI виртуальной машины.  Требуется значение **ObjectId** управляемого удостоверения службы, которое можно получить с помощью Azure PowerShell.  Сначала скачайте [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Затем войдите с помощью `Connect-AzureRmAccount` и выполните следующие команды, чтобы:
- Убедиться, что для контекста сеанса задана нужная подписка Azure (если имеется несколько).
- Перечислить доступные ресурсы в подписке Azure, чтобы удостовериться в наличии группы ресурсов и виртуальной машины с правильными именами.
- Получить свойства управляемого удостоверения службы виртуальной машины, используя соответствующие значения для `<RESOURCE-GROUP>` и `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

Выходные данные выглядят, как показано ниже. В них содержатся подробные сведения об идентификаторе объекта субъекта-службы MSI виртуальной машины:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Теперь добавьте MSI виртуальной машины в группу.  Добавить субъект-службу в группу можно только с помощью Azure AD PowerShell.  Выполните следующую команду:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Если вы захотите проверить сведения об участии в группе, выходные данные будут выглядеть следующим образом:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Включение аутентификации Azure AD для сервера SQL

Теперь, когда вы создали группу и добавили MSI виртуальной машины как участника группы, можно [настроить аутентификацию Azure AD для сервера SQL](~/articles/sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) следующим образом:

1.  На портале Azure выберите **SQL servers** (Серверы SQL Server) на левой панели навигации.
2.  Щелкните сервер SQL, для которого нужно включить аутентификацию Azure AD.
3.  В разделе колонки **Параметры** щелкните **Администратор Active Directory**.
4.  В командной строке щелкните **Задать администратора**.
5.  Выберите учетную запись пользователя Azure AD, которую необходимо сделать администратором сервера, а затем нажмите кнопку **Выбрать**.
6.  На панели команд нажмите кнопку **Сохранить**.

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Создание в базе данных автономного пользователя, представляющего группу Azure AD

В следующем шаге потребуется [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Прежде чем начать, советуем ознакомиться со следующими статьями, содержащими общие сведения об интеграции Azure AD:

- [Универсальная проверка подлинности для Базы данных SQL и хранилища данных SQL (поддержка SSMS для MFA)](~/articles/sql-database/sql-database-ssms-mfa-authentication.md)
- [Настройка аутентификации Azure Active Directory и управление ею с использованием базы данных SQL или хранилища данных SQL](~/articles/sql-database/sql-database-aad-authentication-configure.md)

1.  Запустите среду SQL Server Management Studio.
2.  В диалоговом окне **Подключение к серверу** в поле **Имя сервера** введите имя сервера SQL.
3.  В поле **Authentication** (Аутентификация) выберите **Active Directory — универсальная с поддержкой MFA**.
4.  В поле **Имя пользователя** введите имя учетной записи Azure AD, установленной в качестве администратора сервера, например helen@woodgroveonline.com.
5.  Щелкните по элементу **Параметры**.
6.  В поле **Подключение к базе данных** введите имя несистемной базы данных, которую требуется настроить.
7.  Щелкните **Подключить**.  Завершите процесс входа в систему.
8.  В **обозревателе объектов** разверните папку **Базы данных**.
9.  Щелкните правой кнопкой мыши пользовательскую базу данных и выберите **Создать запрос**.
10.  В окне запроса введите следующую строку и на панели инструментов нажмите кнопку **Выполнить**.
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     В результате выполнения команды должен быть создан автономный пользователь для группы.
11.  Очистите окно запроса, введите следующую строку и на панели инструментов нажмите кнопку **Выполнить**.
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     В результате выполнения команды автономному пользователю должна быть предоставлена возможность считывания всей базы данных.

Теперь код, запущенный на виртуальной машине, может получить маркер из MSI и использовать его для проверки подлинности на сервере SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Получение маркера доступа с использованием удостоверения виртуальной машины и вызов SQL Azure с его помощью 

SQL Azure изначально поддерживает проверку подлинности Azure AD, поэтому может напрямую принимать маркеры доступа, полученные с использованием MSI.  Для создания подключения к SQL используется метод на основе **маркера доступа**.  Он реализуется как часть интеграции SQL Azure с Azure AD и отличается от указания учетных данных в строке подключения.

Ниже приведен пример кода .NET для установки подключения к SQL с помощью маркера доступа.  Этот код должен выполняться на виртуальной машине, чтобы иметь доступ к конечной точке MSI виртуальной машины.  Для использования метода с маркером доступа необходима платформа **.NET Framework 4.6** или более поздней версии.  Замените значения AZURE-SQL-SERVERNAME и DATABASE соответствующим образом.  Обратите внимание, что идентификатор ресурса для SQL Azure — https://database.windows.net/.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Кроме того, вы можете быстро протестировать сквозную настройку с помощью PowerShell без необходимости писать и развертывать приложение на виртуальной машине.

1.  На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**. 
2.  Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3.  Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте **PowerShell** в удаленном сеансе. 
4.  С помощью команды PowerShell `Invoke-WebRequest` сделайте запрос к локальной конечной точке MSI, чтобы получить маркер доступа к SQL Azure.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Преобразуйте ответ из объекта JSON в объект PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Извлеките маркер доступа из ответа.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Откройте подключение к серверу SQL. Не забудьте заменить значения AZURE-SQL-SERVERNAME и DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Затем создайте и отправьте запрос на сервер.  Не забудьте заменить значение для TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Проверьте значение `$DataSet.Tables[0]`, чтобы просмотреть результаты запроса.  Поздравляем, вы выполнили запрос базы данных с помощью MSI виртуальной машины, не предоставляя учетные данные!

## <a name="related-content"></a>Связанная информация

- Общие сведения об MSI см. в разделе [Управляемое удостоверение службы (MSI) для Azure Active Directory](msi-overview.md).
- Дополнительные сведения об аутентификации Azure AD см. в статье [Использование аутентификации Azure Active Directory для аутентификации с помощью базы данных SQL или хранилища данных SQL](~/articles/sql-database/sql-database-aad-authentication.md).
- Дополнительные сведения о настройке SQL Azure для поддержки проверки подлинности Azure AD см. в статье [Использование аутентификации Azure Active Directory для аутентификации с помощью базы данных SQL или хранилища данных SQL](~/articles/sql-database/sql-database-aad-authentication-configure.md).
- Дополнительные сведения о проверке подлинности и доступе к серверу SQL см. в статье [Приступая к работе с разрешениями Database Engine](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.
