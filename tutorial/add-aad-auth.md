<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="02316-101">Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD.</span><span class="sxs-lookup"><span data-stu-id="02316-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="02316-102">Isso é necessário para obter o token de acesso OAuth necessário para chamar o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="02316-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="02316-103">Nesta etapa, você integrará o middleware OWIN e a biblioteca da [biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02316-103">In this step you will integrate the OWIN middleware and the [Microsoft Authentication Library](https://www.nuget.org/packages/Microsoft.Identity.Client/) library into the application.</span></span>

<span data-ttu-id="02316-104">Clique com o botão direito do mouse no projeto do **tutorial de gráfico** no Gerenciador de soluções e escolha **Adicionar > novo item...**. Escolha **arquivo de configuração da Web**, nomeie `PrivateSettings.config` o arquivo e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02316-104">Right-click the **graph-tutorial** project in Solution Explorer and choose **Add > New Item...**. Choose **Web Configuration File**, name the file `PrivateSettings.config` and choose **Add**.</span></span> <span data-ttu-id="02316-105">Substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-105">Replace its entire contents with the following code.</span></span>

```xml
<appSettings>
    <add key="ida:AppID" value="YOUR APP ID" />
    <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
    <add key="ida:RedirectUri" value="http://localhost:PORT/" />
    <add key="ida:AppScopes" value="User.Read Calendars.Read" />
</appSettings>
```

<span data-ttu-id="02316-106">Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_PASSWORD_HERE` pelo segredo do cliente gerado.</span><span class="sxs-lookup"><span data-stu-id="02316-106">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the client secret you generated.</span></span> <span data-ttu-id="02316-107">Se o segredo do cliente contiver um`&`e comercial (), substitua-o `&amp;` pelo `PrivateSettings.config`.</span><span class="sxs-lookup"><span data-stu-id="02316-107">If your client secret contains any ampersands (`&`), be sure to replace them with `&amp;` in `PrivateSettings.config`.</span></span> <span data-ttu-id="02316-108">Além disso, certifique-se `PORT` de modificar o `ida:RedirectUri` valor para o para corresponder à URL do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02316-108">Also be sure to modify the `PORT` value for the `ida:RedirectUri` to match your application's URL.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="02316-109">Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `PrivateSettings.config` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.</span><span class="sxs-lookup"><span data-stu-id="02316-109">If you're using source control such as git, now would be a good time to exclude the `PrivateSettings.config` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="02316-110">Atualize `Web.config` para carregar este novo arquivo.</span><span class="sxs-lookup"><span data-stu-id="02316-110">Update `Web.config` to load this new file.</span></span> <span data-ttu-id="02316-111">Substitua o `<appSettings>` (linha 7) pelo seguinte</span><span class="sxs-lookup"><span data-stu-id="02316-111">Replace the `<appSettings>` (line 7) with the following</span></span>

```xml
<appSettings file="PrivateSettings.config">
```

## <a name="implement-sign-in"></a><span data-ttu-id="02316-112">Implementar logon</span><span class="sxs-lookup"><span data-stu-id="02316-112">Implement sign-in</span></span>

<span data-ttu-id="02316-113">Inicie inicializando o middleware OWIN para usar a autenticação do Azure AD para o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02316-113">Start by initializing the OWIN middleware to use Azure AD authentication for the app.</span></span> <span data-ttu-id="02316-114">Clique com o botão direito do mouse na pasta **App_Start** no Gerenciador de soluções e escolha **Adicionar classe >...**. Nomeie o arquivo `Startup.Auth.cs` e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02316-114">Right-click the **App_Start** folder in Solution Explorer and choose **Add > Class...**. Name the file `Startup.Auth.cs` and choose **Add**.</span></span> <span data-ttu-id="02316-115">Substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-115">Replace the entire contents with the following code.</span></span>

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
            var idClient = new ConfidentialClientApplication(
                appId, redirectUri, new ClientCredential(appSecret), null, null);

            string message;
            string debug;

            try
            {
                string[] scopes = graphScopes.Split(' ');

                var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
                    notification.Code, scopes);

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

<span data-ttu-id="02316-116">Este código configura o middleware OWIN com os valores de `PrivateSettings.config` e define dois métodos `OnAuthenticationFailedAsync` de retorno de chamada e. `OnAuthorizationCodeReceivedAsync`</span><span class="sxs-lookup"><span data-stu-id="02316-116">This code configures the OWIN middleware with the values from `PrivateSettings.config` and defines two callback methods, `OnAuthenticationFailedAsync` and `OnAuthorizationCodeReceivedAsync`.</span></span> <span data-ttu-id="02316-117">Esses métodos de retorno de chamada serão invocados quando o processo de entrada retornar do Azure.</span><span class="sxs-lookup"><span data-stu-id="02316-117">These callback methods will be invoked when the sign-in process returns from Azure.</span></span>

<span data-ttu-id="02316-118">Agora atualize o `Startup.cs` arquivo para chamar o `ConfigureAuth` método.</span><span class="sxs-lookup"><span data-stu-id="02316-118">Now update the `Startup.cs` file to call the `ConfigureAuth` method.</span></span> <span data-ttu-id="02316-119">Substitua todo o conteúdo do `Startup.cs` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-119">Replace the entire contents of `Startup.cs` with the following code.</span></span>

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

<span data-ttu-id="02316-120">Adicione uma `Error` ação à `HomeController` classe para transformar os parâmetros `message` de `debug` consulta e em um `Alert` objeto.</span><span class="sxs-lookup"><span data-stu-id="02316-120">Add an `Error` action to the `HomeController` class to transform the `message` and `debug` query parameters into an `Alert` object.</span></span> <span data-ttu-id="02316-121">Abra `Controllers/HomeController.cs` e adicione a função a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-121">Open `Controllers/HomeController.cs` and add the following function.</span></span>

```cs
public ActionResult Error(string message, string debug)
{
    Flash(message, debug);
    return RedirectToAction("Index");
}
```

<span data-ttu-id="02316-122">Adicione um controlador para lidar com a entrada.</span><span class="sxs-lookup"><span data-stu-id="02316-122">Add a controller to handle sign-in.</span></span> <span data-ttu-id="02316-123">Clique com o botão direito do mouse na pasta **controladores** no Gerenciador de soluções e escolha **Adicionar controlador de >..**.. Escolha **controlador MVC 5-vazio** e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02316-123">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="02316-124">Nomeie o controlador `AccountController` e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02316-124">Name the controller `AccountController` and choose **Add**.</span></span> <span data-ttu-id="02316-125">Substitua todo o conteúdo do arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-125">Replace the entire contents of the file with the following code.</span></span>

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

<span data-ttu-id="02316-126">Isso define uma `SignIn` ação `SignOut` e.</span><span class="sxs-lookup"><span data-stu-id="02316-126">This defines a `SignIn` and `SignOut` action.</span></span> <span data-ttu-id="02316-127">A `SignIn` ação verifica se a solicitação já está autenticada.</span><span class="sxs-lookup"><span data-stu-id="02316-127">The `SignIn` action checks if the request is already authenticated.</span></span> <span data-ttu-id="02316-128">Caso contrário, ele invocará o middleware OWIN para autenticar o usuário.</span><span class="sxs-lookup"><span data-stu-id="02316-128">If not, it invokes the OWIN middleware to authenticate the user.</span></span> <span data-ttu-id="02316-129">A `SignOut` ação invoca o middleware OWIN para sair.</span><span class="sxs-lookup"><span data-stu-id="02316-129">The `SignOut` action invokes the OWIN middleware to sign out.</span></span>

<span data-ttu-id="02316-130">Salve suas alterações e inicie o projeto.</span><span class="sxs-lookup"><span data-stu-id="02316-130">Save your changes and start the project.</span></span> <span data-ttu-id="02316-131">Clique no botão entrar e você deverá ser redirecionado para `https://login.microsoftonline.com`o.</span><span class="sxs-lookup"><span data-stu-id="02316-131">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="02316-132">Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas.</span><span class="sxs-lookup"><span data-stu-id="02316-132">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="02316-133">O navegador redireciona para o aplicativo, mostrando o token.</span><span class="sxs-lookup"><span data-stu-id="02316-133">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="02316-134">Obter detalhes do usuário</span><span class="sxs-lookup"><span data-stu-id="02316-134">Get user details</span></span>

<span data-ttu-id="02316-135">Comece criando um novo arquivo para conter todas as chamadas do Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="02316-135">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="02316-136">Clique com o botão direito do mouse na pasta do **tutorial do gráfico** no Gerenciador de soluções e escolha **Adicionar > nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="02316-136">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="02316-137">Nomeie a pasta `Helpers`.</span><span class="sxs-lookup"><span data-stu-id="02316-137">Name the folder `Helpers`.</span></span> <span data-ttu-id="02316-138">Clique com o botão direito do mouse na nova pasta e escolha **Adicionar classe >...**. Nomeie o arquivo `GraphHelper.cs` e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02316-138">Right click this new folder and choose **Add > Class...**. Name the file `GraphHelper.cs` and choose **Add**.</span></span> <span data-ttu-id="02316-139">Substitua o conteúdo desse arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-139">Replace the contents of this file with the following code.</span></span>

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

<span data-ttu-id="02316-140">Isso implementa a `GetUserDetails` função, que usa o SDK do Microsoft Graph para chamar `/me` o ponto de extremidade e retornar o resultado.</span><span class="sxs-lookup"><span data-stu-id="02316-140">This implements the `GetUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="02316-141">Atualize o `OnAuthorizationCodeReceivedAsync` método no `App_Start/Startup.Auth.cs` para chamar essa função.</span><span class="sxs-lookup"><span data-stu-id="02316-141">Update the `OnAuthorizationCodeReceivedAsync` method in `App_Start/Startup.Auth.cs` to call this function.</span></span> <span data-ttu-id="02316-142">Primeiro, adicione a seguinte `using` instrução à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="02316-142">First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.Helpers;
```

<span data-ttu-id="02316-143">Substitua o bloco `try` existente `OnAuthorizationCodeReceivedAsync` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-143">Replace the existing `try` block in `OnAuthorizationCodeReceivedAsync` with the following code.</span></span>

```cs
try
{
    string[] scopes = graphScopes.Split(' ');

    var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
        notification.Code, scopes);

    var userDetails = await GraphHelper.GetUserDetailsAsync(result.AccessToken);

    string email = string.IsNullOrEmpty(userDetails.Mail) ?
        userDetails.UserPrincipalName : userDetails.Mail;

    message = "User info retrieved.";
    debug = $"User: {userDetails.DisplayName}, Email: {email}";
}
```

<span data-ttu-id="02316-144">Agora, se você salvar as alterações e iniciar o aplicativo, depois de entrar, você deverá ver o nome do usuário e o endereço de email em vez do token de acesso.</span><span class="sxs-lookup"><span data-stu-id="02316-144">Now if you save your changes and start the app, after sign-in you should see the user's name and email address instead of the access token.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="02316-145">Armazenar tokens</span><span class="sxs-lookup"><span data-stu-id="02316-145">Storing the tokens</span></span>

<span data-ttu-id="02316-146">Agora que você pode obter tokens, é hora de implementar uma maneira de armazená-los no aplicativo.</span><span class="sxs-lookup"><span data-stu-id="02316-146">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="02316-147">Como este é um aplicativo de exemplo, usaremos a sessão para armazenar os tokens.</span><span class="sxs-lookup"><span data-stu-id="02316-147">Since this is a sample app, we'll use the session to store the tokens.</span></span> <span data-ttu-id="02316-148">Um aplicativo real usaria uma solução de armazenamento segura mais confiável, como um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="02316-148">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="02316-149">Clique com o botão direito do mouse na pasta do **tutorial do gráfico** no Gerenciador de soluções e escolha **Adicionar > nova pasta**.</span><span class="sxs-lookup"><span data-stu-id="02316-149">Right-click the **graph-tutorial** folder in Solution Explorer, and choose **Add > New Folder**.</span></span> <span data-ttu-id="02316-150">Nomeie a pasta `TokenStorage`.</span><span class="sxs-lookup"><span data-stu-id="02316-150">Name the folder `TokenStorage`.</span></span> <span data-ttu-id="02316-151">Clique com o botão direito do mouse na nova pasta e escolha **Adicionar classe >...**. Nomeie o arquivo `SessionTokenStore.cs` e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="02316-151">Right click this new folder and choose **Add > Class...**. Name the file `SessionTokenStore.cs` and choose **Add**.</span></span> <span data-ttu-id="02316-152">Substitua o conteúdo desse arquivo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-152">Replace the contents of this file with the following code.</span></span>

```cs
using Microsoft.Identity.Client;
using Newtonsoft.Json;
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

    // Adapted from https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-v2
    public class SessionTokenStore
    {
        private static ReaderWriterLockSlim sessionLock = new ReaderWriterLockSlim(LockRecursionPolicy.NoRecursion);
        private readonly string userId = string.Empty;
        private readonly string cacheId = string.Empty;
        private readonly string cachedUserId = string.Empty;
        private HttpContextBase httpContext = null;

        TokenCache tokenCache = new TokenCache();

        public SessionTokenStore(string userId, HttpContextBase httpContext)
        {
            this.userId = userId;
            cacheId = $"{userId}_TokenCache";
            cachedUserId = $"{userId}_UserCache";
            this.httpContext = httpContext;
            Load();
        }

        public TokenCache GetMsalCacheInstance()
        {
            tokenCache.SetBeforeAccess(BeforeAccessNotification);
            tokenCache.SetAfterAccess(AfterAccessNotification);
            Load();
            return tokenCache;
        }

        public bool HasData()
        {
            return (httpContext.Session[cacheId] != null && ((byte[])httpContext.Session[cacheId]).Length > 0);
        }

        public void Clear()
        {
            httpContext.Session.Remove(cacheId);
        }

        private void Load()
        {
            sessionLock.EnterReadLock();
            tokenCache.Deserialize((byte[])httpContext.Session[cacheId]);
            sessionLock.ExitReadLock();
        }

        private void Persist()
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cacheId] = tokenCache.Serialize();
            sessionLock.ExitReadLock();
        }

        // Triggered right before MSAL needs to access the cache.
        private void BeforeAccessNotification(TokenCacheNotificationArgs args)
        {
            // Reload the cache from the persistent store in case it changed since the last access.
            Load();
        }

        // Triggered right after MSAL accessed the cache.
        private void AfterAccessNotification(TokenCacheNotificationArgs args)
        {
            // if the access operation resulted in a cache update
            if (args.HasStateChanged)
            {
                Persist();
            }
        }

        public void SaveUserDetails(CachedUser user)
        {
            sessionLock.EnterReadLock();
            httpContext.Session[cachedUserId] = JsonConvert.SerializeObject(user);
            sessionLock.ExitReadLock();
        }

        public CachedUser GetUserDetails()
        {
            sessionLock.EnterReadLock();
            var cachedUser = JsonConvert.DeserializeObject<CachedUser>((string)httpContext.Session[cachedUserId]);
            sessionLock.ExitReadLock();
            return cachedUser;
        }
    }
}
```

<span data-ttu-id="02316-153">Este código cria uma `SessionTokenStore` classe que funciona com a classe da `TokenCache` biblioteca MSAL.</span><span class="sxs-lookup"><span data-stu-id="02316-153">This code creates a `SessionTokenStore` class that works with the MSAL library's `TokenCache` class.</span></span> <span data-ttu-id="02316-154">A maior parte do código aqui envolve a serialização e desserialização da `TokenCache` para a sessão.</span><span class="sxs-lookup"><span data-stu-id="02316-154">Most of the code here involves serializing and deserializing the `TokenCache` to the session.</span></span> <span data-ttu-id="02316-155">Também fornece uma classe e métodos para serializar e desserializar os detalhes do usuário para a sessão.</span><span class="sxs-lookup"><span data-stu-id="02316-155">It also provides a class and methods to serialize and deserialize the user's details to the session.</span></span>

<span data-ttu-id="02316-156">Agora, adicione a seguinte `using` instrução à parte superior do `App_Start/Startup.Auth.cs` arquivo.</span><span class="sxs-lookup"><span data-stu-id="02316-156">Now, add the following `using` statement to the top of the `App_Start/Startup.Auth.cs` file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.IdentityModel.Claims;
```

<span data-ttu-id="02316-157">Agora atualize a `OnAuthorizationCodeReceivedAsync` função para criar uma instância da `SessionTokenStore` classe e forneça-a para o construtor do `ConfidentialClientApplication` objeto.</span><span class="sxs-lookup"><span data-stu-id="02316-157">Now update the `OnAuthorizationCodeReceivedAsync` function to create an instance of the `SessionTokenStore` class and provide that to the constructor for the `ConfidentialClientApplication` object.</span></span> <span data-ttu-id="02316-158">Isso fará com que o MSAL Use sua implementação de cache para armazenar tokens.</span><span class="sxs-lookup"><span data-stu-id="02316-158">That will cause MSAL to use your cache implementation for storing tokens.</span></span> <span data-ttu-id="02316-159">Substitua a função `OnAuthorizationCodeReceivedAsync` existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="02316-159">Replace the existing `OnAuthorizationCodeReceivedAsync` function with the following.</span></span>

```cs
private async Task OnAuthorizationCodeReceivedAsync(AuthorizationCodeReceivedNotification notification)
{
    // Get the signed in user's id and create a token cache
    string signedInUserId = notification.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
    SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId,
        notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase);

    var idClient = new ConfidentialClientApplication(
        appId, redirectUri, new ClientCredential(appSecret), tokenStore.GetMsalCacheInstance(), null);

    try
    {
        string[] scopes = graphScopes.Split(' ');

        var result = await idClient.AcquireTokenByAuthorizationCodeAsync(
            notification.Code, scopes);

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
    catch(Microsoft.Graph.ServiceException ex)
    {
        string message = "GetUserDetailsAsync threw an exception";
        notification.HandleResponse();
        notification.Response.Redirect($"/Home/Error?message={message}&debug={ex.Message}");
    }
}
```

<span data-ttu-id="02316-160">Para resumir as alterações:</span><span class="sxs-lookup"><span data-stu-id="02316-160">To summarize the changes:</span></span>

- <span data-ttu-id="02316-161">Agora, o código passa `TokenCache` um objeto para o construtor `ConfidentialClientApplication`.</span><span class="sxs-lookup"><span data-stu-id="02316-161">The code now passes a `TokenCache` object to the constructor for `ConfidentialClientApplication`.</span></span> <span data-ttu-id="02316-162">A biblioteca MSAL tratará a lógica de armazenamento dos tokens e a atualização quando necessário.</span><span class="sxs-lookup"><span data-stu-id="02316-162">The MSAL library will handle the logic of storing the tokens and refreshing it when needed.</span></span>
- <span data-ttu-id="02316-163">O código agora passa os detalhes do usuário obtidos do Microsoft Graph para `SessionTokenStore` o objeto a ser armazenado na sessão.</span><span class="sxs-lookup"><span data-stu-id="02316-163">The code now passes the user details obtained from Microsoft Graph to the `SessionTokenStore` object to store in the session.</span></span>
- <span data-ttu-id="02316-164">No caso de sucesso, o código não é mais Redirecionado, apenas retorna.</span><span class="sxs-lookup"><span data-stu-id="02316-164">On success, the code no longer redirects, it just returns.</span></span> <span data-ttu-id="02316-165">Isso permite que o middleware OWIN conclua o processo de autenticação.</span><span class="sxs-lookup"><span data-stu-id="02316-165">This allows the OWIN middleware to complete the authentication process.</span></span>

<span data-ttu-id="02316-166">Como o cache de token é armazenado na sessão, atualize a `SignOut` ação em `Controllers/AccountController.cs` para limpar o token Store antes de sair. Primeiro, adicione a seguinte `using` instrução à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="02316-166">Since the token cache is stored in the session, update the `SignOut` action in `Controllers/AccountController.cs` to clear the token store before signing out. First, add the following `using` statement to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
```

<span data-ttu-id="02316-167">Em seguida, substitua a `SignOut` função existente pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="02316-167">Then, replace the existing `SignOut` function with the following.</span></span>

```cs
public ActionResult SignOut()
{
    if (Request.IsAuthenticated)
    {
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

        tokenStore.Clear();

        Request.GetOwinContext().Authentication.SignOut(
            CookieAuthenticationDefaults.AuthenticationType);
    }

    return RedirectToAction("Index", "Home");
}
```

<span data-ttu-id="02316-168">Os detalhes do usuário em cache são algo que cada modo de exibição no aplicativo precisará, portanto `BaseController` , atualize a classe para carregar essas informações da sessão.</span><span class="sxs-lookup"><span data-stu-id="02316-168">The cached user details are something that every view in the application will need, so update the `BaseController` class to load this information from the session.</span></span> <span data-ttu-id="02316-169">Abra `Controllers/BaseController.cs` e adicione as seguintes `using` instruções à parte superior do arquivo.</span><span class="sxs-lookup"><span data-stu-id="02316-169">Open `Controllers/BaseController.cs` and add the following `using` statements to the top of the file.</span></span>

```cs
using graph_tutorial.TokenStorage;
using System.Security.Claims;
using System.Web;
using Microsoft.Owin.Security.Cookies;
```

<span data-ttu-id="02316-170">Em seguida, adicione a função a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-170">Then add the following function.</span></span>

```cs
protected override void OnActionExecuting(ActionExecutingContext filterContext)
{
    if (Request.IsAuthenticated)
    {
        // Get the signed in user's id and create a token cache
        string signedInUserId = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier).Value;
        SessionTokenStore tokenStore = new SessionTokenStore(signedInUserId, HttpContext);

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

<span data-ttu-id="02316-171">Inicie o servidor e percorra o processo de entrada.</span><span class="sxs-lookup"><span data-stu-id="02316-171">Start the server and go through the sign-in process.</span></span> <span data-ttu-id="02316-172">Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.</span><span class="sxs-lookup"><span data-stu-id="02316-172">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

<span data-ttu-id="02316-174">Clique no avatar do usuário no canto superior direito para acessar o \*\*\*\* link sair.</span><span class="sxs-lookup"><span data-stu-id="02316-174">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="02316-175">Clicar \*\*\*\* em sair redefine a sessão e retorna à Home Page.</span><span class="sxs-lookup"><span data-stu-id="02316-175">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="02316-177">Atualizando tokens</span><span class="sxs-lookup"><span data-stu-id="02316-177">Refreshing tokens</span></span>

<span data-ttu-id="02316-178">Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API.</span><span class="sxs-lookup"><span data-stu-id="02316-178">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="02316-179">Este é o token que permite que o aplicativo acesse o Microsoft Graph em nome do usuário.</span><span class="sxs-lookup"><span data-stu-id="02316-179">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="02316-180">No entanto, esse token é de vida curta.</span><span class="sxs-lookup"><span data-stu-id="02316-180">However, this token is short-lived.</span></span> <span data-ttu-id="02316-181">O token expira uma hora após sua emissão.</span><span class="sxs-lookup"><span data-stu-id="02316-181">The token expires an hour after it is issued.</span></span> <span data-ttu-id="02316-182">É onde o token de atualização se torna útil.</span><span class="sxs-lookup"><span data-stu-id="02316-182">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="02316-183">O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.</span><span class="sxs-lookup"><span data-stu-id="02316-183">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="02316-184">Como o aplicativo está usando a biblioteca do MSAL e `TokenCache` um objeto, você não precisa implementar uma lógica de atualização de token.</span><span class="sxs-lookup"><span data-stu-id="02316-184">Because the app is using the MSAL library and a `TokenCache` object, you do not have to implement any token refresh logic.</span></span> <span data-ttu-id="02316-185">O `ConfidentialClientApplication.AcquireTokenSilentAsync` método faz toda a lógica para você.</span><span class="sxs-lookup"><span data-stu-id="02316-185">The `ConfidentialClientApplication.AcquireTokenSilentAsync` method does all of the logic for you.</span></span> <span data-ttu-id="02316-186">Primeiro ele verifica o token em cache e, se ele não tiver expirado, ele o retornará.</span><span class="sxs-lookup"><span data-stu-id="02316-186">It first checks the cached token, and if it is not expired, it returns it.</span></span> <span data-ttu-id="02316-187">Se ele tiver expirado, ele usará o token de atualização em cache para obter um novo.</span><span class="sxs-lookup"><span data-stu-id="02316-187">If it is expired, it uses the cached refresh token to obtain a new one.</span></span> <span data-ttu-id="02316-188">Você usará esse método no módulo a seguir.</span><span class="sxs-lookup"><span data-stu-id="02316-188">You'll use this method in the following module.</span></span>