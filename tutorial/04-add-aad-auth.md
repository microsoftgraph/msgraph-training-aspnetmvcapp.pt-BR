<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="76dc6-101">Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="76dc6-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="76dc6-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar a API do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="76dc6-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph API.</span></span> <span data-ttu-id="76dc6-103">Nesta etapa, você integrará o middleware OWIN e a biblioteca da [biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

1. <span data-ttu-id="76dc6-104">Clique com o botão direito do mouse no projeto do **tutorial de gráfico** no Gerenciador de soluções e selecione **Adicionar > novo item...**. Escolha **arquivo de configuração da Web**, nomeie `PrivateSettings.config` o arquivo e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-104">Right-click the **graph-tutorial** project in Solution Explorer and select **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and select **Add**.</span></span> <span data-ttu-id="76dc6-105">Substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-105">Replace its entire contents with the following code.</span></span>

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    <span data-ttu-id="76dc6-106">Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_PASSWORD_HERE` pelo segredo do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="76dc6-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="76dc6-107">Se o segredo do cliente contiver um`&`e comercial (), substitua-o `&amp;` pelo `PrivateSettings.config`.</span><span class="sxs-lookup"><span data-stu-id="76dc6-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="76dc6-108">Além disso, certifique-se `PORT` de modificar o `ida:RedirectUri` valor para o para corresponder à URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="76dc6-109">Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `PrivateSettings.config` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.</span><span class="sxs-lookup"><span data-stu-id="76dc6-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="76dc6-110">Atualize `Web.config` para carregar este novo arquivo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="76dc6-111">Substitua o `<appSettings>` (linha 7) pelo seguinte</span><span class="sxs-lookup"><span data-stu-id="76dc6-111">Replace the `<appSettings>` (line 7) with the following</span></span>

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="76dc6-112">Implementar logon</span><span class="sxs-lookup"><span data-stu-id="76dc6-112">Implement sign-in</span></span>

<span data-ttu-id="76dc6-113">Inicie inicializando o middleware OWIN para usar a autenticação do Azure AD para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span>

1. <span data-ttu-id="76dc6-114">Clique com o botão direito do mouse na pasta **App_Start** no Gerenciador de soluções e selecione **Adicionar > classe...**. Nomeie o arquivo `Startup.Auth.cs` e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-114">Right-click the **App_Start** folder in Solution Explorer and select **Add > Class...**. Name the file `Startup.Auth.cs` and select **Add**.</span></span> <span data-ttu-id="76dc6-115">Substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-115">Replace the entire contents with the following code.</span></span>

    ```cs
    using Microsoft.Identity.Client;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.Notifications;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Owin;
    using System.Configuration;
    using System.Threading.Tasks;
    using System.Web;

    namespace graph_tutorial
    {
        public partial class Startup
        {
            // Load configuration settings from PrivateSettings.config
            private static string appId = ConfigurationManager.AppSettings["ida:AppId"];
            private static string appSecret = ConfigurationManager.AppSettings["ida:AppSecret"];
            private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];
            private static string graphScopes = ConfigurationManager.AppSettings["ida:AppScopes"];

            public void ConfigureAuth(IAppBuilder app)
            {
                app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

                app.UseCookieAuthentication(new CookieAuthenticationOptions());

                app.UseOpenIdConnectAuthentication(
                  new OpenIdConnectAuthenticationOptions
                  {
                      ClientId = appId,
                      Authority = "https://login.microsoftonline.com/common/v2.0",
                      Scope = $"openid email profile offline_access {graphScopes}",
                      RedirectUri = redirectUri,
                      PostLogoutRedirectUri = redirectUri,
                      TokenValidationParameters = new TokenValidationParameters
                      {
                          // For demo purposes only, see below
                          ValidateIssuer = false

                          // In a real multi-tenant app, you would add logic to determine whether the
                          // issuer was from an authorized tenant
                          //ValidateIssuer = true,
                          //IssuerValidator = (issuer, token, tvp) =>
                          //{
                          //  if (MyCustomTenantValidation(issuer))
                          //  {
                          //    return issuer;
                          //  }
                          //  else
                          //  {
                          //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
                          //  }
                          //}
                      },
                      Notifications = new OpenIdConnectAuthenticationNotifications
                      {
                          AuthenticationFailed = OnAuthenticationFailedAsync,
                          AuthorizationCodeReceived = OnAuthorizationCodeReceivedAsync
                      }
                  }
                );
            }

            private static Task OnAuthenticationFailedAsync(AuthenticationFailedNotification<OpenIdConnectMessage,
              OpenIdConnectAuthenticationOptions> notification)
            {
                notification.HandleResponse();
                string redirect = $"/Home/Error?message={notification.Exception.Message}";
                if (notification.ProtocolMessage != null && !string.IsNullOrEmpty(notification.ProtocolMessage.ErrorDescription))
                {
                    redirect += $"&debug={notification.ProtocolMessage.ErrorDescription}";
                }
                notification.Response.Redirect(redirect);
                return Task.FromResult(0);
            }

            private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
            {
                var idClient = ConfidentialClientApplicationBuilder.Create(appId)
                    .WithRedirectUri(redirectUri)
                    .WithClientSecret(appSecret)
                    .Build();

                string message;
                string debug;

                try
                {
                    string[] scopes = graphScopes.Split(' ');

                    var result = await idClient.AcquireTokenByAuthorizationCode(
                        scopes, notification.Code).ExecuteAsync();

                    message = "Access token retrieved.";
                    debug = result.AccessToken;
                }
                catch (MsalException ex)
                {
                    message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
                    debug = ex.Message;
                }

                notification.HandleResponse();
                notification.Response.Redirect($"/Home/Error?message={message}&debug={debug}");
            }
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="76dc6-116">Este código configura o middleware OWIN com os valores de `PrivateSettings.config` e define dois métodos `OnAuthenticationFailedAsync` de retorno de chamada e. `OnAuthorizationCodeReceivedAsync`</span><span class="sxs-lookup"><span data-stu-id="76dc6-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="76dc6-117">Esses métodos de retorno de chamada serão invocados quando o processo de entrada retornar do Azure.</span><span class="sxs-lookup"><span data-stu-id="76dc6-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

1. <span data-ttu-id="76dc6-118">Agora atualize o `Startup.cs` arquivo para chamar o `ConfigureAuth` método.</span><span class="sxs-lookup"><span data-stu-id="76dc6-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="76dc6-119">Substitua todo o conteúdo do `Startup.cs` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

    ```cs
    using Microsoft.Owin;
    using Owin;

    [assembly: OwinStartup(typeof(graph_tutorial.Startup))]

    namespace graph_tutorial
    {
        public partial class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                ConfigureAuth(app);
            }
        }
    }
    ```

1. <span data-ttu-id="76dc6-120">Adicione uma `Error` ação à `HomeController` classe para transformar os parâmetros `message` de `debug` consulta e em um `Alert` objeto.</span><span class="sxs-lookup"><span data-stu-id="76dc6-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="76dc6-121">Abra `Controllers/HomeController.cs` e adicione a função a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. <span data-ttu-id="76dc6-122">Adicione um controlador para lidar com a entrada.</span><span class="sxs-lookup"><span data-stu-id="76dc6-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="76dc6-123">Clique com o botão direito do mouse na pasta **controladores** no Gerenciador de soluções e selecione **Adicionar > controlador...**. Escolha **controlador MVC 5-vazio** e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-123">Right-click the **Controllers** folder in Solution Explorer and select **Add > Controller...**. Choose **MVC 5 Controller - Empty** and select **Add**.</span></span> <span data-ttu-id="76dc6-124">Nomeie o controlador `AccountController` e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-124">Name the controller `AccountController` and select **Add**.</span></span> <span data-ttu-id="76dc6-125">Substitua todo o conteúdo do arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-125">Replace the entire contents of the file with the following code.</span></span>

    ```cs
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using System.Security.Claims;
    using System.Web;
    using System.Web.Mvc;

    namespace graph_tutorial.Controllers
    {
        public class AccountController : Controller
        {
            public void SignIn()
            {
                if (!Request.IsAuthenticated)
                {
                    // Signal OWIN to send an authorization request to Azure
                    Request.GetOwinContext().Authentication.Challenge(
                        new AuthenticationProperties { RedirectUri = "/" },
                        OpenIdConnectAuthenticationDefaults.AuthenticationType);
                }
            }

            public ActionResult SignOut()
            {
                if (Request.IsAuthenticated)
                {
                    Request.GetOwinContext().Authentication.SignOut(
                        CookieAuthenticationDefaults.AuthenticationType);
                }

                return RedirectToAction("Index", "Home");
            }
        }
    }
    ```

    <span data-ttu-id="76dc6-126">Isso define uma `SignIn` ação `SignOut` e.</span><span class="sxs-lookup"><span data-stu-id="76dc6-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="76dc6-127">A `SignIn` ação verifica se a solicitação já está autenticada.</span><span class="sxs-lookup"><span data-stu-id="76dc6-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="76dc6-128">Caso contrário, ele invocará o middleware OWIN para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="76dc6-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="76dc6-129">A `SignOut` ação invoca o middleware OWIN para sair.</span><span class="sxs-lookup"><span data-stu-id="76dc6-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

1. <span data-ttu-id="76dc6-130">Salve suas alterações e inicie o projeto.</span><span class="sxs-lookup"><span data-stu-id="76dc6-130">Save your changes and start the project.</span></span> <span data-ttu-id="76dc6-131">Clique no botão entrar e você deverá ser redirecionado para `https://login.microsoftonline.com`o.</span><span class="sxs-lookup"><span data-stu-id="76dc6-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="76dc6-132">Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas.</span><span class="sxs-lookup"><span data-stu-id="76dc6-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="76dc6-133">O navegador redireciona para o aplicativo, mostrando o token.</span><span class="sxs-lookup"><span data-stu-id="76dc6-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="76dc6-134">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="76dc6-134">Get user details</span></span>

<span data-ttu-id="76dc6-135">Depois que o usuário estiver conectado, você pode obter as informações do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="76dc6-135">Once the user is logged in, you can get their information from Microsoft Graph.</span></span>

1. <span data-ttu-id="76dc6-136">Clique com o botão direito do mouse na pasta do **tutorial do gráfico** no Gerenciador de soluções e selecione **Adicionar > nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-136">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="76dc6-137">Nomeie a pasta `Helpers`.</span><span class="sxs-lookup"><span data-stu-id="76dc6-137">Name the folder `Helpers`.</span></span>

1. <span data-ttu-id="76dc6-138">Clique com o botão direito do mouse na nova pasta e selecione **adicionar > classe...**. Nomeie o arquivo `GraphHelper.cs` e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-138">Right click this new folder and select **Add > Class...**. Name the file `GraphHelper.cs` and select **Add**.</span></span> <span data-ttu-id="76dc6-139">Substitua o conteúdo desse arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-139">Replace the contents of this file with the following code.</span></span>

    ```cs
    using Microsoft.Graph;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace graph_tutorial.Helpers
    {
        public static class GraphHelper
        {
            public static async Task<User> GetUserDetailsAsync(string accessToken)
            {
                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(
                        async (requestMessage) =>
                        {
                            requestMessage.Headers.Authorization =
                                new AuthenticationHeaderValue("Bearer", accessToken);
                        }));

                return await graphClient.Me.Request().GetAsync();
            }
        }
    }
    ```

    <span data-ttu-id="76dc6-140">Isso implementa a `GetUserDetails` função, que usa o SDK do Microsoft Graph para chamar `/me` o ponto de extremidade e retornar o resultado.</span><span class="sxs-lookup"><span data-stu-id="76dc6-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="76dc6-141">Atualize o `OnAuthorizationCodeReceivedAsync` método no `App_Start/Startup.Auth.cs` para chamar essa função.</span><span class="sxs-lookup"><span data-stu-id="76dc6-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="76dc6-142">Adicione a seguinte `using` instrução à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-142">Add the following `using` statement to the top of the file.</span></span>

    ```cs
    using graph_tutorial.Helpers;
    ```

1. <span data-ttu-id="76dc6-143">Substitua o bloco `try` existente `OnAuthorizationCodeReceivedAsync` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

    ```cs
    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCode(
            scopes, notification.Code).ExecuteAsync();

        var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

        string email = string.IsNullOrEmpty(userDetails.Mail) ?
            userDetails.UserPrincipalName : userDetails.Mail;

        message = "User info retrieved.";
        debug = $"User: {userDetails.DisplayName}, Email: {email}";
    }
    ```

1. <span data-ttu-id="76dc6-144">Salve suas alterações e inicie o aplicativo, após entrar, você deve ver o nome do usuário e o endereço de email em vez do token de acesso.</span><span class="sxs-lookup"><span data-stu-id="76dc6-144">Save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="76dc6-145">Armazenar tokens</span><span class="sxs-lookup"><span data-stu-id="76dc6-145">Storing the tokens</span></span>

<span data-ttu-id="76dc6-146">Agora que você pode obter tokens, é hora de implementar uma maneira de armazená-los no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="76dc6-147">Como este é um aplicativo de exemplo, você usará a sessão para armazenar os tokens.</span><span class="sxs-lookup"><span data-stu-id="76dc6-147">Since this is a sample app, you will use the session to store the tokens.</span></span> <span data-ttu-id="76dc6-148">Um aplicativo real usaria uma solução de armazenamento segura mais confiável, como um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="76dc6-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span> <span data-ttu-id="76dc6-149">Nesta seção, você irá:</span><span class="sxs-lookup"><span data-stu-id="76dc6-149">In this section, you will:</span></span>

- <span data-ttu-id="76dc6-150">Implemente uma classe de repositório de token para serializar e armazenar o cache de token do MSAL e os detalhes do usuário na sessão do usuário.</span><span class="sxs-lookup"><span data-stu-id="76dc6-150">Implement a token store class to serialize and store the MSAL token cache and the user's details in the user session.</span></span>
- <span data-ttu-id="76dc6-151">Atualize o código de autenticação para usar a classe de repositório de tokens.</span><span class="sxs-lookup"><span data-stu-id="76dc6-151">Update the authentication code to use the token store class.</span></span>
- <span data-ttu-id="76dc6-152">Atualize a classe do controlador base para expor os detalhes do usuário armazenados a todos os modos de exibição no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-152">Update the base controller class to expose the stored user details to all views in the application.</span></span>

1. <span data-ttu-id="76dc6-153">Clique com o botão direito do mouse na pasta do **tutorial do gráfico** no Gerenciador de soluções e selecione **Adicionar > nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-153">Right-click the **graph-tutorial** folder in Solution Explorer, and select **Add > New Folder**.</span></span> <span data-ttu-id="76dc6-154">Nomeie a pasta `TokenStorage`.</span><span class="sxs-lookup"><span data-stu-id="76dc6-154">Name the folder `TokenStorage`.</span></span>

1. <span data-ttu-id="76dc6-155">Clique com o botão direito do mouse na nova pasta e selecione **adicionar > classe...**. Nomeie o arquivo `SessionTokenStore.cs` e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="76dc6-155">Right click this new folder and select **Add > Class...**. Name the file `SessionTokenStore.cs` and select **Add**.</span></span> <span data-ttu-id="76dc6-156">Substitua o conteúdo desse arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-156">Replace the contents of this file with the following code.</span></span>

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT license.

    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using System.Security.Claims;
    using System.Threading;
    using System.Web;

    namespace graph_tutorial.TokenStorage
    {
        // Simple class to serialize into the session
        public class CachedUser
        {
            public string DisplayName { get; set; }
            public string Email { get; set; }
            public string Avatar { get; set; }
        }

        public class SessionTokenStore
        {
            private static readonly ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);

            private HttpContext httpContext = null;
            private string tokenCacheKey = string.Empty;
            private string userCacheKey = string.Empty;

            public SessionTokenStore(ITokenCache tokenCache, HttpContext context, ClaimsPrincipal user)
            {
                httpContext = context;

                if (tokenCache != null)
                {
                    tokenCache.SetBeforeAccess(BeforeAccessNotification);
                    tokenCache.SetAfterAccess(AfterAccessNotification);
                }

                var userId = GetUsersUniqueId(user);
                tokenCacheKey = $"{userId}_TokenCache";
                userCacheKey = $"{userId}_UserCache";
            }

            public bool HasData()
            {
                return (httpContext.Session[tokenCacheKey] != null &&
                    ((byte[])httpContext.Session[tokenCacheKey]).Length > 0);
            }

            public void Clear()
            {
                sessionLock.EnterWriteLock();

                try
                {
                    httpContext.Session.Remove(tokenCacheKey);
                }
                finally
                {
                    sessionLock.ExitWriteLock();
                }
            }

            private void BeforeAccessNotification(TokenCacheNotificationArgs args)
            {
                sessionLock.EnterReadLock();

                try
                {
                    // Load the cache from the session
                    args.TokenCache.DeserializeMsalV3((byte[])httpContext.Session[tokenCacheKey]);
                }
                finally
                {
                    sessionLock.ExitReadLock();
                }
            }

            private void AfterAccessNotification(TokenCacheNotificationArgs args)
            {
                if (args.HasStateChanged)
                {
                    sessionLock.EnterWriteLock();

                    try
                    {
                        // Store the serialized cache in the session
                        httpContext.Session[tokenCacheKey] = args.TokenCache.SerializeMsalV3();
                    }
                    finally
                    {
                        sessionLock.ExitWriteLock();
                    }
                }
            }

            public void SaveUserDetails(CachedUser user)
            {

                sessionLock.EnterWriteLock();
                httpContext.Session[userCacheKey] = JsonConvert.SerializeObject(user);
                sessionLock.ExitWriteLock();
            }

            public CachedUser GetUserDetails()
            {
                sessionLock.EnterReadLock();
                var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[userCacheKey]);
                sessionLock.ExitReadLock();
                return cachedUser;
            }

            private string GetUsersUniqueId(ClaimsPrincipal user)
            {
                // Combine the user's object ID with their tenant ID

                if (user != null)
                {
                    var userObjectId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("oid").Value;

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value ??
                        user.FindFirst("tid").Value;

                    if (!string.IsNullOrEmpty(userObjectId) && !string.IsNullOrEmpty(userTenantId))
                    {
                        return $"{userObjectId}.{userTenantId}";
                    }
                }

                return null;
            }
        }
    }
    ```

1. <span data-ttu-id="76dc6-157">Adicione a seguinte `using` instrução à parte superior do `App_Start/Startup.Auth.cs` arquivo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-157">Add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="76dc6-158">Substitua a função `OnAuthorizationCodeReceivedAsync` existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="76dc6-158">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

    ```cs
    private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
    {
        var idClient = ConfidentialClientApplicationBuilder.Create(appId)
            .WithRedirectUri(redirectUri)
            .WithClientSecret(appSecret)
            .Build();

        var signedInUser = new ClaimsPrincipal(notification.AuthenticationTicket.Identity);
        var tokenStore = new SessionTokenStore(idClient.UserTokenCache, HttpContext.Current, signedInUser);

        try
        {
            string[] scopes = graphScopes.Split(' ');

            var result = await idClient.AcquireTokenByAuthorizationCode(
                scopes, notification.Code).ExecuteAsync();

            var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

            var cachedUser = new CachedUser()
            {
                DisplayName = userDetails.DisplayName,
                Email = string.IsNullOrEmpty(userDetails.Mail) ?
                userDetails.UserPrincipalName : userDetails.Mail,
                Avatar = string.Empty
            };

            tokenStore.SaveUserDetails(cachedUser);
        }
        catch (MsalException ex)
        {
            string message = "AcquireTokenByAuthorizationCodeAsync threw an exception";
            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
        }
        catch (Microsoft.Graph.ServiceException ex)
        {
            string message = "GetUserDetailsAsync threw an exception";
            notification.HandleResponse();
            notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="76dc6-159">As alterações nessa nova versão do `OnAuthorizationCodeReceivedAsync` fazem o seguinte:</span><span class="sxs-lookup"><span data-stu-id="76dc6-159">The changes in this new version of `OnAuthorizationCodeReceivedAsync` do the following:</span></span>
    >
    > - <span data-ttu-id="76dc6-160">O código agora encapsula o `ConfidentialClientApplication`cache de token de usuário padrão com a `SessionTokenStore` classe.</span><span class="sxs-lookup"><span data-stu-id="76dc6-160">The code now wraps the `ConfidentialClientApplication`'s default user token cache with the `SessionTokenStore` class.</span></span> <span data-ttu-id="76dc6-161">A biblioteca MSAL tratará a lógica de armazenamento dos tokens e a atualização quando necessário.</span><span class="sxs-lookup"><span data-stu-id="76dc6-161">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
    > - <span data-ttu-id="76dc6-162">O código agora passa os detalhes do usuário obtidos do Microsoft Graph para `SessionTokenStore` o objeto a ser armazenado na sessão.</span><span class="sxs-lookup"><span data-stu-id="76dc6-162">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
    > - <span data-ttu-id="76dc6-163">No caso de sucesso, o código não é mais Redirecionado, apenas retorna.</span><span class="sxs-lookup"><span data-stu-id="76dc6-163">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="76dc6-164">Isso permite que o middleware OWIN conclua o processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="76dc6-164">This allows the OWIN middleware to complete the authentication process.</span></span>

1. <span data-ttu-id="76dc6-165">Atualize a `SignOut` ação para limpar o repositório de token antes de sair. Adicione a seguinte `using` instrução à parte superior de `Controllers/AccountController.cs`.</span><span class="sxs-lookup"><span data-stu-id="76dc6-165">Update the `SignOut` action to clear the token store before signing out. Add the following `using` statement to the top of `Controllers/AccountController.cs`.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. <span data-ttu-id="76dc6-166">Substitua a função `SignOut` existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="76dc6-166">Replace the existing `SignOut` function with the following.</span></span>

    ```cs
    public ActionResult SignOut()
    {
        if (Request.IsAuthenticated)
        {
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

            tokenStore.Clear();

            Request.GetOwinContext().Authentication.SignOut(
                CookieAuthenticationDefaults.AuthenticationType);
        }

        return RedirectToAction("Index", "Home");
    }
    ```

1. <span data-ttu-id="76dc6-167">Abra `Controllers/BaseController.cs` e adicione as seguintes `using` instruções à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-167">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. <span data-ttu-id="76dc6-168">Adicione a função a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-168">Add the following function.</span></span>

    ```cs
    protected override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        if (Request.IsAuthenticated)
        {
            // Get the user's token cache
            var tokenStore = new SessionTokenStore(null,
                System.Web.HttpContext.Current, ClaimsPrincipal.Current);

            if (tokenStore.HasData())
            {
                // Add the user to the view bag
                ViewBag.User = tokenStore.GetUserDetails();
            }
            else
            {
                // The session has lost data. This happens often
                // when debugging. Log out so the user can log back in
                Request.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
                filterContext.Result = RedirectToAction("Index", "Home");
            }
        }

        base.OnActionExecuting(filterContext);
    }
    ```

1. <span data-ttu-id="76dc6-169">Inicie o servidor e percorra o processo de entrada.</span><span class="sxs-lookup"><span data-stu-id="76dc6-169">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="76dc6-170">Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.</span><span class="sxs-lookup"><span data-stu-id="76dc6-170">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

1. <span data-ttu-id="76dc6-172">Clique no avatar do usuário no canto superior direito para acessar o \*\*\*\* link sair.</span><span class="sxs-lookup"><span data-stu-id="76dc6-172">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="76dc6-173">Clicar \*\*\*\* em sair redefine a sessão e retorna à Home Page.</span><span class="sxs-lookup"><span data-stu-id="76dc6-173">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="76dc6-175">Atualizando tokens</span><span class="sxs-lookup"><span data-stu-id="76dc6-175">Refreshing tokens</span></span>

<span data-ttu-id="76dc6-176">Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="76dc6-176">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="76dc6-177">Este é o token que permite ao aplicativo acessar o Microsoft Graph em nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="76dc6-177">This is the token that allows the app to access Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="76dc6-178">No entanto, esse token é de vida curta.</span><span class="sxs-lookup"><span data-stu-id="76dc6-178">However, this token is short-lived.</span></span> <span data-ttu-id="76dc6-179">O token expira uma hora após sua emissão.</span><span class="sxs-lookup"><span data-stu-id="76dc6-179">The token expires an hour after it is issued.</span></span> <span data-ttu-id="76dc6-180">É onde o token de atualização se torna útil.</span><span class="sxs-lookup"><span data-stu-id="76dc6-180">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="76dc6-181">O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.</span><span class="sxs-lookup"><span data-stu-id="76dc6-181">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="76dc6-182">Como o aplicativo está usando a biblioteca MSAL e serializando `TokenCache` o objeto, você não precisa implementar uma lógica de atualização de token.</span><span class="sxs-lookup"><span data-stu-id="76dc6-182">Because the app is using the MSAL library and serializing the `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="76dc6-183">O `ConfidentialClientApplication.AcquireTokenSilentAsync` método faz toda a lógica para você.</span><span class="sxs-lookup"><span data-stu-id="76dc6-183">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="76dc6-184">Primeiro ele verifica o token em cache e, se ele não tiver expirado, ele o retornará.</span><span class="sxs-lookup"><span data-stu-id="76dc6-184">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="76dc6-185">Se ele tiver expirado, ele usará o token de atualização em cache para obter um novo.</span><span class="sxs-lookup"><span data-stu-id="76dc6-185">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="76dc6-186">Você usará esse método no módulo a seguir.</span><span class="sxs-lookup"><span data-stu-id="76dc6-186">You'll use this method in the following module.</span></span>
