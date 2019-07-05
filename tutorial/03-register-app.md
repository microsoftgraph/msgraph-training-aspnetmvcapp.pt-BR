<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="99f2f-101">Neste exercício, você criará um novo registro de aplicativo Web do Azure AD usando o centro de administração do Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="99f2f-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="99f2f-102">Determine a URL do SSL do seu aplicativo do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="99f2f-102">Determine your ASP.NET app's SSL URL.</span></span> <span data-ttu-id="99f2f-103">No Gerenciador de soluções do Visual Studio, selecione o projeto do **tutorial de gráfico** .</span><span class="sxs-lookup"><span data-stu-id="99f2f-103">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="99f2f-104">Na janela **Propriedades** , localize o valor da **URL do SSL**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-104">In the **Properties** window, find the value of **SSL URL**.</span></span> <span data-ttu-id="99f2f-105">Copie esse valor.</span><span class="sxs-lookup"><span data-stu-id="99f2f-105">Copy this value.</span></span>

    ![Captura de tela da janela Propriedades do Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="99f2f-107">Abra um navegador e navegue até o [centro de administração do Azure Active Directory](https://aad.portal.azure.com).</span><span class="sxs-lookup"><span data-stu-id="99f2f-107">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="99f2f-108">Faça logon usando uma **conta pessoal** (também conhecida como Conta da Microsoft) ou **Conta Corporativa ou de Estudante**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-108">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="99f2f-109">Selecione **Azure Active Directory** na navegação à esquerda e, em seguida, selecione **registros de aplicativo** em **gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-109">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="99f2f-110">Uma captura de tela dos registros de aplicativo</span><span class="sxs-lookup"><span data-stu-id="99f2f-110">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="99f2f-111">Selecione **Novo registro**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-111">Select **New registration**.</span></span> <span data-ttu-id="99f2f-112">Na página **Registrar um aplicativo**, defina os valores da seguinte forma.</span><span class="sxs-lookup"><span data-stu-id="99f2f-112">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="99f2f-113">Defina **Nome** para `ASP.NET Graph Tutorial`.</span><span class="sxs-lookup"><span data-stu-id="99f2f-113">Set **Name** to `ASP.NET Graph Tutorial`.</span></span>
    - <span data-ttu-id="99f2f-114">Defina **Tipos de conta com suporte** para **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-114">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="99f2f-115">Em **URI de Redirecionamento**, defina o primeiro menu suspenso como `Web` e defina o valor como a URL do aplicativo ASP.NET que você copiou na etapa 1.</span><span class="sxs-lookup"><span data-stu-id="99f2f-115">Under **Redirect URI**, set the first drop-down to `Web` and set the value to the ASP.NET app URL you copied in step 1.</span></span>

    ![Uma captura de tela da página registrar um aplicativo](./images/aad-register-an-app.png)

1. <span data-ttu-id="99f2f-117">Selecione **registrar**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-117">Select **Register**.</span></span> <span data-ttu-id="99f2f-118">Na página do **Tutorial ASP.net Graph** , copie o valor da **ID do aplicativo (cliente)** e salve-o, você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="99f2f-118">On the **ASP.NET Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Uma captura de tela da ID do aplicativo do novo registro de aplicativo](./images/aad-application-id.png)

1. <span data-ttu-id="99f2f-120">Selecione **Autenticação** em **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-120">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="99f2f-121">Localize a seção **Concessão Implícita** e habilite **tokens de ID**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-121">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="99f2f-122">Selecione **Salvar**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-122">Select **Save**.</span></span>

    ![Uma captura de tela da seção Grant implícita](./images/aad-implicit-grant.png)

1. <span data-ttu-id="99f2f-124">Selecione **Certificados e segredos** sob **Gerenciar**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-124">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="99f2f-125">Selecione o botão **Novo segredo do cliente**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-125">Select the **New client secret** button.</span></span> <span data-ttu-id="99f2f-126">Insira um valor em **Descrição** e selecione uma das opções para **expirar** e selecione **Adicionar**.</span><span class="sxs-lookup"><span data-stu-id="99f2f-126">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![Uma captura de tela da caixa de diálogo Adicionar um segredo do cliente](./images/aad-new-client-secret.png)

1. <span data-ttu-id="99f2f-128">Copie o valor secreto do cliente antes de sair desta página.</span><span class="sxs-lookup"><span data-stu-id="99f2f-128">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="99f2f-129">Você precisará dele na próxima etapa.</span><span class="sxs-lookup"><span data-stu-id="99f2f-129">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="99f2f-130">Este segredo do cliente nunca é mostrado novamente, portanto, copie-o agora.</span><span class="sxs-lookup"><span data-stu-id="99f2f-130">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![Uma captura de tela do novo segredo do cliente recentemente adicionado](./images/aad-copy-client-secret.png)
