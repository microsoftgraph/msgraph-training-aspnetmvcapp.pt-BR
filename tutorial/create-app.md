<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b3ad8-101">Abra o Visual Studio e selecione **arquivo _GT_ novo projeto de >**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-101">Open Visual Studio, and select **File > New > Project**.</span></span> <span data-ttu-id="b3ad8-102">Na caixa de diálogo **novo projeto** , faça o seguinte:</span><span class="sxs-lookup"><span data-stu-id="b3ad8-102">In the **New Project** dialog, do the following:</span></span>

1. <span data-ttu-id="b3ad8-103">Selecione **modelos _GT_ Visual C# _GT_ Web**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-103">Select **Templates > Visual C# > Web**.</span></span>
1. <span data-ttu-id="b3ad8-104">Selecione **ASP.NET Web Application (.NET Framework)**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-104">Select **ASP.NET Web Application (.NET Framework)**.</span></span>
1. <span data-ttu-id="b3ad8-105">Insira o **gráfico-tutorial** para o nome do projeto.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-105">Enter **graph-tutorial** for the Name of the project.</span></span>

![Visual Studio 2017 criar nova caixa de diálogo de projeto](./images/vs-new-project-01.png)

> [!NOTE]
> <span data-ttu-id="b3ad8-107">Certifique-se de inserir exatamente o mesmo nome para o projeto do Visual Studio especificado nas instruções do laboratório.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-107">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="b3ad8-108">O nome do projeto do Visual Studio se torna parte do namespace no código.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-108">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="b3ad8-109">O código dentro dessas instruções depende do namespace correspondente ao nome do projeto do Visual Studio especificado nessas instruções.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-109">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="b3ad8-110">Se você usar um nome de projeto diferente, o código não será compilado, a menos que você ajuste todos os namespaces para corresponder ao nome do projeto do Visual Studio que você digitou ao criar o projeto.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-110">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

<span data-ttu-id="b3ad8-111">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-111">Select **OK**.</span></span> <span data-ttu-id="b3ad8-112">Na caixa de diálogo **novo projeto de aplicativo Web do ASP.net** , selecione **MVC** (em **ASP.NET 4.7.2 templates**) e clique em **OK**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-112">In the **New ASP.NET Web Application Project** dialog, select **MVC** (under **ASP.NET 4.7.2 Templates**) and select **OK**.</span></span>

<span data-ttu-id="b3ad8-113">Pressione **F5** ou selecione **debug > iniciar depuração**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-113">Press **F5** or select **Debug > Start Debugging**.</span></span> <span data-ttu-id="b3ad8-114">Se tudo estiver funcionando, seu navegador padrão deverá abrir e exibir uma página padrão do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-114">If everything is working, your default browser should open and display a default ASP.NET page.</span></span>

<span data-ttu-id="b3ad8-115">Antes de prosseguir, atualize o `bootstrap` pacote NuGet e instale alguns pacotes NuGet adicionais que você usará posteriormente.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-115">Before moving on, update the `bootstrap` NuGet package, and install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="b3ad8-116">[Microsoft. Owin. host. SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) para habilitar as interfaces do [Owin](http://owin.org/) no aplicativo ASP.net.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-116">[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb/) to enable the [OWIN](http://owin.org/) interfaces in the ASP.NET application.</span></span>
- <span data-ttu-id="b3ad8-117">[Microsoft. Owin. Security. OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) para fazer a Autenticação OpenID Connect com o Azure.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-117">[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/) for doing OpenID Connect authentication with Azure.</span></span>
- <span data-ttu-id="b3ad8-118">[Microsoft. Owin. Security. cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) para habilitar a autenticação baseada em cookies.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-118">[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies/) to enable cookie-based authentication.</span></span>
- <span data-ttu-id="b3ad8-119">[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) para solicitar e gerenciar tokens de acesso.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-119">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) for requesting and managing access tokens.</span></span>
- <span data-ttu-id="b3ad8-120">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) para fazer chamadas para o Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-120">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>

<span data-ttu-id="b3ad8-121">Selecione **ferramentas _GT_ Gerenciador de pacotes do NuGet _GT_ console do Gerenciador de pacotes**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-121">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span> <span data-ttu-id="b3ad8-122">No console do Gerenciador de pacotes, insira os seguintes comandos.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-122">In the Package Manager Console, enter the following commands.</span></span>

```Powershell
Update-Package bootstrap
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Identity.Client -Version 2.1.0-preview
Install-Package Microsoft.Graph -Version 1.11.0
```

<span data-ttu-id="b3ad8-123">Crie uma classe de inicialização OWIN básica.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-123">Create a basic OWIN startup class.</span></span> <span data-ttu-id="b3ad8-124">Clique com o botão `graph-tutorial` direito do mouse na pasta no Gerenciador de soluções e escolha **Adicionar > novo item**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-124">Right-click the `graph-tutorial` folder in Solution Explorer and choose **Add > New Item**.</span></span> <span data-ttu-id="b3ad8-125">Escolha o modelo de **classe de inicialização OWIN** , nomeie `Startup.cs`o arquivo e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-125">Choose the **OWIN Startup Class** template, name the file `Startup.cs`, and choose **Add**.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="b3ad8-126">Projetar o aplicativo</span><span class="sxs-lookup"><span data-stu-id="b3ad8-126">Design the app</span></span>

<span data-ttu-id="b3ad8-127">Comece criando um modelo simples para uma mensagem de erro.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-127">Start by creating a simple model for an error message.</span></span> <span data-ttu-id="b3ad8-128">Você usará esse modelo para mensagens de erro do flash nos modos de exibição do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-128">You'll use this model to flash error messages in the app's views.</span></span>

<span data-ttu-id="b3ad8-129">Clique com o botão direito do mouse na pasta **modelos** no Gerenciador de soluções e escolha **Adicionar classe >...**. Nomeie a classe `Alert` e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-129">Right-click the **Models** folder in Solution Explorer and choose **Add > Class...**. Name the class `Alert` and choose **Add**.</span></span> <span data-ttu-id="b3ad8-130">Adicione o código a seguir `Alert.cs`no.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-130">Add the following code in `Alert.cs`.</span></span>

```cs
namespace graph_tutorial.Models
{
    public class Alert
    {
        public const string AlertKey = "TempDataAlerts";
        public string Message { get; set; }
        public string Debug { get; set; }
    }
}
```

<span data-ttu-id="b3ad8-131">Agora, atualize o layout global do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-131">Now update the global layout of the app.</span></span> <span data-ttu-id="b3ad8-132">Abra o `./Views/Shared/_Layout.cshtml` arquivo e substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-132">Open the `./Views/Shared/_Layout.cshtml` file, and replace its entire contents with the following code.</span></span>

```html
@{
    var alerts = TempData.ContainsKey(graph_tutorial.Models.Alert.AlertKey) ?
        (List<graph_tutorial.Models.Alert>)TempData[graph_tutorial.Models.Alert.AlertKey] :
        new List<graph_tutorial.Models.Alert>();
}

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ASP.NET Graph Tutorial</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")

    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
          integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt"
          crossorigin="anonymous">
</head>

<body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
        <div class="container">
            @Html.ActionLink("ASP.NET Graph Tutorial", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
                    aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
                <ul class="navbar-nav mr-auto">
                    <li class="nav-item">
                        @Html.ActionLink("Home", "Index", "Home", new { area = "" },
                            new { @class = ViewBag.Current == "Home" ? "nav-link active" : "nav-link" })
                    </li>
                    @if (Request.IsAuthenticated)
                    {
                        <li class="nav-item" data-turbolinks="false">
                            @Html.ActionLink("Calendar", "Index", "Calendar", new { area = "" },
                                new { @class = ViewBag.Current == "Calendar" ? "nav-link active" : "nav-link" })
                        </li>
                    }
                </ul>
                <ul class="navbar-nav justify-content-end">
                    <li class="nav-item">
                        <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                            <i class="fas fa-external-link-alt mr-1"></i>Docs
                        </a>
                    </li>
                    @if (Request.IsAuthenticated)
                    {
                        <li class="nav-item dropdown">
                            <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">
                                @if (!string.IsNullOrEmpty(ViewBag.User.Avatar))
                                {
                                    <img src="@ViewBag.User.Avatar" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                                }
                                else
                                {
                                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                                }
                            </a>
                            <div class="dropdown-menu dropdown-menu-right">
                                <h5 class="dropdown-item-text mb-0">@ViewBag.User.DisplayName</h5>
                                <p class="dropdown-item-text text-muted mb-0">@ViewBag.User.Email</p>
                                <div class="dropdown-divider"></div>
                                @Html.ActionLink("Sign Out", "SignOut", "Account", new { area = "" }, new { @class = "dropdown-item" })
                            </div>
                        </li>
                    }
                    else
                    {
                        <li class="nav-item">
                            @Html.ActionLink("Sign In", "SignIn", "Account", new { area = "" }, new { @class = "nav-link" })
                        </li>
                    }
                </ul>
            </div>
        </div>
    </nav>
    <main role="main" class="container">
        @foreach (var alert in alerts)
        {
            <div class="alert alert-danger" role="alert">
                <p class="mb-3">@alert.Message</p>
                @if (!string.IsNullOrEmpty(alert.Debug))
                {
                    <pre class="alert-pre border bg-light p-2"><code>@alert.Debug</code></pre>
                }
            </div>
        }

        @RenderBody()
    </main>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

<span data-ttu-id="b3ad8-133">Este código adiciona a [inicialização](https://getbootstrap.com/) para estilos simples e a [fonte incrível](https://fontawesome.com/) para alguns ícones simples.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-133">This code adds [Bootstrap](https://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="b3ad8-134">Também define um layout global com uma barra de navegação e usa a `Alert` classe para exibir alertas.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-134">It also defines a global layout with a nav bar, and uses the `Alert` class to display any alerts.</span></span>

<span data-ttu-id="b3ad8-135">Agora, `Content/Site.css` abra e substitua todo o conteúdo pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-135">Now open `Content/Site.css` and replace its entire contents with the following code.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="b3ad8-136">Agora, atualize a página padrão.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-136">Now update the default page.</span></span> <span data-ttu-id="b3ad8-137">Abra o `Views/Home/index.cshtml` arquivo e substitua seu conteúdo pelo seguinte.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-137">Open the `Views/Home/index.cshtml` file and replace its contents with the following.</span></span>

```html
@{
    ViewBag.Current = "Home";
}

<div class="jumbotron">
    <h1>ASP.NET Graph Tutorial</h1>
    <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from ASP.NET</p>
    @if (Request.IsAuthenticated)
    {
        <h4>Welcome @ViewBag.User.DisplayName!</h4>
        <p>Use the navigation bar at the top of the page to get started.</p>
    }
    else
    {
        @Html.ActionLink("Click here to sign in", "SignIn", "Account", new { area = "" }, new { @class = "btn btn-primary btn-large" })
    }
</div>
```

<span data-ttu-id="b3ad8-138">Agora, adicione uma função auxiliar para criar `Alert` um e passá-lo para o modo de exibição.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-138">Now add a helper function to create an `Alert` and pass it to the view.</span></span> <span data-ttu-id="b3ad8-139">Para torná-la facilmente disponível para qualquer controlador criado, defina uma classe de controlador de base.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-139">In order to make it easily available to any controller we create, define a base controller class.</span></span>

<span data-ttu-id="b3ad8-140">Clique com o botão direito do mouse na pasta **controladores** no Gerenciador de soluções e escolha **Adicionar controlador de >..**.. Escolha **controlador MVC 5-vazio** e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-140">Right-click the **Controllers** folder in Solution Explorer and choose **Add > Controller...**. Choose **MVC 5 Controller - Empty** and choose **Add**.</span></span> <span data-ttu-id="b3ad8-141">Nomeie o controlador `BaseController` e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-141">Name the controller `BaseController` and choose **Add**.</span></span> <span data-ttu-id="b3ad8-142">Substitua o conteúdo do `BaseController.cs` pelo código a seguir.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-142">Replace the contents of `BaseController.cs` with the following code.</span></span>

```cs
using graph_tutorial.Models;
using System.Collections.Generic;
using System.Web.Mvc;

namespace graph_tutorial.Controllers
{
    public abstract class BaseController : Controller
    {
        protected void Flash(string message, string debug=null)
        {
            var alerts = TempData.ContainsKey(Alert.AlertKey) ?
                (List<Alert>)TempData[Alert.AlertKey] :
                new List<Alert>();

            alerts.Add(new Alert
            {
                Message = message,
                Debug = debug
            });

            TempData[Alert.AlertKey] = alerts;
        }
    }
}
```

<span data-ttu-id="b3ad8-143">Qualquer controlador pode herdar dessa classe de controladora base para obter acesso `Flash` à função.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-143">Any controller can inherit from this base controller class to gain access to the `Flash` function.</span></span> <span data-ttu-id="b3ad8-144">Atualizar a `HomeController` classe da `BaseController`qual herdar.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-144">Update the `HomeController` class to inherit from `BaseController`.</span></span> <span data-ttu-id="b3ad8-145">Abra `Controllers/HomeController.cs` e altere a `public class HomeController : Controller` linha para:</span><span class="sxs-lookup"><span data-stu-id="b3ad8-145">Open `Controllers/HomeController.cs` and change the `public class HomeController : Controller` line to:</span></span>

```cs
public class HomeController : BaseController
```

<span data-ttu-id="b3ad8-146">Salve todas as suas alterações e reinicie o servidor.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-146">Save all of your changes and restart the server.</span></span> <span data-ttu-id="b3ad8-147">Agora, o aplicativo deve ser muito diferente.</span><span class="sxs-lookup"><span data-stu-id="b3ad8-147">Now, the app should look very different.</span></span>

![Uma captura de tela da página inicial reprojetada](./images/create-app-01.png)