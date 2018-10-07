---
title: Реализация прикладного уровня микрослужб с помощью веб-интерфейсов API
description: Архитектура микрослужб .NET для упакованных в контейнеры приложений .NET | Реализация прикладного уровня микрослужб с помощью веб-интерфейсов API
author: CESARDELATORRE
ms.author: wiwagn
ms.date: 12/12/2017
ms.openlocfilehash: 1af8d0290eea26d57f4744bbd6d9819d886d4db4
ms.sourcegitcommit: 979597cd8055534b63d2c6ee8322938a27d0c87b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37106558"
---
# <a name="implementing-the-microservice-application-layer-using-the-web-api"></a>Реализация прикладного уровня микрослужб с помощью веб-интерфейсов API

## <a name="using-dependency-injection-to-inject-infrastructure-objects-into-your-application-layer"></a>Внедрение объектов инфраструктуры в прикладной уровень с помощью внедрения зависимостей

Как упоминалось ранее, прикладной уровень может быть реализован в рамках создаваемого артефакта, например проекта веб-интерфейса API или проекта веб-приложения MVC. При создании микрослужбы с помощью ASP.NET Core прикладным уровнем обычно является библиотека веб-интерфейсов API. Чтобы отделить ту часть, которая берется из ASP.NET Core (инфраструктуру и контроллеры), от пользовательского кода прикладного уровня, можно поместить прикладной уровень в отдельную библиотеку классов, хотя это необязательно.

Например, код прикладного уровня для микрослужбы размещения заказов реализуется непосредственно в рамках проекта **Ordering.API** (проекта веб-интерфейса API ASP.NET Core), как показано на рисунке 9-23.

![](./media/image20.png)

**Рис. 9-23**. Прикладной уровень в проекте веб-интерфейса API ASP.NET Core Ordering.API

ASP.NET Core содержит простой [встроенный контейнер IoC](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) (представленный интерфейсом IServiceProvider), поддерживающий внедрение через конструктор по умолчанию, а ASP.NET предоставляет посредством внедрения зависимостей определенные службы. В ASP.NET Core под термином *служба* понимаются любые зарегистрированные типы, которые будут внедряться посредством внедрения зависимостей. Настроить службы встроенного контейнера можно в методе ConfigureServices в классе Startup вашего приложения. Зависимости реализуются в службах, которые требуются типу.

Как правило, необходимо внедрить зависимости, реализующие объекты инфраструктуры. Типичной внедряемой зависимостью является репозиторий. Однако можно внедрять и любые другие имеющиеся зависимости инфраструктуры. Чтобы упростить реализацию, можно напрямую внедрить объект на основе шаблона "Единица работы" (объект EF DbContext), так как DBContext также является реализацией сохраняемых объектов инфраструктуры.

В приведенном ниже примере показано, как платформа .NET Core внедряет необходимые объекты репозитория посредством конструктора. Класс представляет собой обработчик команд, о котором мы поговорим в следующем разделе.

```csharp
public class CreateOrderCommandHandler
    : IAsyncRequestHandler<CreateOrderCommand, bool>
{
    private readonly IOrderRepository _orderRepository;
    private readonly IIdentityService _identityService;
    private readonly IMediator _mediator;

    // Using DI to inject infrastructure persistence Repositories
    public CreateOrderCommandHandler(IMediator mediator, 
                                     IOrderRepository orderRepository, 
                                     IIdentityService identityService)
    {
        _orderRepository = orderRepository ?? 
                          throw new ArgumentNullException(nameof(orderRepository));
        _identityService = identityService ?? 
                          throw new ArgumentNullException(nameof(identityService));
        _mediator = mediator ?? 
                                 throw new ArgumentNullException(nameof(mediator));
    }

    public async Task<bool> Handle(CreateOrderCommand message)
    {
        // Create the Order AggregateRoot
        // Add child entities and value objects through the Order aggregate root
        // methods and constructor so validations, invariants, and business logic 
        // make sure that consistency is preserved across the whole aggregate
        var address = new Address(message.Street, message.City, message.State, 
                                  message.Country, message.ZipCode);
        var order = new Order(message.UserId, address, message.CardTypeId, 
                              message.CardNumber, message.CardSecurityNumber, 
                              message.CardHolderName, message.CardExpiration);
            
        foreach (var item in message.OrderItems)
        {
            order.AddOrderItem(item.ProductId, item.ProductName, item.UnitPrice,
                               item.Discount, item.PictureUrl, item.Units);
        }

        _orderRepository.Add(order);

        return await _orderRepository.UnitOfWork
            .SaveEntitiesAsync();
    }
}
```

Этот класс использует внедренные репозитории для выполнения транзакции и сохранения изменений состояния. Не имеет значения, является ли класс обработчиком команд, методом контроллера веб-интерфейса API ASP.NET Core или [службой приложения DDD](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/). В любом случае он представляет собой простой класс, который использует репозитории, сущности домена и другие средства координации приложения подобно тому, как это делает обработчик команд. Внедрение зависимостей работает одинаково для всех перечисленных выше классов, как в этом примере, в котором внедрение зависимостей основано на конструкторе.

### <a name="registering-the-dependency-implementation-types-and-interfaces-or-abstractions"></a>Регистрация типов и интерфейсов или абстракций для реализации зависимостей

Прежде чем использовать объекты, внедренные посредством конструкторов, необходимо знать, где следует зарегистрировать интерфейсы и классы, которые предоставляют объекты, внедренные в приложение с помощью внедрения зависимостей (например, в случае внедрения зависимостей на основе конструктора, показанного выше).

#### <a name="using-the-built-in-ioc-container-provided-by-aspnet-core"></a>Использование встроенного конструктора IoC, предоставляемого платформой ASP.NET Core

При использовании встроенного контейнера IoC, предоставляемого платформой ASP.NET Core, типы, которые нужно внедрить, регистрируются в методе ConfigureServices в файле Startup.cs, как в следующем коде:

```csharp
// Registration of types into ASP.NET Core built-in container
public void ConfigureServices(IServiceCollection services)
{
    // Register out-of-the-box framework services.
    services.AddDbContext<CatalogContext>(c =>
    {
        c.UseSqlServer(Configuration["ConnectionString"]);
    },
    ServiceLifetime.Scoped
    );
    services.AddMvc();
    // Register custom application dependencies.
    services.AddScoped<IMyCustomRepository, MyCustomSQLRepository>();
}
```

Чаще всего типы регистрируются в контейнере IoC парами, состоящими из интерфейса и связанного с ним класса реализации. Затем при запросе объекта из контейнера IoC посредством любого конструктора вы запрашиваете объект определенного типа интерфейса. Например, в предыдущем примере в последней строке указано, что когда любой из конструкторов имеет зависимость от IMyCustomRepository (интерфейса или абстракции), контейнер IoC внедряет экземпляр класса реализации MyCustomSQLServerRepository.

#### <a name="using-the-scrutor-library-for-automatic-types-registration"></a>Автоматическая регистрация типов с помощью библиотеки Scrutor

При использовании внедрения зависимостей в .NET Core может потребоваться проверить сборку и автоматически зарегистрировать ее типы в соответствии с соглашением. Эта возможность в настоящее время недоступна в ASP.NET Core. Однако для этого можно использовать библиотеку [Scrutor](https://github.com/khellang/Scrutor). Такой подход удобен в случае, если имеются десятки типов, которые необходимо зарегистрировать в контейнере IoC.

#### <a name="additional-resources"></a>Дополнительные ресурсы

-   **Мэтью Кинг (Matthew King). Регистрация служб в Scrutor**
    [*https://mking.net/blog/registering-services-with-scrutor*](https://mking.net/blog/registering-services-with-scrutor)

<!-- -->

-   **Кристиан Хелланг (Kristian Hellang). Scrutor.** Репозиторий GitHub.
    [*https://github.com/khellang/Scrutor*](https://github.com/khellang/Scrutor)

#### <a name="using-autofac-as-an-ioc-container"></a>Использование Autofac в качестве IoC

Вы также можете использовать дополнительные контейнеры IoC и подключить их к конвейеру ASP.NET Core, как в случае с микрослужбой размещения заказов в eShopOnContainers, которая использует [Autofac](https://autofac.org/). При использовании Autofac типы, как правило, регистрируются посредством модулей, что позволяет разделить типы регистрации на несколько файлов в зависимости от местонахождения типов, так же как типы приложения распределяются по нескольким библиотекам классов.

Например, ниже приведен [модуль приложения Autofac](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Infrastructure/AutofacModules/ApplicationModule.cs) для проекта [веб-интерфейса API Ordering.API](https://github.com/dotnet-architecture/eShopOnContainers/tree/master/src/Services/Ordering/Ordering.API) с типами, которые потребуется внедрить.

```csharp
public class ApplicationModule : Autofac.Module
{
    public string QueriesConnectionString { get; }
    public ApplicationModule(string qconstr)
    {
        QueriesConnectionString = qconstr;
    }

    protected override void Load(ContainerBuilder builder)
    {
        builder.Register(c => new OrderQueries(QueriesConnectionString))
            .As<IOrderQueries>()
            .InstancePerLifetimeScope();
        builder.RegisterType<BuyerRepository>()
            .As<IBuyerRepository>()
            .InstancePerLifetimeScope();
        builder.RegisterType<OrderRepository>()
            .As<IOrderRepository>()
            .InstancePerLifetimeScope();
        builder.RegisterType<RequestManager>()
            .As<IRequestManager>()
            .InstancePerLifetimeScope();
   }
}
```

Принципы и процесс регистрации очень похожи на регистрацию типов с помощью встроенного контейнера IoC ASP.NET Core, но синтаксис при использовании Autofac немного иной.

В примере кода абстракция IOrderRepository регистрируется вместе с классом реализации OrderRepository. Это означает, что каждый раз, когда в конструкторе объявляется зависимость посредством абстракции или интерфейса IOrderRepository, контейнер IoC внедряет экземпляр класса OrderRepository.

Тип области экземпляра определяет то, как экземпляр используется совместно при запросах к одной и той же службе или зависимости. При запросе зависимости контейнер IoC может вернуть следующее:

-   один экземпляр для каждой области времени существования (в контейнере IoC ASP.NET Core такой экземпляр называется экземпляром *с заданной областью*);

-   новый экземпляр для каждой зависимости (в контейнере IoC ASP.NET Core такой экземпляр называется *временным*);

-   один общий экземпляр для всех объектов, использующих контейнер IoC (в контейнере IoC ASP.NET Core такой экземпляр называется *единичным*).

#### <a name="additional-resources"></a>Дополнительные ресурсы

-   **Общие сведения о внедрении зависимостей в ASP.NET Core**
    [*https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection*](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)

-   **Autofac.** Официальная документация.
    [*http://docs.autofac.org/en/latest/*](http://docs.autofac.org/en/latest/)

-   **Сравнение времени существования службы контейнеров IoC ASP.NET Core с областями действия экземпляра контейнера IoC Autofac. Сезар де ла Торре (Cesar de la Torre).**
    [*https://blogs.msdn.microsoft.com/cesardelatorre/2017/01/26/comparing-asp-net-core-ioc-service-life-times-and-autofac-ioc-instance-scopes/*](https://blogs.msdn.microsoft.com/cesardelatorre/2017/01/26/comparing-asp-net-core-ioc-service-life-times-and-autofac-ioc-instance-scopes/)

## <a name="implementing-the-command-and-command-handler-patterns"></a>Реализация шаблонов команд и обработчиков команд

В примере внедрения зависимостей через конструктор в предыдущем разделе контейнер IoC внедрял репозитории через конструктор в классе. Но куда именно они внедрялись? В простом веб-интерфейсе API (например, микрослужбе каталога в eShopOnContainers) они внедряются на уровне контроллеров MVC в конструкторе контроллера. Однако в коде, приведенном в начале этого раздела (класс [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs) из службы Ordering.API в eShopOnContainers), внедрение зависимостей производится через конструктор определенного обработчика команд. Давайте рассмотрим, что такое обработчик команд и зачем его использовать.

Шаблон команды, по сути, связан с шаблоном CQRS, который был представлен ранее в этом руководстве. CQRS имеет два аспекта. Первый аспект — это запросы, причем используются упрощенные запросы на основе микро-ORM [Dapper](https://github.com/StackExchange/dapper-dot-net), который был рассмотрен ранее. Второй аспект — это команды, которые являются отправной точкой для транзакций, и канал входных данных извне службы.

Как показано на рисунке 9-24, шаблон основан на принятии команд со стороны клиента, их обработке на основе правил модели предметной области и, наконец, сохранении состояний с помощью транзакций.

![](./media/image21.png)

**Рис. 9-24**. Общее представление команд или "уровня транзакций" в шаблоне CQRS

### <a name="the-command-class"></a>Класс команд

Команда — это запрос к системе на выполнение действия, которое изменяет состояние системы. Команды являются императивными и должны обрабатываться только один раз.

Так как команды являются императивами, в их именах обычно есть глагол в повелительном наклонении (например, create или update) и может присутствовать тип агрегата, например CreateOrderCommand. В отличие от события, команда не связана с фактом в прошлом. Это всего лишь запрос, который может быть отклонен.

Команды могут поступать из пользовательского интерфейса в результате инициации запроса пользователем или из диспетчера процессов, когда он предписывает агрегату выполнить действие.

Важной особенностью команды является то, что она должна обрабатываться только один раз одним получателем. Связано это с тем, что команда представляет собой одно действие или транзакцию, которую нужно выполнить в приложении. Например, одну и ту же команду создания заказа не следует обрабатывать несколько раз. Это важное различие между командами и событиями. События могут обрабатываться несколько раз, так как одно событие может представлять интерес для множества систем или микрослужб.

Кроме того, важно, чтобы команда обрабатывалась только одни раз, если она не является идемпотентной. Команда является идемпотентной, если она может выполняться несколько раз с одинаковым результатом либо из-за самого ее характера, либо из-за способа обработки команды системой.

Команды имеет смысл делать идемпотентными, если этого требуют бизнес-правила и инварианты предметной области. Продолжая приведенный выше пример, если по какой-либо причине (логика повтора, взлом и т. д.) одна и та же команда CreateOrder поступает в систему несколько раз, необходимо иметь возможность определить такую ситуацию и предотвратить создание нескольких заказов. Для этого в операции необходимо включить какой-либо идентификатор, чтобы определять, были ли команда или обновление уже обработаны.

Команда отправляется одному получателю. Она не публикуется. Публикуются события интеграции, сообщающие некий факт — что произошло что-то, что может представлять интерес для приемников событий. Издателя событий не интересует, какие приемники получат событие и что они будут с ним делать. Однако с событиями интеграции дело обстоит иначе, о чем уже говорилось в предыдущих разделах.

Команда реализуется с помощью класса, который содержит поля данных или коллекции со всеми сведениями, необходимыми для выполнения этой команды. Команда — это объект передачи данных (DTO) особого типа, предназначенный специально для запроса изменений или транзакций. Сама по себе команда основана только на тех сведениях, которые необходимы для ее обработки, и ни на чем больше.

В приведенном ниже примере показан упрощенный класс CreateOrderCommand. Это неизменяемая команда, которая используется в микрослужбе размещения заказов в eShopOnContainers.

```csharp
// DDD and CQRS patterns comment
// Note that we recommend that you implement immutable commands
// In this case, immutability is achieved by having all the setters as private
// plus being able to update the data just once, when creating the object
// through the constructor.
// References on immutable commands:
// http://cqrs.nu/Faq
// https://docs.spine3.org/motivation/immutability.html
// http://blog.gauffin.org/2012/06/griffin-container-introducing-command-support/
// https://msdn.microsoft.com/library/bb383979.aspx
[DataContract]
public class CreateOrderCommand
    :IAsyncRequest<bool>
{
    [DataMember]
    private readonly List<OrderItemDTO> _orderItems;
    [DataMember]
    public string City { get; private set; }
    [DataMember]
    public string Street { get; private set; }
    [DataMember]
    public string State { get; private set; }
    [DataMember]
    public string Country { get; private set; }
    [DataMember]
    public string ZipCode { get; private set; }
    [DataMember]
    public string CardNumber { get; private set; }
    [DataMember]
    public string CardHolderName { get; private set; }
    [DataMember]
    public DateTime CardExpiration { get; private set; }
    [DataMember]
    public string CardSecurityNumber { get; private set; }
    [DataMember]
    public int CardTypeId { get; private set; }
    [DataMember]
    public IEnumerable<OrderItemDTO> OrderItems => _orderItems;

    public CreateOrderCommand()
    {
        _orderItems = new List<OrderItemDTO>();
    }

    public CreateOrderCommand(List<OrderItemDTO> orderItems, string city,
        string street,
        string state, string country, string zipcode,
        string cardNumber, string cardHolderName, DateTime cardExpiration,
        string cardSecurityNumber, int cardTypeId) : this()
    {
        _orderItems = orderItems;
        City = city;
        Street = street;
        State = state;
        Country = country;
        ZipCode = zipcode;
        CardNumber = cardNumber;
        CardHolderName = cardHolderName;
        CardSecurityNumber = cardSecurityNumber;
        CardTypeId = cardTypeId;
        CardExpiration = cardExpiration;
    }

    public class OrderItemDTO
    {
        public int ProductId { get; set; }
        public string ProductName { get; set; }
        public decimal UnitPrice { get; set; }
        public decimal Discount { get; set; }
        public int Units { get; set; }
        public string PictureUrl { get; set; }
    }
}
```

По существу, класс команды содержит все данные, необходимые для выполнения бизнес-транзакции с помощью объектов модели предметной области. Таким образом, команды — это попросту структуры данных, которые содержат доступные только для чтения данные, но не алгоритмы. Имя команды указывает на ее назначение. Во многих языках, например в C\#, команды представлены классами, но они не являются настоящими классами в объектно-ориентированном смысле.

Дополнительной характеристикой команд является неизменяемость, так как предполагается, что они обрабатываются непосредственно моделью предметной области. Они не должны изменяться в течение предполагаемого времени существования. В классе C\# неизменяемость может обеспечиваться благодаря отсутствию методов задания или других методов, которые изменяют внутреннее состояние.

Например, в плане данных класс команды для создания заказа может быть аналогичен создаваемому заказу, однако, вероятно, требуемые атрибуты могут различаться. Например, команда CreateOrderCommand не включает в себя идентификатор заказа, так как заказ еще не создан.

Многие классы команд могут быть простыми и требуют лишь несколько полей, связанных с изменяемым состоянием. Например, это верно в случае изменения состояния заказа с "обрабатывается" на "оплачен" или "отправлен" с помощью команды наподобие следующей:

```csharp
[DataContract]
public class UpdateOrderStatusCommand
    :IAsyncRequest<bool>
{
    [DataMember]
    public string Status { get; private set; }

    [DataMember]
    public string OrderId { get; private set; }

    [DataMember]
    public string BuyerIdentityGuid { get; private set; }
}
```

Некоторые разработчики разделяют объекты запросов пользовательского интерфейса и объекты DTO команд, но это всего лишь вопрос предпочтений. Такое разделение требует значительных усилий, а преимущества невелики, причем объекты очень схожи по сути. Например, в eShopOnContainers некоторые команды поступают непосредственно со стороны клиента.

### <a name="the-command-handler-class"></a>Класс обработчика команд

Для каждой команды следует реализовать класс обработчика команд. Этого требует шаблон, и именно в этом классе будут использоваться объект команды, объекты предметной области и объекты репозиториев инфраструктуры. Обработчик команд — это, по сути, центральный элемент прикладного уровня в рамках CQRS и DDD. Однако вся логика предметной области должна содержаться в классах предметной области — корневых объектах агрегатов (корневых сущностях), дочерних сущностях или [службах предметной области](https://lostechies.com/jimmybogard/2008/08/21/services-in-domain-driven-design/), но не в обработчике команд, который представляет собой класс прикладного уровня.

Обработчик команд принимает команду и получает результат из используемого агрегата. Результатом должно быть успешное выполнение команды или исключение. В случае исключения состояние системы не должно меняться.

Обработчик команд обычно выполняет следующие действия:

-   получает объект команды, например DTO (от [медиатора](https://en.wikipedia.org/wiki/Mediator_pattern) или другого объекта инфраструктуры);

-   проверяет допустимость команды (если она не была проверена медиатором);

-   создает экземпляр корневой сущности агрегата, являющийся целевым для текущей команды;

-   выполняет метод экземпляра корневой сущности агрегата, получая необходимые данные из команды;

-   сохраняет новое состояние агрегата в связанной с ним базе данных. Эта последняя операция и является транзакцией.

Как правило, обработчик команд работает с одним агрегатом, определяемым корневым объектом агрегата (корневой сущностью). Если принимаемая команда должна влиять на несколько агрегатов, можно использовать события предметной области для распространения состояний или действий в нескольких агрегатах.

Важным моментом является то, что при обработке команды вся логика предметной области должна находиться внутри модели предметной области (агрегатов), причем она должна быть полностью инкапсулирована и готова для модульного тестирования. Обработчик команд служит лишь для того, чтобы получить модель предметной области из базы данных и (на последнем этапе) сообщить уровню инфраструктуры (репозиториям) о необходимости сохранить изменения в случае изменения модели. Преимуществом такого подхода является возможность рефакторинга логики предметной области в рамках изолированной, полностью инкапсулированной поведенческой модели предметной области без изменения кода на прикладном уровне или уровне инфраструктуры, то есть на связующем уровне (обработчики команд, веб-интерфейс API, репозитории и т. д.).

Если обработчики команд становятся слишком сложными, содержащими слишком много логики, это может быть признаком плохого кода. Проверьте их и, если найдете логику предметной области, выполните рефакторинг кода, чтобы переместить эту логику в методы объектов предметной области (корневая сущность агрегата и дочерняя сущность).

В приведенном ниже коде в качестве примера класса обработчика команд показан тот же класс CreateOrderCommandHandler, который вы уже видели в начале этой главы. В этом случае мы хотим привлечь внимание к методу Handle и операциям с объектами и агрегатами модели предметной области.

```csharp
public class CreateOrderCommandHandler
    : IAsyncRequestHandler<CreateOrderCommand, bool>
{
    private readonly IOrderRepository _orderRepository;
    private readonly IIdentityService _identityService;
    private readonly IMediator _mediator;

    // Using DI to inject infrastructure persistence Repositories
    public CreateOrderCommandHandler(IMediator mediator, 
                                     IOrderRepository orderRepository, 
                                     IIdentityService identityService)
    {
        _orderRepository = orderRepository ?? 
                          throw new ArgumentNullException(nameof(orderRepository));
        _identityService = identityService ?? 
                          throw new ArgumentNullException(nameof(identityService));
        _mediator = mediator ?? 
                                 throw new ArgumentNullException(nameof(mediator));
    }

    public async Task<bool> Handle(CreateOrderCommand message)
    {
        // Create the Order AggregateRoot
        // Add child entities and value objects through the Order aggregate root
        // methods and constructor so validations, invariants, and business logic 
        // make sure that consistency is preserved across the whole aggregate
        var address = new Address(message.Street, message.City, message.State, 
                                  message.Country, message.ZipCode);
        var order = new Order(message.UserId, address, message.CardTypeId, 
                              message.CardNumber, message.CardSecurityNumber, 
                              message.CardHolderName, message.CardExpiration);
            
        foreach (var item in message.OrderItems)
        {
            order.AddOrderItem(item.ProductId, item.ProductName, item.UnitPrice,
                               item.Discount, item.PictureUrl, item.Units);
        }

        _orderRepository.Add(order);

        return await _orderRepository.UnitOfWork
            .SaveEntitiesAsync();
    }
}
```

Обработчик команд должен выполнять следующие дополнительные действия:

-   использовать данные команды для взаимодействия с методами и поведением корневой сущности агрегата;

-   вызывать события предметной области внутри объектов предметной области во время выполнения транзакции прозрачным с точки зрения обработчика команд образом;

-   в случае успешного результата операции агрегата вызывать обработчик команд событий интеграции после завершения транзакции. (Эти события могут также вызываться классами инфраструктуры, такими как репозитории.)

#### <a name="additional-resources"></a>Дополнительные ресурсы

-   **Марк Симанн (Mark Seemann). Граничный слой приложения не является объектно-ориентированным**
    [*http://blog.ploeh.dk/2011/05/31/AttheBoundaries,ApplicationsareNotObject-Oriented/*](http://blog.ploeh.dk/2011/05/31/AttheBoundaries,ApplicationsareNotObject-Oriented/)

-   **Команды и события**
    [*http://cqrs.nu/Faq/commands-and-events*](http://cqrs.nu/Faq/commands-and-events)

-   **Что делает обработчик команд?**
    [*http://cqrs.nu/Faq/command-handlers*](http://cqrs.nu/Faq/command-handlers)

-   **Джимми Богард (Jimmy Bogard). Шаблоны команд домена — обработчики**
    [*https://jimmybogard.com/domain-command-patterns-handlers/*](https://jimmybogard.com/domain-command-patterns-handlers/)

-   **Джимми Богард (Jimmy Bogard). Шаблоны команд домена — проверка**
    [*https://jimmybogard.com/domain-command-patterns-validation/*](https://jimmybogard.com/domain-command-patterns-validation/)

## <a name="the-command-process-pipeline-how-to-trigger-a-command-handler"></a>Конвейер обработки команд: активация обработчика команд

Следующий вопрос заключается в том, как вызывается обработчик команд. Его можно вызывать из каждого связанного контроллера ASP.NET Core. Однако такой подход требует слишком большого количества связей и не идеален.

Другие два варианта, которые рекомендуются, представлены ниже:

-   посредством артефакта шаблона медиатора в памяти;

-   с помощью асинхронной очереди сообщений между контроллерами и обработчиками.

### <a name="using-the-mediator-pattern-in-memory-in-the-command-pipeline"></a>Использование шаблона медиатора (в памяти) в конвейере команд

Как показано на рисунке 9-25, в рамках подхода на основе CQRS используется интеллектуальный медиатор, похожий на шину в памяти, который может осуществлять перенаправление в нужный обработчик команд в соответствии с типом полученной команды или объекта DTO. Одиночные черные стрелки между компонентами представляют зависимости между объектами (которые часто внедряются посредством внедрения зависимостей) и соответствующие взаимодействия.

![](./media/image22.png)

**Рис. 9-25**. Использование шаблона медиатора в процессе обработки в отдельной микрослужбе CQRS

Основанием для применения шаблона медиатора является то, что в корпоративных приложениях обработка запросов может становиться сложной. Вам может требоваться добавить сквозную функциональность, например ведения журнала, проверки, аудит и безопасность. В таком случае вы можете применить конвейер медиатора (см. статью [Шаблон медиатора](https://en.wikipedia.org/wiki/Mediator_pattern)) в качестве средства для реализации дополнительной сквозной функциональности или поведения.

Медиатор — это объект, который инкапсулирует методы выполнения этого процесса: он координирует выполнение на основе состояния, способы вызова обработчика команд и полезные данные, предоставляемые ему. С помощью медиатора вы можете применять сквозную функциональность централизованным и прозрачным образом, применяя декораторы (или [расширения функциональности конвейера](https://github.com/jbogard/MediatR/wiki/Behaviors) начиная с [MediatR 3](https://www.nuget.org/packages/MediatR/3.0.0)). Дополнительную информацию см. в статье [Шаблон декоратора](https://en.wikipedia.org/wiki/Decorator_pattern).

Декораторы и расширения функциональности похожи на [аспектно-ориентированное программирование (АОП)](https://en.wikipedia.org/wiki/Aspect-oriented_programming), но применяются к определенному конвейеру обработки, управляемому медиатором. Аспекты в АОП, которые реализуют сквозную функциональность, применяются на основе *средств внедрения аспектов*, внедряемых во время компиляции или с помощью перехвата вызовов объектов. Иногда говорят, что оба подхода АОП работают "волшебным образом", так как их применение трудно проследить. В случае серьезных проблем или ошибок отладка в АОП может вызывать трудности. С другой стороны, эти декораторы и расширения функциональности являются явными и применяются только в контексте медиатора, поэтому отладка является гораздо более предсказуемой и легкой.

Например, в микрослужбе размещения заказов eShopOnContainers реализованы два образца расширений функциональности: класс [LogBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/LoggingBehavior.cs) и класс [ValidatorBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/ValidatorBehavior.cs). Реализация расширений функциональности рассмотрена в следующем разделе. В нем показано, как в eShopOnContainers реализованы [расширения функциональности](https://github.com/jbogard/MediatR/wiki/Behaviors) [MediatR 3](https://www.nuget.org/packages/MediatR/3.0.0).

### <a name="using-message-queues-out-of-proc-in-the-commands-pipeline"></a>Использование очередей сообщений (внепроцессных) в конвейере команд

Еще один вариант — это использование асинхронных сообщений на основе брокеров или очередей сообщений, как показано на рисунке 9-26. Его можно использовать в сочетании с компонентом медиатора непосредственно перед обработчиком команд.

![](./media/image23.png)

**Рис. 9-26**. Использование очередей сообщений (внепроцессное и внутрипроцессное взаимодействие) с командами CQRS

Использование очередей сообщений для принятия команд может еще более усложнить конвейер команд, так как вам может потребоваться разделить его на два процесса, связанных посредством внешней очереди сообщений. Однако его следует использовать, если вам нужно повысить масштабируемость и производительность с помощью асинхронных сообщений. Представьте, что в ситуации, продемонстрированной на рисунке 9-26, контроллер просто отправляет сообщение команды в очередь и возвращает управление. В этом случае обработчики команд обрабатывают сообщения в удобном для себя темпе. Это важное преимущество очередей: очередь сообщений может служить буфером в случае, когда требуется высочайшая масштабируемость, например в сценариях с большим объемом входящих данных.

Однако из-за асинхронного характера очередей сообщений необходимо продумать способ, который позволит сообщать клиентскому приложению об успешном или неудачном выполнении процесса команды. Как правило, не следует использовать команды, выполняющиеся в автономном режиме. Каждому бизнес-приложению требуется знать, была ли команда обработана успешно или по крайней мере была ли она проверена и принята.

Таким образом, возможность предоставления ответа клиенту после проверки сообщения команды, отправленного в асинхронную очередь, повышает сложность системы по сравнению с внутрипроцессной обработкой команды, при которой результат операции возвращается после выполнения транзакции. При использовании очередей может требоваться возвращать результат обработки команды посредством других сообщений о результате операции, для чего в системе необходимы дополнительные компоненты и пользовательские сообщения.

Кроме того, асинхронные команды являются односторонними, что во многих случаях не требуется. Об этом говорят Алексей Бурцев (Burtsev Alexey) и Грег Янг (Greg Young) в этой интересной [беседе в Интернете](https://groups.google.com/forum/#!msg/dddcqrs/xhJHVxDx2pM/WP9qP8ifYCwJ):

\[Алексей Бурцев\] Мне часто встречается код, в котором асинхронная обработка команд или односторонние сообщения команд используются без всякой причины (в нем не выполняются длительные операции или внешний асинхронный код; в нем даже не пересекаются границы приложения для использования шины сообщений). Зачем это излишнее усложнение? В действительности мне еще не встречался пример кода CQRS с блокирующими обработчиками команд, хотя в большинстве случаев они вполне применимы.

\[Грег Янг\] \[...\] асинхронных команд не существует; они по сути являются событиями. Если я могу принять то, что вы мне отправили, и породить событие в случае несогласия, значит, вы уже не указываете, что мне нужно что-то сделать. Вы сообщаете мне, что что-то было сделано. Сначала разница кажется небольшой, но она имеет множество последствий.

Асинхронные команды значительно повышают сложность системы, так как нет простого способа сообщать о сбоях. Поэтому асинхронные команды рекомендуются только при высоких требованиях к масштабируемости или в особых случаях, когда взаимодействие с внутренними микрослужбами осуществляется посредством сообщений. В этих ситуациях необходимо спроектировать отдельную систему для информирования о сбоях и восстановления.

В первоначальной версии eShopOnContainers было решено использовать синхронную обработку команд, начиная с HTTP-запросов, на основе шаблона медиатора. Это позволяет легко возвращать результат процесса (успех или неудача), как в реализации [CreateOrderCommandHandler](https://github.com/dotnet-architecture/eShopOnContainers/blob/master/src/Services/Ordering/Ordering.API/Application/Commands/CreateOrderCommandHandler.cs).

В любом случае решение должно приниматься на основе бизнес-требований к приложению или микрослужбе.

## <a name="implementing-the-command-process-pipeline-with-a-mediator-pattern-mediatr"></a>Реализация конвейера обработки команд с помощью шаблона медиатора (MediatR)

В качестве примера реализации в этом руководстве используется внутрипроцессный конвейер на основе шаблона медиатора, обеспечивающий прием команд и их маршрутизацию в памяти в соответствующие обработчики команд. В руководстве также предлагается применять [расширения функциональности](https://github.com/jbogard/MediatR/wiki/Behaviors) для разделения сквозной функциональности.

Для реализации в .NET Core доступно множество библиотек с открытым кодом, которые реализуют шаблон медиатора. В этом руководстве применяется библиотека [MediatR](https://github.com/jbogard/MediatR) с открытым кодом (созданная Джимми Богардом (Jimmy Bogard)), но можно выбрать и другой подход. MediatR — это небольшая простая библиотека, которая позволяет обрабатывать сообщения в памяти как команды, применяя декораторы или расширения функциональности.

Использование шаблона медиатора помогает уменьшить количество связей и изолировать функциональность, связанную с запрашиваемой работой. При этом вы можете автоматически подключаться к обработчику, который выполняет эту работу — в данном случае к обработчику команд.

Еще одна причина для использования шаблона медиатора была раскрыта Джимми Богардом при рецензировании этого руководства:

"Я думаю, здесь стоит упомянуть тестирование — вы получаете ясное и согласованное представление о поведении системы". Запрос поступает, ответ выдается — этот принцип оказался очень полезным при разработке согласованно работающих тестов.

Сначала рассмотрим пример контроллера WebAPI, в котором будет использоваться объект медиатора. Если бы объект медиатора не применялся, потребовалось бы внедрить все зависимости для контроллера, такие как средство ведения журнала и другие. Поэтому конструктор был бы весьма сложным. Если же вы используете объект медиатора, конструктор контроллера может быть гораздо проще. Он может иметь всего лишь несколько зависимостей вместо множества, как в случае, когда для каждой сквозной операции имеется отдельная зависимость. Это показано в следующем примере:

```csharp
public class MyMicroserviceController : Controller
{
    public MyMicroserviceController(IMediator mediator, 
                                    IMyMicroserviceQueries microserviceQueries)
    // ...
```

Как можно видеть, медиатор обеспечивает простой контроллер веб-интерфейса API, в котором нет ничего лишнего. Кроме того, в методах контроллера код для отправки команды объекту медиатора ограничивается практически одной строкой:

```csharp
[Route("new")]
[HttpPost] 
public async Task<IActionResult> ExecuteBusinessOperation([FromBody]RunOpCommand 
                                                               runOperationCommand) 
{
    var commandResult = await _mediator.SendAsync(runOperationCommand); 

    return commandResult ? (IActionResult)Ok() : (IActionResult)BadRequest();
}
```

### <a name="implementing-idempotent-commands"></a>Реализация идемпотентных команд

Более сложным примером по сравнению с приведенным выше в приложении eShopOnContainers является отправка объекта CreateOrderCommand из микрослужбы размещения заказов. Однако поскольку бизнес-процесс размещения заказов является немного более сложным и в данном случае он фактически начинается в микрослужбе Basket, действие отправки объекта CreateOrderCommand выполняется из обработчика событий интеграции с именем [UserCheckoutAcceptedIntegrationEvent.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/IntegrationEvents/EventHandling/UserCheckoutAcceptedIntegrationEventHandler.cs), а не из простого контроллера WebAPI, вызываемого из клиентского приложения, как в предыдущем более простом примере. 

Тем не менее действие отправки команды в MediatR по многом схоже, как показано в приведенном ниже коде.

```csharp
var createOrderCommand = new CreateOrderCommand(eventMsg.Basket.Items,     
                                                eventMsg.UserId, eventMsg.City, 
                                                eventMsg.Street, eventMsg.State,
                                                eventMsg.Country, eventMsg.ZipCode,
                                                eventMsg.CardNumber, 
                                                eventMsg.CardHolderName, 
                                                eventMsg.CardExpiration,
                                                eventMsg.CardSecurityNumber,  
                                                eventMsg.CardTypeId);

var requestCreateOrder = new IdentifiedCommand<CreateOrderCommand,bool>(createOrderCommand, 
                                                                        eventMsg.RequestId);
result = await _mediator.Send(requestCreateOrder);
```

Этот случай также немного сложнее, так как мы реализуем идемпотентные команды. Процесс CreateOrderCommand должен быть идемпотентным, чтобы в случае повторной передачи по сети одного и того же сообщения по какой-либо причине, например из-за выполнения повторных попыток, заказ обрабатывался бы только один раз.

Для этого бизнес-команда (в данном случае CreateOrderCommand) упаковывается и внедряется в универсальный класс IdentifiedCommand, отслеживаемый по идентификатору каждого поступающего по сети сообщения, которое должно быть идемпотентным.

В приведенном ниже коде видно, что IdentifiedCommand — это просто объект DTO с идентификатором и объектом упакованной бизнес-команды.

```csharp
public class IdentifiedCommand<T, R> : IRequest<R>
    where T : IRequest<R>
{
    public T Command { get; }
    public Guid Id { get; }
    public IdentifiedCommand(T command, Guid id)
    {
        Command = command;
        Id = id;
    }
}
```

Затем обработчик CommandHandler для IdentifiedCommand с именем [IdentifiedCommandHandler.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/IdentifiedCommandHandler.cs) проверяет, имеется ли уже идентификатор, полученный в сообщении, в таблице. Если он имеется, команда не будет обработана повторно, то есть она является идемпотентной. Этот код инфраструктуры выполняется в результате вызова метода `_requestManager.ExistAsync`, приведенного ниже.

```csharp
// IdentifiedCommandHandler.cs
public class IdentifiedCommandHandler<T, R> : 
                                   IAsyncRequestHandler<IdentifiedCommand<T, R>, R>
                                   where T : IRequest<R>
{
    private readonly IMediator _mediator;
    private readonly IRequestManager _requestManager;

    public IdentifiedCommandHandler(IMediator mediator, 
                                    IRequestManager requestManager)
    {
        _mediator = mediator;
        _requestManager = requestManager;
    }

    protected virtual R CreateResultForDuplicateRequest()
    {
        return default(R);
    }

    public async Task<R> Handle(IdentifiedCommand<T, R> message)
    {
        var alreadyExists = await _requestManager.ExistAsync(message.Id);
        if (alreadyExists)
        {
            return CreateResultForDuplicateRequest();
        }
        else
        {
            await _requestManager.CreateRequestForCommandAsync<T>(message.Id);

            // Send the embeded business command to mediator 
            // so it runs its related CommandHandler 
            var result = await _mediator.Send(message.Command);
                
            return result;
        }
    }
}
```

Объект IdentifiedCommand выступает в роли конверта для бизнес-команды, поэтому когда бизнес-команда должна быть обработана в случае, если идентификатор не повторяется, этот объект берет внутреннюю бизнес-команду и повторно передает ее в медиатор. Это продемонстрировано в последней части приведенного выше кода, в которой выполняется метод `_mediator.Send(message.Command)` из [IdentifiedCommandHandler.cs](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Commands/IdentifiedCommandHandler.cs).

При этом выполняется связывание с обработчиком бизнес-команд и его запуск. В данном случае это обработчик CreateOrderCommandHandler, который выполняет транзакции в базе данных Ordering, как показано в приведенном ниже коде.

```csharp
// CreateOrderCommandHandler.cs
public class CreateOrderCommandHandler
                                   : IAsyncRequestHandler<CreateOrderCommand, bool>
{
    private readonly IOrderRepository _orderRepository;
    private readonly IIdentityService _identityService;
    private readonly IMediator _mediator;

    // Using DI to inject infrastructure persistence Repositories
    public CreateOrderCommandHandler(IMediator mediator, 
                                     IOrderRepository orderRepository, 
                                     IIdentityService identityService)
    {
        _orderRepository = orderRepository ?? 
                          throw new ArgumentNullException(nameof(orderRepository));
        _identityService = identityService ?? 
                          throw new ArgumentNullException(nameof(identityService));
        _mediator = mediator ?? 
                                 throw new ArgumentNullException(nameof(mediator));
    }

    public async Task<bool> Handle(CreateOrderCommand message)
    {
        // Add/Update the Buyer AggregateRoot
        var address = new Address(message.Street, message.City, message.State,
                                  message.Country, message.ZipCode);
        var order = new Order(message.UserId, address, message.CardTypeId,  
                              message.CardNumber, message.CardSecurityNumber, 
                              message.CardHolderName, message.CardExpiration);
            
        foreach (var item in message.OrderItems)
        {
            order.AddOrderItem(item.ProductId, item.ProductName, item.UnitPrice,
                               item.Discount, item.PictureUrl, item.Units);
        }

        _orderRepository.Add(order);

        return await _orderRepository.UnitOfWork
            .SaveEntitiesAsync();
    }
}
```

### <a name="registering-the-types-used-by-mediatr"></a>Регистрация типов, используемых библиотекой MediatR

Чтобы сообщить библиотеке MediatR об используемых классах обработчиков команд, необходимо зарегистрировать классы медиаторов и обработчиков команд в контейнере IoC. По умолчанию библиотека MediatR использует Autofac в качестве контейнера IoC, но вы также можете использовать встроенный контейнер IoC в ASP.NET Core или любой другой контейнер, поддерживаемый MediatR.

В приведенном ниже коде показано, как зарегистрировать типы и команды Mediator при использовании модулей Autofac.

```csharp
public class MediatorModule : Autofac.Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterAssemblyTypes(typeof(IMediator).GetTypeInfo().Assembly)
            .AsImplementedInterfaces();

        // Register all the Command classes (they implement IAsyncRequestHandler)
        // in assembly holding the Commands
        builder.RegisterAssemblyTypes(
                              typeof(CreateOrderCommand).GetTypeInfo().Assembly).
                                   AsClosedTypesOf(typeof(IAsyncRequestHandler<,>));
        // Other types registration
        //...
    }
}
```

Именно в нем творится вся магия MediatR. 

Так как каждый обработчик команд реализует универсальный интерфейс IAsyncRequestHandler&lt;T&gt;, при регистрации сборок код регистрирует в RegisteredAssemblyTypes все типы, созданные как обработчики RequestHandler. При этом он связывает обработчики CommandHandler с командами посредством отношения, определенного в классе CommandHandler, как показано в следующем примере:

```csharp
public class CreateOrderCommandHandler
  : IAsyncRequestHandler<CreateOrderCommand, bool>
{
```

Этот код сопоставляет команды с обработчиками команд. Обработчик представляет собой простой класс, но он наследуется от RequestHandler&lt;T&gt;, и библиотека MediatR обеспечивает его вызов с надлежащими полезными данными.

## <a name="applying-cross-cutting-concerns-when-processing-commands-with-the-behaviors-in-mediatr"></a>Применение сквозной функциональности при обработке команд с помощью расширений функциональности в MediatR

Есть еще одна возможность: применение сквозной функциональности к конвейеру медиатора. В конце кода модуля регистрации Autofac можно заметить, что он регистрирует тип расширения функциональности, а именно: пользовательский класс LoggingBehavior и класс ValidatorBehavior. Вы также можете добавить другие пользовательские расширения функциональности.

```csharp
public class MediatorModule : Autofac.Module
{
    protected override void Load(ContainerBuilder builder)
    {
        builder.RegisterAssemblyTypes(typeof(IMediator).GetTypeInfo().Assembly)
            .AsImplementedInterfaces();

        // Register all the Command classes (they implement IAsyncRequestHandler)
        // in assembly holding the Commands
        builder.RegisterAssemblyTypes(
                              typeof(CreateOrderCommand).GetTypeInfo().Assembly).
                                   AsClosedTypesOf(typeof(IAsyncRequestHandler<,>));
        // Other types registration
        //...        
        builder.RegisterGeneric(typeof(LoggingBehavior<,>)).
                                                   As(typeof(IPipelineBehavior<,>));
        builder.RegisterGeneric(typeof(ValidatorBehavior<,>)).
                                                   As(typeof(IPipelineBehavior<,>));
    }
}
```

Класс [LoggingBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/LoggingBehavior.cs) может быть реализован в виде приведенного ниже кода, который записывает в журнал сведения о выполняемом обработчике команд и результате его выполнения (успех или неудача).

```csharp
public class LoggingBehavior<TRequest, TResponse> 
         : IPipelineBehavior<TRequest, TResponse>
{
    private readonly ILogger<LoggingBehavior<TRequest, TResponse>> _logger;
    public LoggingBehavior(ILogger<LoggingBehavior<TRequest, TResponse>> logger) =>
                                                                  _logger = logger;

    public async Task<TResponse> Handle(TRequest request,
                                        RequestHandlerDelegate<TResponse> next)
    {
        _logger.LogInformation($"Handling {typeof(TRequest).Name}");
        var response = await next();
        _logger.LogInformation($"Handled {typeof(TResponse).Name}");
        return response;
    }
}
```

Если реализовать этот класс декоратора и применить его к конвейеру, в журнал будут записываться сведения о выполнении всех команд, обрабатываемых посредством MediatR.

В микрослужбе размещения заказов eShopOnContainers применяется еще одно расширение функциональности для проведения базовых проверок. Это класс [ValidatorBehavior](https://github.com/dotnet-architecture/eShopOnContainers/blob/dev/src/Services/Ordering/Ordering.API/Application/Behaviors/ValidatorBehavior.cs), основанный на библиотеке [FluentValidation](https://github.com/JeremySkinner/FluentValidation) и показанный в следующем коде:

```csharp
public class ValidatorDecorator<TRequest, TResponse>
    : IAsyncRequestHandler<TRequest, TResponse>
    where TRequest : IAsyncRequest<TResponse>
{
    private readonly IAsyncRequestHandler<TRequest, TResponse> _inner;
    private readonly IValidator<TRequest>[] _validators;

    public ValidatorDecorator(
        IAsyncRequestHandler<TRequest, TResponse> inner,
        IValidator<TRequest>[] validators)
    {
        _inner = inner;
        _validators = validators;
    }

    public async Task<TResponse> Handle(TRequest message)
    {
        var failures = _validators
            .Select(v => v.Validate(message))
            .SelectMany(result => result.Errors)
            .Where(error => error != null)
            .ToList();
            if (failures.Any())
            {
                throw new OrderingDomainException(
                $"Command Validation Errors for type {typeof(TRequest).Name}",
                new ValidationException("Validation exception", failures));
            }
            var response = await _inner.Handle(message);
        return response;
    }
}
```

Затем на основе [FluentValidation](https://github.com/JeremySkinner/FluentValidation) была реализована проверка данных, передаваемых с помощью команды CreateOrderCommand, как показано в следующем коде:

```csharp
public class ValidatorBehavior<TRequest, TResponse> 
         : IPipelineBehavior<TRequest, TResponse>
{
    private readonly IValidator<TRequest>[] _validators;
    public ValidatorBehavior(IValidator<TRequest>[] validators) =>
                                                         _validators = validators;

    public async Task<TResponse> Handle(TRequest request,
                                        RequestHandlerDelegate<TResponse> next)
    {
        var failures = _validators
            .Select(v => v.Validate(request))
            .SelectMany(result => result.Errors)
            .Where(error => error != null)
            .ToList();

        if (failures.Any())
        {
            throw new OrderingDomainException(
                $"Command Validation Errors for type {typeof(TRequest).Name}",
                        new ValidationException("Validation exception", failures));
        }

        var response = await next();
        return response;
    }
}
```

Затем на основе FluentValidation была реализована проверка данных, передаваемых с помощью команды CreateOrderCommand, как показано в следующем коде:

```csharp
public class CreateOrderCommandValidator : AbstractValidator<CreateOrderCommand>
{
    public CreateOrderCommandValidator()
    {
        RuleFor(command => command.City).NotEmpty();
        RuleFor(command => command.Street).NotEmpty();
        RuleFor(command => command.State).NotEmpty();
        RuleFor(command => command.Country).NotEmpty();
        RuleFor(command => command.ZipCode).NotEmpty();
        RuleFor(command => command.CardNumber).NotEmpty().Length(12, 19); 
        RuleFor(command => command.CardHolderName).NotEmpty();
        RuleFor(command => command.CardExpiration).NotEmpty().Must(BeValidExpirationDate).WithMessage("Please specify a valid card expiration date"); 
        RuleFor(command => command.CardSecurityNumber).NotEmpty().Length(3); 
        RuleFor(command => command.CardTypeId).NotEmpty();
        RuleFor(command => command.OrderItems).Must(ContainOrderItems).WithMessage("No order items found"); 
    }

    private bool BeValidExpirationDate(DateTime dateTime)
    {
        return dateTime >= DateTime.UtcNow;
    }

    private bool ContainOrderItems(IEnumerable<OrderItemDTO> orderItems)
    {
        return orderItems.Any();
    }
}

```

Можно создать дополнительные проверки. Это очень простой и изящный способ реализации проверок команд.

Аналогичным образом можно реализовать дополнительные расширения функциональности для других аспектов или сквозной функциональности, которые требуется применять к командам во время их обработки.

#### <a name="additional-resources"></a>Дополнительные ресурсы

##### <a name="the-mediator-pattern"></a>Шаблон медиатора

-   **Шаблон медиатора**
    [*https://en.wikipedia.org/wiki/Mediator\_pattern*](https://en.wikipedia.org/wiki/Mediator_pattern)

##### <a name="the-decorator-pattern"></a>Шаблон декоратора

-   **Шаблон декоратора**
    [*https://en.wikipedia.org/wiki/Decorator\_pattern*](https://en.wikipedia.org/wiki/Decorator_pattern)

##### <a name="mediatr-jimmy-bogard"></a>MediatR (Джимми Богард (Jimmy Bogard))

-   **MediatR.** Репозиторий GitHub.
    [*https://github.com/jbogard/MediatR*](https://github.com/jbogard/MediatR)

-   **CQRS с MediatR и AutoMapper**
    [*https://lostechies.com/jimmybogard/2015/05/05/cqrs-with-mediatr-and-automapper/*](https://lostechies.com/jimmybogard/2015/05/05/cqrs-with-mediatr-and-automapper/)

-   **Сажаем контроллеры на диету: запросы POST и команды.**
    [*https://lostechies.com/jimmybogard/2013/12/19/put-your-controllers-on-a-diet-posts-and-commands/*](https://lostechies.com/jimmybogard/2013/12/19/put-your-controllers-on-a-diet-posts-and-commands/)

-   **Обеспечение сквозной функциональности с помощью конвейера медиатора**
    [*https://lostechies.com/jimmybogard/2014/09/09/tackling-cross-cutting-concerns-with-a-mediator-pipeline/*](https://lostechies.com/jimmybogard/2014/09/09/tackling-cross-cutting-concerns-with-a-mediator-pipeline/)

-   **CQRS и REST: идеальная пара**
    [*https://lostechies.com/jimmybogard/2016/06/01/cqrs-and-rest-the-perfect-match/*](https://lostechies.com/jimmybogard/2016/06/01/cqrs-and-rest-the-perfect-match/)

-   **Примеры конвейера MediatR**
    [*https://lostechies.com/jimmybogard/2016/10/13/mediatr-pipeline-examples/*](https://lostechies.com/jimmybogard/2016/10/13/mediatr-pipeline-examples/)

-   **Средства тестирования вертикальных срезов для MediatR и ASP.NET Core**
    *<https://lostechies.com/jimmybogard/2016/10/24/vertical-slice-test-fixtures-for-mediatr-and-asp-net-core/> *

-   **Объявление о выпуске расширений MediatR для внедрения зависимостей Майкрософт**
    [*https://lostechies.com/jimmybogard/2016/07/19/mediatr-extensions-for-microsoft-dependency-injection-released/*](https://lostechies.com/jimmybogard/2016/07/19/mediatr-extensions-for-microsoft-dependency-injection-released/)

##### <a name="fluent-validation"></a>Плавная проверка

-   **Джереми Скиннер (Jeremy Skinner). FluentValidation.** Репозиторий GitHub.
    [*https://github.com/JeremySkinner/FluentValidation*](https://github.com/JeremySkinner/FluentValidation)

>[!div class="step-by-step"]
[Назад](microservice-application-layer-web-api-design.md)
[Вперед](../implement-resilient-applications/index.md)
