<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f1cfc-101">Neste exercício, você criará um novo registro de aplicativo Web do Azure AD usando o centro de administração do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="f1cfc-102">Determine a URL do seu aplicativo do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-102">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="f1cfc-103">No Gerenciador de soluções do Visual Studio, selecione o projeto do **tutorial de gráfico** .</span><span class="sxs-lookup"><span data-stu-id="f1cfc-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="f1cfc-104">Na janela **Propriedades** , localize o valor da **URL**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-104">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="f1cfc-105">Copie esse valor.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-105">Copy this value.</span></span>

    ![Captura de tela da janela Propriedades do Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="f1cfc-107">Abra um navegador e navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="f1cfc-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="f1cfc-108">Faça logon usando uma **conta pessoal** (aka: conta da Microsoft) ou **conta corporativa ou de estudante**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="f1cfc-109">Selecione **Azure Active Directory** na navegação à esquerda e, em seguida, selecione **registros de aplicativo (visualização)** em **gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations (Preview)** under **Manage**.</span></span>

    ![<span data-ttu-id="f1cfc-110">Uma captura de tela dos registros de aplicativo</span><span class="sxs-lookup"><span data-stu-id="f1cfc-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="f1cfc-111">Selecione **novo registro**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-111">Select **New registration**.</span></span> <span data-ttu-id="f1cfc-112">Na página **registrar um aplicativo** , defina os valores da seguinte maneira.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="f1cfc-113">Defina \*\*\*\* o nome `ASP.NET Graph Tutorial`como.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="f1cfc-114">Defina os **tipos de conta com suporte** para **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="f1cfc-115">Em **URI**de redirecionamento, defina o primeiro menu `Web` suspenso como e defina o valor para a URL do aplicativo ASP.NET que você copiou na etapa 1.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Uma captura de tela da página registrar um aplicativo](./images/aad-register-an-app.png)

1. <span data-ttu-id="f1cfc-117">Escolha **registrar**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-117">Choose **Register**.</span></span> <span data-ttu-id="f1cfc-118">Na página do **Tutorial ASP.net Graph** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](./images/aad-application-id.png)

1. <span data-ttu-id="f1cfc-120">Selecione **autenticação** em **gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="f1cfc-121">Localize a seção **Grant implícita** e habilite os tokens de **ID**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="f1cfc-122">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-122">Choose **Save**.</span></span>

    ![Uma captura de tela da seção Grant implícita](./images/aad-implicit-grant.png)

1. <span data-ttu-id="f1cfc-124">Selecione **certificados & segredos** sob **gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="f1cfc-125">Selecione o botão **novo cliente secreto** .</span><span class="sxs-lookup"><span data-stu-id="f1cfc-125">Select the **New client secret** button.</span></span> <span data-ttu-id="f1cfc-126">Insira um valor em **Descrição** e selecione uma das opções para **expirar** e escolha **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-126">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![Uma captura de tela da caixa de diálogo Adicionar um segredo do cliente](./images/aad-new-client-secret.png)

1. <span data-ttu-id="f1cfc-128">Copie o valor de segredo do cliente antes de sair desta página.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="f1cfc-129">Você precisará dela na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="f1cfc-130">Esse segredo do cliente nunca é mostrado novamente, portanto, certifique-se de copiá-lo agora.</span><span class="sxs-lookup"><span data-stu-id="f1cfc-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Uma captura de tela do novo segredo do cliente recentemente adicionado](./images/aad-copy-client-secret.png)