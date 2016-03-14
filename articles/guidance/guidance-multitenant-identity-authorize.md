<properties
   pageTitle="Авторизация в мультитенантных приложениях | Microsoft Azure"
   description="Выполнение авторизации в мультитенантном приложении"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Авторизация в мультитенантных приложениях

Этот материал входит в [цикл статей]. К статьям прилагается полный [пример приложения].

В этой статье мы рассмотрим два общих подхода к авторизации.

-	**Авторизация на основе ролей**. Авторизация действия на основе ролей, назначенных пользователю. Например, для выполнения некоторых действий требуется роль администратора.
-	**Авторизация на основе ресурсов**. Авторизация действия на основе конкретного ресурса. Например, каждый ресурс имеет владельца. Владелец может удалить ресурс, а другие пользователи — нет.

В стандартном приложении используется сочетание обоих вариантов. Например, чтобы удалить ресурс, пользователь должен быть владельцем ресурса _или_ администратором.

## Авторизация на основе ролей

Приложение [Tailspin Surveys][Tailspin] определяет указанные далее роли:

- Администратор. Может выполнять все операции CRUD в любом опросе, принадлежащем этому клиенту.
- Создатель. Может создавать опросы.
- Читатель. Может читать все опросы, принадлежащие этому клиенту.

Роли применяются к _пользователям_ приложения. В приложении Surveys пользователь является или администратором, или создателем, или читателем.

Описание способов определения ролей и управление ими см. в статье [Роли приложения].

Код авторизации не зависит от способа управления ролями, поэтому он не будет иметь существенных отличий. В ASP.NET Core 1.0 представлена абстракция, которая называется _политиками авторизации_. С помощью этой функции пользователь определяет политики авторизации в коде, а затем применяет их к действиям контроллера. Политика не связана с контроллером.

### Создайте политики.

Чтобы определить политику, сначала следует создать класс, реализующий `IAuthorizationRequirement`. Проще всего извлечь его из `AuthorizationHandler`. В методе `Handle` проверьте соответствующие утверждения.

Ниже приведен пример из приложения Tailspin Surveys.

```csharp
public class SurveyCreatorRequirement : AuthorizationHandler<SurveyCreatorRequirement>, IAuthorizationRequirement
{
    protected override void Handle(AuthorizationContext context, SurveyCreatorRequirement requirement)
    {
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin) ||
            context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            context.Succeed(requirement);
        }
    }
}
```

> [AZURE.NOTE] См. [SurveyCreatorRequirement.cs]

Этот класс определяет требование для пользователя по созданию опроса. Пользователю должна быть назначена роль SurveyAdmin или SurveyCreator.

В классе запуска определите именованную политику, содержащую одно или несколько требований. Если существует несколько требований, пользователь должен выполнить _каждое_ из них, чтобы пройти авторизацию. Следующий код определяет две политики:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy(PolicyNames.RequireSurveyCreator,
        policy =>
        {
            policy.AddRequirements(new SurveyCreatorRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });

    options.AddPolicy(PolicyNames.RequireSurveyAdmin,
        policy =>
        {
            policy.AddRequirements(new SurveyAdminRequirement());
            policy.AddAuthenticationSchemes(CookieAuthenticationDefaults.AuthenticationScheme);
        });
});
```

> [AZURE.NOTE] См. [Startup.cs]

Этот код также задает схему проверки подлинности, согласно которой ASP.NET определяет, какое ПО промежуточного слоя для проверки подлинности должно выполняться при сбое авторизации. В этом случае мы указываем ПО промежуточного слоя для проверки подлинности файла cookie, так как это ПО может перенаправлять пользователей на страницу с сообщением "Запрещено". Расположение страницы с сообщением "Запрещено" задается в параметре AccessDeniedPath для ПО промежуточного слоя файла cookie. См. статью [Настройка ПО промежуточного слоя для проверки подлинности].

### Авторизация действий контроллера

И, наконец, чтобы авторизовать действие в контроллере MVC, можно задать политику в атрибуте `Authorize`, как показано ниже.

```csharp
[Authorize(Policy = "SurveyCreatorRequirement")]
public IActionResult Create()
{
    // ...
}
```

В более ранних версиях ASP.NET необходимо задать свойство **Roles** для атрибута, как показано ниже.

```csharp
// old way
[Authorize(Roles = "SurveyCreator")]

```

Эта возможность все еще поддерживается в ASP.NET Core 1.0, но она имеет определенные недостатки по сравнению с политиками авторизации:

-	Она предполагает использование определенного типа утверждения. Политики могут проверять наличие любого типа утверждения. Роли являются лишь типом утверждения.
-	Имя роли жестко задается в атрибуте. Благодаря политикам логика авторизации реализуется в одном месте, что упрощает обновление или даже скачивание из параметров конфигурации.
-	Политики позволяют принимать более сложные решения по авторизации (например, возраст 21 год и старше), которые не могут быть выражены простым членством в роли.

## Авторизация на основе ресурсов

_Авторизация на основе ресурсов_ выполняется каждый раз, когда авторизация зависит от конкретного ресурса, затрагиваемого операцией. В приложении Tailspin Surveys для каждого опроса существует владелец и ни одного или несколько участников.

-	Владелец может читать, обновлять, удалять, публиковать опрос и отменять его публикацию.
-	Владелец может назначать участников опроса.
-	Участники могут читать и обновлять опрос.

Обратите внимание, что "владелец" и "участник" не являются ролями приложения. Они сохраняются для каждого приложения в базе данных приложений. Чтобы проверить, может ли пользователь, к примеру, удалить опрос, приложение проверяет, является ли пользователь владельцем этого опроса.

В ASP.NET Core 1.0 авторизация на основе ресурсов реализуется путем наследования от **AuthorizationHandler** и переопределения метода **Handle**.

```csharp
public class SurveyAuthorizationHandler : AuthorizationHandler<OperationAuthorizationRequirement, Survey>
{
     protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
    {
    }
}
```

Обратите внимание, что этот класс является строго типизированным для объектов опроса. Зарегистрируйте класс для DI при запуске, как показано ниже.

```csharp
services.AddSingleton<IAuthorizationHandler>(factory =>
{
    return new SurveyAuthorizationHandler();
});
```

Для проверок авторизации используйте интерфейс **IAuthorizationService**, который можно внедрить в контроллеры. Следующий код проверяет, может ли пользователь читать опрос:

```csharp
if (await _authorizationService.AuthorizeAsync(User, survey, Operations.Read) == false)
{
    return new HttpStatusCodeResult(403);
}
```

Поскольку мы передаем объект `Survey`, этот вызов вызовет `SurveyAuthorizationHandler`.

В коде авторизации целесообразно объединить все разрешения пользователя на основе ролей и ресурсов, а затем проверить обобщенный набор на возможность выполнения нужной операции. Ниже приведен пример из приложения Surveys. Приложение определяет несколько типов разрешений:

- Администратор
- Участник
- создатель;
- Владелец
- читатель.

Приложение также определяет набор возможных операций с опросами:

- Создание
- чтение
- Блокировка изменений
- Удалить
- Опубликовать
- Отмена публикации

Приведенный ниже код создает список разрешений для конкретного пользователя и опроса. Обратите внимание, что этот код просматривает роли приложения пользователя и поля владельца или участника в опросе.

```csharp
protected override void Handle(AuthorizationContext context, OperationAuthorizationRequirement operation, Survey resource)
{
    var permissions = new List<UserPermissionType>();
    string userTenantId = context.User.GetTenantIdValue();
    int userId = ClaimsPrincipalExtensions.GetUserKey(context.User);
    string user = context.User.GetUserName();

    if (resource.TenantId == userTenantId)
    {
        // Admin can do anything, as long as the resource belongs to the admin's tenant.
        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyAdmin))
        {
            context.Succeed(operation);
            return;
        }

        if (context.User.HasClaim(ClaimTypes.Role, Roles.SurveyCreator))
        {
            permissions.Add(UserPermissionType.Creator);
        }
        else
        {
            permissions.Add(UserPermissionType.Reader);
        }

        if (resource.OwnerId == userId)
        {
            permissions.Add(UserPermissionType.Owner);
        }
    }
    if (resource.Contributors != null && resource.Contributors.Any(x => x.UserId == userId))
    {
        permissions.Add(UserPermissionType.Contributor);
    }
    if (ValidateUserPermissions[operation](permissions))
    {
        context.Succeed(operation);
    }
}
```

> [AZURE.NOTE] См. [SurveyAuthorizationHandler.cs].

Необходимо убедиться, что в мультитенантном приложении разрешения не попадают в данные другого клиента. В приложении Surveys разрешение участника предоставляется в рамках всех клиентов — назначить в качестве участника можно пользователя из другого клиента. Другие типы разрешений ограничены ресурсами, принадлежащими клиенту пользователя, поэтому перед предоставлением этих типов разрешений код проверяет идентификатор клиента. (Поле `TenantId`, назначенное при создании опроса.)

Следующим шагом является проверка возможности выполнения операции (чтение, обновление, удаление и т. д.) согласно разрешениям. В приложении Surveys для выполнения этого шага используется таблица подстановки функций:

```csharp
static readonly Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>> ValidateUserPermissions
    = new Dictionary<OperationAuthorizationRequirement, Func<List<UserPermissionType>, bool>>

    {
        { Operations.Create, x => x.Contains(UserPermissionType.Creator) },

        { Operations.Read, x => x.Contains(UserPermissionType.Creator) ||
                                x.Contains(UserPermissionType.Reader) ||
                                x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Update, x => x.Contains(UserPermissionType.Contributor) ||
                                x.Contains(UserPermissionType.Owner) },

        { Operations.Delete, x => x.Contains(UserPermissionType.Owner) },

        { Operations.Publish, x => x.Contains(UserPermissionType.Owner) },

        { Operations.UnPublish, x => x.Contains(UserPermissionType.Owner) }
    };
```

## Дополнительные ресурсы

- [Авторизация на основе ресурсов](https://docs.asp.net/en/latest/security/authorization/resourcebased.html)
- [Настраиваемая авторизация на основе политик](https://docs.asp.net/en/latest/security/authorization/policies.html)

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[цикл статей]: guidance-multitenant-identity.md
[Роли приложения]: guidance-multitenant-identity-app-roles.md
[SurveyCreatorRequirement.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyCreatorRequirement.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[Настройка ПО промежуточного слоя для проверки подлинности]: guidance-multitenant-identity-authenticate.md#configuring-the-authentication-middleware
[SurveyAuthorizationHandler.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Security/Policy/SurveyAuthorizationHandler.cs
[пример приложения]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->