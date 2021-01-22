<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você estenderá o aplicativo do exercício anterior para dar suporte à autenticação com o Azure AD. Isso é necessário para obter o token de acesso OAuth necessário para chamar a API do Microsoft Graph. Nesta etapa, você integrará o middleware OWIN e a biblioteca da Biblioteca de Autenticação da [Microsoft](https://www.nuget.org/packages/Microsoft.Identity.Client/) ao aplicativo.

1. Clique com o botão direito do **mouse no** projeto de tutorial gráfico no Explorador de Soluções e selecione Adicionar > Novo **Item...**. Choose **Web Configuration File**, name the file and select `PrivateSettings.config` **Add**. Substitua todo o conteúdo pelo código a seguir.

    ```xml
    <appSettings>
        <add key="ida:AppID" value="YOUR APP ID" />
        <add key="ida:AppSecret" value="YOUR APP PASSWORD" />
        <add key="ida:RedirectUri" value="https://localhost:PORT/" />
        <add key="ida:AppScopes" value="User.Read Calendars.Read" />
    </appSettings>
    ```

    Substitua pela ID do aplicativo do Portal de Registro de Aplicativo e substitua pelo `YOUR_APP_ID_HERE` `YOUR_APP_PASSWORD_HERE` segredo do cliente gerado. Se o segredo do cliente contiver esãos ( `&` ), certifique-se de substituí-los `&amp;` por . `PrivateSettings.config` Certifique-se também de modificar `PORT` o valor para que ele corresponder à URL do seu `ida:RedirectUri` aplicativo.

    > [!IMPORTANT]
    > Se você estiver usando o controle de código-fonte, como git, agora seria um bom momento para excluir o arquivo do controle de código-fonte para evitar o vazamento inadvertida de sua ID de aplicativo `PrivateSettings.config` e senha.

1. Atualize `Web.config` para carregar esse novo arquivo. Substitua `<appSettings>` o (linha 7) pelo seguinte

    ```xml
    <appSettings file="PrivateSettings.config">
    ```

## <a name="implement-sign-in"></a>Implementar a login

Comece inicializando o middleware OWIN para usar a autenticação do Azure AD para o aplicativo.

1. Clique com o botão direito **do mouse na App_Start** no Explorador de Soluções e selecione Adicionar > **Classe...**. Nome do arquivo `Startup.Auth.cs` e selecione **Adicionar**. Substitua todo o conteúdo pelo código a seguir.

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
    > Este código configura o middleware OWIN com os valores de e define dois métodos `PrivateSettings.config` de retorno de chamada e `OnAuthenticationFailedAsync` `OnAuthorizationCodeReceivedAsync` . Esses métodos de retorno de chamada serão invocados quando o processo de login retornar do Azure.

1. Agora atualize `Startup.cs` o arquivo para chamar o `ConfigureAuth` método. Substitua todo o conteúdo pelo `Startup.cs` código a seguir.

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

1. Adicione uma `Error` ação à classe para transformar os `HomeController` `message` parâmetros e consulta em um `debug` `Alert` objeto. Abra `Controllers/HomeController.cs` e adicione a função a seguir.

    ```cs
    public ActionResult Error(string message, string debug)
    {
        Flash(message, debug);
        return RedirectToAction("Index");
    }
    ```

1. Adicione um controlador para manipular a login. Clique com o botão direito **do** mouse na pasta Controladores no Solution Explorer e selecione **Adicionar > Controlador...**. Choose **MVC 5 Controller - Empty** and select **Add**. Nome do controlador `AccountController` e selecione **Adicionar**. Substitua todo o conteúdo do arquivo pelo código a seguir.

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

    Isso define uma `SignIn` ação `SignOut` e uma. A `SignIn` ação verifica se a solicitação já está autenticada. Caso não tenha, ele invoca o middleware OWIN para autenticar o usuário. A `SignOut` ação invoca o middleware OWIN para sair.

1. Salve suas alterações e inicie o projeto. Clique no botão de login e você deve ser redirecionado `https://login.microsoftonline.com` para. Entre com sua conta da Microsoft e consenta com as permissões solicitadas. O navegador redireciona para o aplicativo, mostrando o token.

### <a name="get-user-details"></a>Obter detalhes do usuário

Depois que o usuário está conectado, você pode obter as informações dele no Microsoft Graph.

1. Clique com o botão direito do **mouse na pasta de tutorial** do gráfico no Explorador de Soluções e selecione Adicionar > Nova **Pasta.** Nome da pasta `Helpers` .

1. Clique com o botão direito do mouse nessa nova pasta e selecione **Adicionar > Classe...**. Nome do arquivo `GraphHelper.cs` e selecione **Adicionar**. Substitua o conteúdo deste arquivo pelo código a seguir.

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
                        (requestMessage) =>
                        {
                            requestMessage.Headers.Authorization =
                                new AuthenticationHeaderValue("Bearer", accessToken);
                            return Task.FromResult(0);
                        }));

                return await graphClient.Me.Request().GetAsync();
            }
        }
    }
    ```

    Isso implementa a função, que usa o SDK do Microsoft Graph para chamar o ponto de `GetUserDetails` extremidade e retornar o `/me` resultado.

1. Atualize `OnAuthorizationCodeReceivedAsync` o método para chamar essa `App_Start/Startup.Auth.cs` função. Adicione a `using` instrução a seguir na parte superior do arquivo.

    ```cs
    using graph_tutorial.Helpers;
    ```

1. Substitua o bloco existente `try` pelo `OnAuthorizationCodeReceivedAsync` código a seguir.

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

1. Salve suas alterações e inicie o aplicativo. Depois de entrar, você deverá ver o nome e o endereço de email do usuário em vez do token de acesso.

## <a name="storing-the-tokens"></a>Armazenar os tokens

Agora que você pode obter tokens, é hora de implementar uma maneira de armazená-los no aplicativo. Como este é um aplicativo de exemplo, você usará a sessão para armazenar os tokens. Um aplicativo real usaria uma solução de armazenamento seguro mais confiável, como um banco de dados. Nesta seção, você vai:

- Implemente uma classe de armazenamento de tokens para serializar e armazenar o cache de token MSAL e os detalhes do usuário na sessão do usuário.
- Atualize o código de autenticação para usar a classe do armazenamento de tokens.
- Atualize a classe de controlador base para expor os detalhes do usuário armazenados a todos os visualizações no aplicativo.

1. Clique com o botão direito do **mouse na pasta de tutorial** do gráfico no Explorador de Soluções e selecione Adicionar > Nova **Pasta.** Nome da pasta `TokenStorage` .

1. Clique com o botão direito do mouse nessa nova pasta e selecione **Adicionar > Classe...**. Nome do arquivo `SessionTokenStore.cs` e selecione **Adicionar**. Substitua o conteúdo deste arquivo pelo código a seguir.

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

1. Adicione as instruções `using` a seguir na parte superior do `App_Start/Startup.Auth.cs` arquivo.

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
    > As alterações nesta nova versão fazem `OnAuthorizationCodeReceivedAsync` o seguinte:
    >
    > - O código agora envolve o cache de token de usuário `ConfidentialClientApplication` padrão com a `SessionTokenStore` classe. A biblioteca MSAL lidará com a lógica de armazenar os tokens e atualize-os quando necessário.
    > - O código agora passa os detalhes do usuário obtidos do Microsoft Graph para `SessionTokenStore` o objeto a ser armazenamento na sessão.
    > - Em caso de sucesso, o código não redireciona mais, ele apenas retorna. Isso permite que o middleware OWIN conclua o processo de autenticação.

1. Atualize `SignOut` a ação para limpar o armazenamento de tokens antes de sair. Adicione a `using` instrução a seguir à parte superior de `Controllers/AccountController.cs` .

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

1. Abra `Controllers/BaseController.cs` e adicione as instruções a seguir na parte superior do `using` arquivo.

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

1. Inicie o servidor e vá pelo processo de login. Você deve voltar à home page, mas a interface do usuário deve mudar para indicar que você está entrar.

    ![Uma captura de tela da home page após entrar](./images/add-aad-auth-01.png)

1. Clique no avatar do usuário no canto superior direito para acessar o link **Sair.** Clicar em **Sair** redefine a sessão e o retorna para a home page.

    ![Uma captura de tela do menu suspenso com o link Sair](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Atualização de tokens

Neste ponto, seu aplicativo tem um token de acesso, que é enviado no `Authorization` header de chamadas de API. Esse é o token que permite que o aplicativo acesse o Microsoft Graph em nome do usuário.

No entanto, esse token tem curta duração. O token expira uma hora após sua emissão. É aqui que o token de atualização se torna útil. O token de atualização permite que o aplicativo solicite um novo token de acesso sem exigir que o usuário entre novamente.

Como o aplicativo está usando a biblioteca MSAL e serializando o objeto, você não precisa implementar nenhuma lógica de atualização `TokenCache` de token. O `ConfidentialClientApplication.AcquireTokenSilentAsync` método faz toda a lógica para você. Primeiro, ele verifica o token armazenado em cache e, se não tiver expirado, ele o retornará. Se ele tiver expirado, ele usará o token de atualização em cache para obter um novo. Você usará esse método no módulo a seguir.
