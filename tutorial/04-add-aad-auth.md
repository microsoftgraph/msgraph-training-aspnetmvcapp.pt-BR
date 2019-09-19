<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para oferecer suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar a API do Microsoft Graph. Nesta etapa, você integrará o middleware OWIN e a biblioteca da [biblioteca de autenticação da Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) no aplicativo.

1. Clique com o botão direito do mouse no projeto do **tutorial de gráfico** no Gerenciador de soluções e selecione **Adicionar > novo item...**. Escolha **arquivo de configuração da Web**, nomeie `PrivateSettings.config` o arquivo e selecione **Adicionar**. Substitua todo o conteúdo pelo código a seguir.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Substitua `YOUR_APP_ID_HERE` pela ID do aplicativo do portal de registro do aplicativo e substitua `YOUR_APP_PASSWORD_HERE` pelo segredo do cliente gerado. Se o segredo do cliente contiver um`&`e comercial (), substitua-o `&amp;` pelo `PrivateSettings.config`. Além disso, certifique-se `PORT` de modificar o `ida:RedirectUri` valor para o para corresponder à URL do aplicativo.

    > [!IMPORTANT]
    > Se você estiver usando o controle de origem como o Git, agora seria uma boa hora para excluir `PrivateSettings.config` o arquivo do controle de origem para evitar vazar inadvertidamente sua ID de aplicativo e sua senha.

1. Atualize `Web.config` para carregar este novo arquivo. Substitua o `<appSettings>` (linha 7) pelo seguinte

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Implementar logon

Inicie inicializando o middleware OWIN para usar a autenticação do Azure AD para o aplicativo.

1. Clique com o botão direito do mouse na pasta **App_Start** no Gerenciador de soluções e selecione **Adicionar > classe...**. Nomeie o arquivo `Startup.Auth.cs` e selecione **Adicionar**. Substitua todo o conteúdo pelo código a seguir.

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
    > Este código configura o middleware OWIN com os valores de `PrivateSettings.config` e define dois métodos `OnAuthenticationFailedAsync` de retorno de chamada e. `OnAuthorizationCodeReceivedAsync` Esses métodos de retorno de chamada serão invocados quando o processo de entrada retornar do Azure.

1. Agora atualize o `Startup.cs` arquivo para chamar o `ConfigureAuth` método. Substitua todo o conteúdo do `Startup.cs` pelo código a seguir.

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

1. Adicione uma `Error` ação à `HomeController` classe para transformar os parâmetros `message` de `debug` consulta e em um `Alert` objeto. Abra `Controllers/HomeController.cs` e adicione a função a seguir.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Adicione um controlador para lidar com a entrada. Clique com o botão direito do mouse na pasta **controladores** no Gerenciador de soluções e selecione **Adicionar > controlador...**. Escolha **controlador MVC 5-vazio** e selecione **Adicionar**. Nomeie o controlador `AccountController` e selecione **Adicionar**. Substitua todo o conteúdo do arquivo pelo código a seguir.

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

    Isso define uma `SignIn` ação `SignOut` e. A `SignIn` ação verifica se a solicitação já está autenticada. Caso contrário, ele invocará o middleware OWIN para autenticar o usuário. A `SignOut` ação invoca o middleware OWIN para sair.

1. Salve suas alterações e inicie o projeto. Clique no botão entrar e você deverá ser redirecionado para `https://login.microsoftonline.com`o. Faça logon com sua conta da Microsoft e concorde com as permissões solicitadas. O navegador redireciona para o aplicativo, mostrando o token.

### <a name="get-user-details"></a>Obter detalhes do usuário

Depois que o usuário estiver conectado, você pode obter as informações do Microsoft Graph.

1. Clique com o botão direito do mouse na pasta do **tutorial do gráfico** no Gerenciador de soluções e selecione **Adicionar > nova pasta**. Nomeie a pasta `Helpers`.

1. Clique com o botão direito do mouse na nova pasta e selecione **adicionar > classe...**. Nomeie o arquivo `GraphHelper.cs` e selecione **Adicionar**. Substitua o conteúdo desse arquivo pelo código a seguir.

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

    Isso implementa a `GetUserDetails` função, que usa o SDK do Microsoft Graph para chamar `/me` o ponto de extremidade e retornar o resultado.

1. Atualize o `OnAuthorizationCodeReceivedAsync` método no `App_Start/Startup.Auth.cs` para chamar essa função. Adicione a seguinte `using` instrução à parte superior do arquivo.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Substitua o bloco `try` existente `OnAuthorizationCodeReceivedAsync` pelo código a seguir.

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

1. Salve suas alterações e inicie o aplicativo, após entrar, você deve ver o nome do usuário e o endereço de email em vez do token de acesso.

## <a name="storing-the-tokens"></a>Armazenar tokens

Agora que você pode obter tokens, é hora de implementar uma maneira de armazená-los no aplicativo. Como este é um aplicativo de exemplo, você usará a sessão para armazenar os tokens. Um aplicativo real usaria uma solução de armazenamento segura mais confiável, como um banco de dados. Nesta seção, você irá:

- Implemente uma classe de repositório de token para serializar e armazenar o cache de token do MSAL e os detalhes do usuário na sessão do usuário.
- Atualize o código de autenticação para usar a classe de repositório de tokens.
- Atualize a classe do controlador base para expor os detalhes do usuário armazenados a todos os modos de exibição no aplicativo.

1. Clique com o botão direito do mouse na pasta do **tutorial do gráfico** no Gerenciador de soluções e selecione **Adicionar > nova pasta**. Nomeie a pasta `TokenStorage`.

1. Clique com o botão direito do mouse na nova pasta e selecione **adicionar > classe...**. Nomeie o arquivo `SessionTokenStore.cs` e selecione **Adicionar**. Substitua o conteúdo desse arquivo pelo código a seguir.

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

                    var userTenantId = user.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value ??
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

1. Adicione as seguintes `using` instruções à parte superior do `App_Start/Startup.Auth.cs` arquivo.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    ```

1. Substitua a função `OnAuthorizationCodeReceivedAsync` existente pelo seguinte.

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
    > As alterações nessa nova versão do `OnAuthorizationCodeReceivedAsync` fazem o seguinte:
    >
    > - O código agora encapsula o `ConfidentialClientApplication`cache de token de usuário padrão com a `SessionTokenStore` classe. A biblioteca MSAL tratará a lógica de armazenamento dos tokens e a atualização quando necessário.
    > - O código agora passa os detalhes do usuário obtidos do Microsoft Graph para `SessionTokenStore` o objeto a ser armazenado na sessão.
    > - No caso de sucesso, o código não é mais Redirecionado, apenas retorna. Isso permite que o middleware OWIN conclua o processo de autenticação.

1. Atualize a `SignOut` ação para limpar o repositório de token antes de sair. Adicione a seguinte `using` instrução à parte superior de `Controllers/AccountController.cs`.

    ```cs
    using graph_tutorial.TokenStorage;
    ```

1. Substitua a função `SignOut` existente pelo seguinte.

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

1. Abra `Controllers/BaseController.cs` e adicione as seguintes `using` instruções à parte superior do arquivo.

    ```cs
    using graph_tutorial.TokenStorage;
    using System.Security.Claims;
    using System.Web;
    using Microsoft.Owin.Security.Cookies;
    ```

1. Adicione a função a seguir.

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

1. Inicie o servidor e percorra o processo de entrada. Você deve terminar de volta na Home Page, mas a interface do usuário deve ser alterada para indicar que você está conectado.

    ![Uma captura de tela da Home Page após entrar](./images/add-aad-auth-01.png)

1. Clique no avatar do usuário no canto superior direito para **acessar o link sair.** Clicar **em sair** redefine a sessão e retorna à Home Page.

    ![Uma captura de tela do menu suspenso com o link sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Atualizando tokens

Nesse ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` cabeçalho das chamadas de API. Este é o token que permite ao aplicativo acessar o Microsoft Graph em nome do usuário.

No entanto, esse token é de vida curta. O token expira uma hora após sua emissão. É onde o token de atualização se torna útil. O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.

Como o aplicativo está usando a biblioteca MSAL e serializando `TokenCache` o objeto, você não precisa implementar uma lógica de atualização de token. O `ConfidentialClientApplication.AcquireTokenSilentAsync` método faz toda a lógica para você. Primeiro ele verifica o token em cache e, se ele não tiver expirado, ele o retornará. Se ele tiver expirado, ele usará o token de atualização em cache para obter um novo. Você usará esse método no módulo a seguir.
