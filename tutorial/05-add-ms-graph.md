<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0eda9-101">Nesta demonstração, você incorporará o Microsoft Graph no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="0eda9-101">In this demo you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="0eda9-102">Para este aplicativo, você usará a [biblioteca de cliente do Microsoft Graph para .net](https://github.com/microsoftgraph/msgraph-sdk-dotnet) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="0eda9-102">For this application, you will use the [Microsoft Graph Client Library for .NET](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="0eda9-103">Obter eventos de calendário do Outlook</span><span class="sxs-lookup"><span data-stu-id="0eda9-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="0eda9-104">Comece estendendo `GraphHelper` a classe criada no último módulo.</span><span class="sxs-lookup"><span data-stu-id="0eda9-104">Start by extending the `GraphHelper` class you created in the last module.</span></span>

1. <span data-ttu-id="0eda9-105">Adicione as seguintes `using` instruções à parte superior do `Helpers/GraphHelper.cs` arquivo.</span><span class="sxs-lookup"><span data-stu-id="0eda9-105">Add the following `using` statements to the top of the `Helpers/GraphHelper.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using Microsoft.Identity.Client;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Claims;
    using System.Web;
    ```

1. <span data-ttu-id="0eda9-106">Adicione o código a seguir à `GraphHelper` classe.</span><span class="sxs-lookup"><span data-stu-id="0eda9-106">Add the following code to the `GraphHelper` class.</span></span>

    ```cs
    // Load configuration settings from PrivateSettings.config
    private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
    private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
    private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

    public static async Task<IEnumerable<Event>> GetEventsAsync()
    {
        var graphClient = GetAuthenticatedClient();

        var events = await graphClient.Me.Events.Request()
            .Select("subject,organizer,start,end")
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        return events.CurrentPage;
    }

    private static GraphServiceClient GetAuthenticatedClient()
    {
        return new GraphServiceClient(
            new DelegateAuthenticationProvider(
                async (requestMessage) =>
                {
                    var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                        .WithRedirectUri(redirectUri)
                        .WithClientSecret(appSecret)
                        .Build();

                    string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
                    var tokenStore = new SessionTokenStore(signedInUserId, HttpContext.Current);
                    tokenStore.Initialize(idClient.UserTokenCache);

                    var accounts = await idClient.GetAccountsAsync();

                    // By calling this here, the token can be refreshed
                    // if it's expired right before the Graph call is made
                    var scopes = graphScopes.Split(' ');
                    var result = await idClient.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                        .ExecuteAsync();

                    requestMessage.Headers.Authorization =
                        new AuthenticationHeaderValue("Bearer", result.AccessToken);
                }));
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="0eda9-107">Considere o que esse código está fazendo.</span><span class="sxs-lookup"><span data-stu-id="0eda9-107">Consider what this code is doing.</span></span>
    >
    > - <span data-ttu-id="0eda9-108">A `GetAuthenticatedClient` função inicializa a `GraphServiceClient` com um provedor de autenticação que `AcquireTokenSilent`chama.</span><span class="sxs-lookup"><span data-stu-id="0eda9-108">The `GetAuthenticatedClient` function initializes a `GraphServiceClient` with an authentication provider that calls `AcquireTokenSilent`.</span></span>
    > - <span data-ttu-id="0eda9-109">Na `GetEventsAsync` função:</span><span class="sxs-lookup"><span data-stu-id="0eda9-109">In the `GetEventsAsync` function:</span></span>
    >   - <span data-ttu-id="0eda9-110">A URL que será chamada é `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="0eda9-110">The URL that will be called is `/v1.0/me/events`.</span></span>
    >   - <span data-ttu-id="0eda9-111">A `Select` função limita os campos retornados para cada evento para apenas aqueles que o modo de exibição realmente usará.</span><span class="sxs-lookup"><span data-stu-id="0eda9-111">The `Select` function limits the fields returned for each events to just those the view will actually use.</span></span>
    >   - <span data-ttu-id="0eda9-112">A `OrderBy` função classifica os resultados pela data e hora em que foram criados, com o item mais recente em primeiro lugar.</span><span class="sxs-lookup"><span data-stu-id="0eda9-112">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="0eda9-113">Criar um controlador para os modos de exibição de calendário.</span><span class="sxs-lookup"><span data-stu-id="0eda9-113">Create a controller for the calendar views.</span></span> <span data-ttu-id="0eda9-114">Clique com o botão direito do mouse na pasta **controladores** no Gerenciador de soluções e selecione **Adicionar > controlador...**. Escolha **controlador MVC 5-vazio** e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0eda9-114">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="0eda9-115">Nomeie o controlador `CalendarController` e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0eda9-115">Name the controller `CalendarController` and select **Add**.</span></span> <span data-ttu-id="0eda9-116">Substitua todo o conteúdo do novo arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0eda9-116">Replace the entire contents of the new file with the following code.</span></span>

    ```cs
    using System;
    using graph_tutorial.Helpers;
    using System.Threading.Tasks;
    using System.Web.Mvc;

    namespace graph_tutorial.Controllers
    {
        public class CalendarController : BaseController
        {
            // GET: Calendar
            [Authorize]
            public async Task<ActionResult> Index()
            {
                var events = await GraphHelper.GetEventsAsync();

                // Change start and end dates from UTC to local time
                foreach (var ev in events)
                {
                    ev.Start.DateTime = DateTime.Parse(ev.Start.DateTime).ToLocalTime().ToString();
                    ev.Start.TimeZone = TimeZoneInfo.Local.Id;
                    ev.End.DateTime = DateTime.Parse(ev.End.DateTime).ToLocalTime().ToString();
                    ev.End.TimeZone = TimeZoneInfo.Local.Id;
                }

                return Json(events, JsonRequestBehavior.AllowGet);
            }
        }
    }
    ```

1. <span data-ttu-id="0eda9-117">Inicie o aplicativo, entre e clique no link **calendário** na barra de navegação.</span><span class="sxs-lookup"><span data-stu-id="0eda9-117">Start the app, sign in, and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="0eda9-118">Se tudo funcionar, você deverá ver um despejo JSON de eventos no calendário do usuário.</span><span class="sxs-lookup"><span data-stu-id="0eda9-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="0eda9-119">Exibir os resultados</span><span class="sxs-lookup"><span data-stu-id="0eda9-119">Display the results</span></span>

<span data-ttu-id="0eda9-120">Agora você pode adicionar um modo de exibição para exibir os resultados de forma mais amigável.</span><span class="sxs-lookup"><span data-stu-id="0eda9-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="0eda9-121">No Gerenciador de soluções, clique com o botão direito do mouse na pasta exibir **/calendário** e selecione **Adicionar > modo de exibição...**. Nomeie o modo `Index` de exibição e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="0eda9-121">In Solution Explorer, right-click the **Views/Calendar** folder and select **Add > View...**. Name the view `Index` and select **Add**.</span></span> <span data-ttu-id="0eda9-122">Substitua todo o conteúdo do novo arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0eda9-122">Replace the entire contents of the new file with the following code.</span></span>

    ```html
    @model IEnumerable<Microsoft.Graph.Event>

    @{
        ViewBag.Current = "Calendar";
    }

    <h1>Calendar</h1>
    <table class="table">
        <thead>
            <tr>
                <th scope="col">Organizer</th>
                <th scope="col">Subject</th>
                <th scope="col">Start</th>
                <th scope="col">End</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model)
            {
                <tr>
                    <td>@item.Organizer.EmailAddress.Name</td>
                    <td>@item.Subject</td>
                    <td>@Convert.ToDateTime(item.Start.DateTime).ToString("M/d/yy h:mm tt")</td>
                    <td>@Convert.ToDateTime(item.End.DateTime).ToString("M/d/yy h:mm tt")</td>
                </tr>
            }
        </tbody>
    </table>
    ```

    <span data-ttu-id="0eda9-123">Isso executará um loop através de uma coleção de eventos e adicionará uma linha de tabela para cada um.</span><span class="sxs-lookup"><span data-stu-id="0eda9-123">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="0eda9-124">Remova a `return Json(events, JsonRequestBehavior.AllowGet);` linha da `Index` função no `Controllers/CalendarController.cs`e substitua-a pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="0eda9-124">Remove the `return Json(events, JsonRequestBehavior.AllowGet);` line from the `Index` function in `Controllers/CalendarController.cs`, and replace it with the following code.</span></span>

    ```cs
    return View(events);
    ```

1. <span data-ttu-id="0eda9-125">Inicie o aplicativo, entre e clique no link do **calendário** .</span><span class="sxs-lookup"><span data-stu-id="0eda9-125">Start the app, sign in, and click the **Calendar** link.</span></span> <span data-ttu-id="0eda9-126">O aplicativo agora deve renderizar uma tabela de eventos.</span><span class="sxs-lookup"><span data-stu-id="0eda9-126">The app should now render a table of events.</span></span>

    ![Uma captura de tela da tabela de eventos](./images/add-msgraph-01.png)
