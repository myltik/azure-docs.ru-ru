<properties
	pageTitle="Построение службы, использующей существующую базу данных SQL в серверной части .NET мобильных служб | Microsoft Azure"
	description="Узнайте, как использовать существующую облачную или локальную базу данных SQL с мобильной службой на основе .NET"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>


# Сборка службы с использованием существующей базы данных SQL на внутреннем сервере мобильных служб .NET

Серверная часть мобильных служб .NET упрощает использования существующих ресурсов при создании мобильной службы. Один особенно интересный сценарий — использование существующей БД SQL (в локальной среде или в облаке), которая может использоваться другими приложениями, чтобы сделать существующие данные доступными для мобильных клиентов. В этом случае требуется, чтобы модель базы данных (или *схема*) не изменялась, иначе существующие решения перестанут работать.

<a name="ExistingModel"></a>
## Изучение существующей модели базы данных

Для этого учебного руководства мы используем базу данных, созданную с помощью вашей мобильной службы, но мы не будем использовать модель по умолчанию. Мы вручную создадим произвольную модель, которая будет представлять существующее приложение. Полные сведения о подключении к локальной базе данных [Подключение к локальному SQL Server из мобильной службы Azure с помощью гибридных подключений](mobile-services-dotnet-backend-hybrid-connections-get-started.md).

1. Сначала создайте серверный проект мобильных служб в **Visual Studio 2013 Update 2** или с помощью проекта быстрого запуска, который можно загрузить на вкладке «Мобильные службы» вашей службы в [портале управления Azure](http://manage.windowsazure.com). В целях данного руководства исходим из того, что ваш серверный проект называется **ShoppingService**.

2. Создайте файл **Customer.cs** в папке **Models** и используйте следующую реализацию. Нужно добавить ссылку сборки **System.ComponentModel.DataAnnotations** в ваш проект.

        using System.Collections.Generic;
        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Customer
            {
                [Key]
                public int CustomerId { get; set; }

                public string Name { get; set; }

                public virtual ICollection<Order> Orders { get; set; }

            }
        }

3. Создайте файл **Order.cs** внутри папки **Models** и используйте следующую реализацию:

        using System.ComponentModel.DataAnnotations;

        namespace ShoppingService.Models
        {
            public class Order
            {
                [Key]
                public int OrderId { get; set; }

                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                public int CustomerId { get; set; }

                public virtual Customer Customer { get; set; }

            }
        }

    Обратите внимание, что у этих двух классов есть *отношение*: каждый экземпляр **Order** связан с одним экземпляром **Customer**, а один экземпляр **Customer** может быть связан с несколькими экземплярами **Orders**. Такие отношения широко распространены в существующих моделях данных.

4. Создайте файл **ExistingContext.cs** внутри папки **Models** и используйте следующую реализацию:

        using System.Data.Entity;

        namespace ShoppingService.Models
        {
            public class ExistingContext : DbContext
            {
                public ExistingContext()
                    : base("Name=MS_TableConnectionString")
                {
                }

                public DbSet<Customer> Customers { get; set; }

                public DbSet<Order> Orders { get; set; }

            }
        }

Показанная выше структура напоминает существующую модель Entity Framework, которую вы можете уже использовать в существующих приложениях. Обратите внимание, что на этом этапе модель не учитывает использование мобильных служб.

<a name="DTOs"></a>
## Создание объектов передачи данных (DTO) для вашей мобильной службы

Модель данных вашей мобильной службы может быть сколь угодно сложной, она может содержать сотни объектов с разными отношениями между ними. При создании мобильного приложения обычно рекомендуется упростить модель данных и исключить отношения (или обрабатывать их вручную), чтобы снизить объем данных, передаваемых между приложением и службой. В этом разделе мы создадим набор упрощенных объектов (так называемые «объекты передачи данных» или DTO), которые связаны с данными в базе данных, но содержат лишь минимальный набор свойств, необходимых для мобильного приложения.

1. Создайте файл **MobileCustomer.cs** в папке **DataObjects** вашего проекта службы и используйте следующую реализацию:

        using Microsoft.WindowsAzure.Mobile.Service;

        namespace ShoppingService.DataObjects
        {
            public class MobileCustomer : EntityData
            {
                public string Name { get; set; }
            }
        }

    Обратите внимание, что этот класс похож на класс **Customer** в модели, за исключением того, что удалено свойство отношения с **Order**. Чтобы объект правильно работал с автономной синхронизацией мобильных служб, ему требуется набор *системных свойств* для оптимистической блокировки. DTO наследует от [**EntityData**](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx), содержащего эти свойства. Целочисленное свойство **CustomerId** исходной модели заменено строковым свойством **Id** из **EntityData**. Именно это свойство **Id** будет использоваться мобильными службами.

2. Создайте файл **MobileOrder.cs** в папке **DataObjects** вашего проекта службы.

        using Microsoft.WindowsAzure.Mobile.Service;
        using Newtonsoft.Json;
        using System.ComponentModel.DataAnnotations;
        using System.ComponentModel.DataAnnotations.Schema;

        namespace ShoppingService.DataObjects
        {
            public class MobileOrder : EntityData
            {
                public string Item { get; set; }

                public int Quantity { get; set; }

                public bool Completed { get; set; }

                [NotMapped]
                public int CustomerId { get; set; }

                [Required]
                public string MobileCustomerId { get; set; }

                public string MobileCustomerName { get; set; }
            }
        }

    Свойство отношения **Customer** было заменено именем **Customer** и свойством **MobileCustomerId**, которое может использоваться для ручного модулирования отношения на стороне клиента. Пока можно не обращать внимания на свойство **CustomerId**, оно будет использовано позже.

3. Возможно, вы заметили, что в дополнение к системным свойствам базового класса **EntityData** у наших DTO теперь больше свойств, чем у типов модели. Нужно место для хранения этих свойств, поэтому добавим несколько столбцов в исходную базу данных. При этом изменяется база данных, но не нарушится работа существующих приложений, поскольку изменения только добавочные (в схему добавляются новые столбцы). Для этого добавьте следующие инструкции в начало файлов **Customer.cs** и **Order.cs**:

        using System.ComponentModel.DataAnnotations.Schema;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.ComponentModel.DataAnnotations;
        using System;

4. Затем добавьте следующие дополнительные свойства для каждого из этих классов:

        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        [Index]
        [TableColumn(TableColumnType.CreatedAt)]
        public DateTimeOffset? CreatedAt { get; set; }

        [TableColumn(TableColumnType.Deleted)]
        public bool Deleted { get; set; }

        [Index]
        [TableColumn(TableColumnType.Id)]
        [MaxLength(36)]
        public string Id { get; set; }

        [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
        [TableColumn(TableColumnType.UpdatedAt)]
        public DateTimeOffset? UpdatedAt { get; set; }

        [TableColumn(TableColumnType.Version)]
        [Timestamp]
        public byte[] Version { get; set; }

4. Системные свойства только что добавили встроенное поведение (например, автоматическое обновление времени создания и обновления), прозрачное для работы базы данных. Чтобы включить это поведение, нужно внести изменения в **ExistingContext.cs**. В начале файла добавьте следующее:

        using System.Data.Entity.ModelConfiguration.Conventions;
        using Microsoft.WindowsAzure.Mobile.Service.Tables;
        using System.Linq;

5. В тексте **ExistingContext** переопределите [**OnModelCreating**](http://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx):

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceTableColumn", (property, attributes) => attributes.Single().ColumnType.ToString()));

            base.OnModelCreating(modelBuilder);
        }

5. Заполним базу данных образцовыми данными. Откройте файл **WebApiConfig.cs**. Создайте новый [**IDatabaseInitializer**](http://msdn.microsoft.com/library/gg696323.aspx) и настройте его в методе **Register**, как показано ниже.

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.Models;
        using System;
        using System.Collections.Generic;
        using System.Collections.ObjectModel;
        using System.Data.Entity;
        using System.Web.Http;

        namespace ShoppingService
        {
            public static class WebApiConfig
            {
                public static void Register()
                {
                    ConfigOptions options = new ConfigOptions();

                    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

                    Database.SetInitializer(new ExistingInitializer());
                }
            }

            public class ExistingInitializer : ClearDatabaseSchemaIfModelChanges<ExistingContext>
            {
                protected override void Seed(ExistingContext context)
                {
                    List<Order> orders = new List<Order>
                    {
                        new Order { OrderId = 10, Item = "Guitars", Quantity = 2, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 20, Item = "Drums", Quantity = 10, Id = Guid.NewGuid().ToString()},
                        new Order { OrderId = 30, Item = "Tambourines", Quantity = 20, Id = Guid.NewGuid().ToString() }
                    };

                    List<Customer> customers = new List<Customer>
                    {
                        new Customer { CustomerId = 1, Name = "John", Orders = new Collection<Order> {
                            orders[0]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 2, Name = "Paul", Orders = new Collection<Order> {
                            orders[1]}, Id = Guid.NewGuid().ToString()},
                        new Customer { CustomerId = 3, Name = "Ringo", Orders = new Collection<Order> {
                            orders[2]}, Id = Guid.NewGuid().ToString()},
                    };

                    foreach (Customer c in customers)
                    {
                        context.Customers.Add(c);
                    }

                    base.Seed(context);
                }
            }
        }

<a name="Mapping"></a>
## Установка сопоставления между DTO и моделью

Теперь у нас есть типы моделей **Customer** и **Order** и DTO **MobileCustomer** и **MobileOrder**, но нужно наладить автоматическое преобразование между ними. Здесь мобильные службы опираются на реляционный модуль сопоставления [**AutoMapper**](http://automapper.org/), на который уже есть ссылка в проекте.

1. Добавьте следующий код в начало файла **WebApiConfig.cs**:

        using AutoMapper;
        using ShoppingService.DataObjects;

2. Чтобы определить сопоставление, добавьте следующий код в метод **Register** класса **WebApiConfig**.

        Mapper.Initialize(cfg =>
        {
            cfg.CreateMap<MobileOrder, Order>();
            cfg.CreateMap<MobileCustomer, Customer>();
            cfg.CreateMap<Order, MobileOrder>()
                .ForMember(dst => dst.MobileCustomerId, map => map.MapFrom(x => x.Customer.Id))
                .ForMember(dst => dst.MobileCustomerName, map => map.MapFrom(x => x.Customer.Name));
            cfg.CreateMap<Customer, MobileCustomer>();

        });

Теперь AutoMapper будет сопоставлять объекты друг с другом. Будут сопоставлены все свойства с соответствующими именами, например, **MobileOrder.CustomerId** будет автоматически сопоставлено с **Order.CustomerId**. Пользовательские сопоставления можно настроить, как показано выше, где мы сопоставляем свойство **MobileCustomerName** со свойством **Name** свойства отношения **Customer**.

<a name="DomainManager"></a>
## Реализация логики для домена

Теперь следует реализовать [**MappedEntityDomainManager**](http://msdn.microsoft.com/library/dn643300.aspx), который служит уровнем абстракции между нашим хранилищем данных и контроллером, который будет обслуживать HTTP-трафик наших клиентов. Мы сможем написать контроллер в следующем разделе. Мы добавляем DTO и **MappedEntityDomainManager** для обработки обмена данными с исходным хранилищем данных. Мы также получаем место для реализации необходимой логики.

1. Добавьте файл **MobileCustomerDomainManager.cs** в папку **Models** вашего проекта. Вставьте следующую реализацию:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Data.Entity;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileCustomerDomainManager : MappedEntityDomainManager<MobileCustomer, Customer>
            {
                private ExistingContext context;

                public MobileCustomerDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                public static int GetKey(string mobileCustomerId, DbSet<Customer> customers, HttpRequestMessage request)
                {
                    int customerId = customers
                       .Where(c => c.Id == mobileCustomerId)
                       .Select(c => c.CustomerId)
                       .FirstOrDefault();

                    if (customerId == 0)
                    {
                        throw new HttpResponseException(request.CreateNotFoundResponse());
                    }
                    return customerId;
                }

                protected override T GetKey<T>(string mobileCustomerId)
                {
                    return (T)(object)GetKey(mobileCustomerId, this.context.Customers, this.Request);
                }

                public override SingleResult<MobileCustomer> Lookup(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return LookupEntity(c => c.CustomerId == customerId);
                }

                public override async Task<MobileCustomer> InsertAsync(MobileCustomer mobileCustomer)
                {
                    return await base.InsertAsync(mobileCustomer);
                }

                public override async Task<MobileCustomer> UpdateAsync(string mobileCustomerId, Delta<MobileCustomer> patch)
                {
                    int customerId = GetKey<int>(mobileCustomerId);

                    Customer existingCustomer = await this.Context.Set<Customer>().FindAsync(customerId);
                    if (existingCustomer == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileCustomer existingCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);
                    patch.Patch(existingCustomerDTO);
                    Mapper.Map<MobileCustomer, Customer>(existingCustomerDTO, existingCustomer);

                    await this.SubmitChangesAsync();

                    MobileCustomer updatedCustomerDTO = Mapper.Map<Customer, MobileCustomer>(existingCustomer);

                    return updatedCustomerDTO;
                }

                public override async Task<MobileCustomer> ReplaceAsync(string mobileCustomerId, MobileCustomer mobileCustomer)
                {
                    return await base.ReplaceAsync(mobileCustomerId, mobileCustomer);
                }

                public override async Task<bool> DeleteAsync(string mobileCustomerId)
                {
                    int customerId = GetKey<int>(mobileCustomerId);
                    return await DeleteItemAsync(customerId);
                }
            }
        }

    Важная часть этого класса — метод **GetKey**, где мы указываем, как обнаружить свойство идентификатора объекта в исходной модели данных.

2. Добавьте файл **MobileOrderDomainManager.cs** в папку **Models** вашего проекта:

        using AutoMapper;
        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using System.Linq;
        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.OData;

        namespace ShoppingService.Models
        {
            public class MobileOrderDomainManager : MappedEntityDomainManager<MobileOrder, Order>
            {
                private ExistingContext context;

                public MobileOrderDomainManager(ExistingContext context, HttpRequestMessage request, ApiServices services)
                    : base(context, request, services)
                {
                    Request = request;
                    this.context = context;
                }

                protected override T GetKey<T>(string mobileOrderId)
                {
                    int orderId = this.context.Orders
                        .Where(o => o.Id == mobileOrderId)
                        .Select(o => o.OrderId)
                        .FirstOrDefault();

                    if (orderId == 0)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }
                    return (T)(object)orderId;
                }

                public override SingleResult<MobileOrder> Lookup(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return LookupEntity(o => o.OrderId == orderId);
                }

                private async Task<Customer> VerifyMobileCustomer(string mobileCustomerId, string mobileCustomerName)
                {
                    int customerId = MobileCustomerDomainManager.GetKey(mobileCustomerId, this.context.Customers, this.Request);
                    Customer customer = await this.context.Customers.FindAsync(customerId);
                    if (customer == null)
                    {
                        throw new HttpResponseException(Request.CreateBadRequestResponse("Customer with name '{0}' was not found", mobileCustomerName));
                    }
                    return customer;
                }

                public override async Task<MobileOrder> InsertAsync(MobileOrder mobileOrder)
                {
                    Customer customer = await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);
                    mobileOrder.CustomerId = customer.CustomerId;
                    return await base.InsertAsync(mobileOrder);
                }

                public override async Task<MobileOrder> UpdateAsync(string mobileOrderId, Delta<MobileOrder> patch)
                {
                    Customer customer = await VerifyMobileCustomer(patch.GetEntity().MobileCustomerId, patch.GetEntity().MobileCustomerName);

                    int orderId = GetKey<int>(mobileOrderId);

                    Order existingOrder = await this.Context.Set<Order>().FindAsync(orderId);
                    if (existingOrder == null)
                    {
                        throw new HttpResponseException(this.Request.CreateNotFoundResponse());
                    }

                    MobileOrder existingOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);
                    patch.Patch(existingOrderDTO);
                    Mapper.Map<MobileOrder, Order>(existingOrderDTO, existingOrder);

                    // This is required to map the right Id for the customer
                    existingOrder.CustomerId = customer.CustomerId;

                    await this.SubmitChangesAsync();

                    MobileOrder updatedOrderDTO = Mapper.Map<Order, MobileOrder>(existingOrder);

                    return updatedOrderDTO;
                }

                public override async Task<MobileOrder> ReplaceAsync(string mobileOrderId, MobileOrder mobileOrder)
                {
                    await VerifyMobileCustomer(mobileOrder.MobileCustomerId, mobileOrder.MobileCustomerName);

                    return await base.ReplaceAsync(mobileOrderId, mobileOrder);
                }

                public override Task<bool> DeleteAsync(string mobileOrderId)
                {
                    int orderId = GetKey<int>(mobileOrderId);
                    return DeleteItemAsync(orderId);
                }
            }
        }

    В этом случае нас интересуют методы **InsertAsync** и **UpdateAsync**; именно здесь мы применяем отношение каждого экземпляра **Order** с действующим экземпляром **Customer**. В **InsertAsync** мы заполним свойство **MobileOrder.CustomerId**, которое сопоставляется со свойством **Order.CustomerId**. Мы получаем это значение, находя **Customer** с соответствующим **MobileOrder.MobileCustomerId**. Причина состоит в том, что по умолчанию клиент располагает только идентификатором мобильных служб (**MobileOrder.MobileCustomerId**) экземпляра **Customer**, который отличается от фактического основного ключа, необходимого для задания внешнего ключа (**MobileOrder.CustomerId**) из **Order** в **Customer**. Такой подход используется только внутри службы для поддержки операции вставки.

Теперь можно создавать контроллеры, чтобы предоставить доступ к DTO нашим клиентам.

<a name="Controller"></a>
## Реализация TableController с помощью

1. В папке **Controllers** добавьте файл **MobileCustomerController.cs**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileCustomerController : TableController<MobileCustomer>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    var context = new ExistingContext();
                    DomainManager = new MobileCustomerDomainManager(context, Request, Services);
                }

                public IQueryable<MobileCustomer> GetAllMobileCustomers()
                {
                    return Query();
                }

                public SingleResult<MobileCustomer> GetMobileCustomer(string id)
                {
                    return Lookup(id);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PatchAsync(string id, Delta<MobileCustomer> patch)
                {
                    return base.UpdateAsync(id, patch);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task<MobileCustomer> PostAsync(MobileCustomer item)
                {
                    return base.InsertAsync(item);
                }

                [AuthorizeLevel(AuthorizationLevel.Admin)]
                protected override Task DeleteAsync(string id)
                {
                    return base.DeleteAsync(id);
                }
            }
        }

    Обратите внимание на использование атрибута AuthorizeLevel для ограничения открытого доступа к операциям вставки, обновления и удаления на контроллере. В рамках этого сценария список экземпляров Customer будет доступен только для чтения, но мы разрешаем создание новых экземпляров Orders и их связь с существующими заказчиками.

2. В папке **Controllers** добавьте файл **MobileOrderController.cs**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using ShoppingService.DataObjects;
        using ShoppingService.Models;
        using System.Linq;
        using System.Threading.Tasks;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Description;
        using System.Web.Http.OData;

        namespace ShoppingService.Controllers
        {
            public class MobileOrderController : TableController<MobileOrder>
            {
                protected override void Initialize(HttpControllerContext controllerContext)
                {
                    base.Initialize(controllerContext);
                    ExistingContext context = new ExistingContext();
                    DomainManager = new MobileOrderDomainManager(context, Request, Services);
                }

                public IQueryable<MobileOrder> GetAllMobileOrders()
                {
                    return Query();
                }

                public SingleResult<MobileOrder> GetMobileOrder(string id)
                {
                    return Lookup(id);
                }

                public Task<MobileOrder> PatchMobileOrder(string id, Delta<MobileOrder> patch)
                {
                    return UpdateAsync(id, patch);
                }

                [ResponseType(typeof(MobileOrder))]
                public async Task<IHttpActionResult> PostMobileOrder(MobileOrder item)
                {
                    MobileOrder current = await InsertAsync(item);
                    return CreatedAtRoute("Tables", new { id = current.Id }, current);
                }

                public Task DeleteMobileOrder(string id)
                {
                    return DeleteAsync(id);
                }
            }
        }

3. Теперь все готово к запуску службы. Нажмите клавишу **F5** и используйте тестовый клиент, встроенный в страницу справки, для изменения данных.

Обратите внимание, что обе реализации контроллера монопольно используют DTO **MobileCustomer** и **MobileOrder** и не зависят от базовой модели. Эти DTO поддерживают сериализацию для JSON и могут использоваться для обмена данными с SDK клиента мобильных служб на любых платформах. Например, при сборке приложения для Магазина Windows соответствующий тип на стороне клиента будет выглядеть, как показано ниже. Этот тип будет работать аналогично на других клиентских платформах.

    using Microsoft.WindowsAzure.MobileServices;
    using System;

    namespace ShoppingClient
    {
        public class MobileCustomer
        {
            public string Id { get; set; }

            public string Name { get; set; }

            [CreatedAt]
            public DateTimeOffset? CreatedAt { get; set; }

            [UpdatedAt]
            public DateTimeOffset? UpdatedAt { get; set; }

            public bool Deleted { get; set; }

            [Version]
            public string Version { get; set; }

        }

    }

В качестве следующего шага можно собрать клиентское приложение для доступа к службе. Дополнительные сведения см. в статье [Добавление мобильных служб в существующее приложение](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#update-the-app-to-use-the-mobile-service).

<!---HONumber=September15_HO1-->