<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a2bc4-101">Neste exercício, você criará um novo registro de aplicativo Web do Azure AD usando o ARP (portal de registro de aplicativo).</span><span class="sxs-lookup"><span data-stu-id="a2bc4-101">In this exercise, you will create a new Azure AD web application registration using the Application Registry Portal (ARP).</span></span>

1. <span data-ttu-id="a2bc4-102">Abra um navegador e navegue até o [portal de registro do aplicativo](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="a2bc4-102">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="a2bc4-103">Faça logon usando uma **conta pessoal** (aka: conta da Microsoft) ou **conta corporativa ou de estudante**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="a2bc4-104">Selecione **Adicionar um aplicativo** na parte superior da página.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-104">Select **Add an app** at the top of the page.</span></span>

    > [!NOTE]
    > <span data-ttu-id="a2bc4-105">Se você vir mais de um botão **Adicionar um aplicativo** na página, selecione aquele que corresponde à lista **aplicativos convergidos** .</span><span class="sxs-lookup"><span data-stu-id="a2bc4-105">If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="a2bc4-106">Na página **registrar seu aplicativo** , defina o **nome do aplicativo** como **tutorial de gráfico do ASP.net** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-106">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Captura de tela da criação de um novo aplicativo no site do portal de registro de aplicativo](./images/arp-create-app-01.png)

1. <span data-ttu-id="a2bc4-108">Na página **registro do tutorial do ASP.net Graph** , na seção **Propriedades** , copie a **ID do aplicativo** , pois você precisará dela mais tarde.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-108">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Captura de tela da ID do aplicativo recém-criado](./images/arp-create-app-02.png)

1. <span data-ttu-id="a2bc4-110">Role para baixo até a seção **segredos do aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="a2bc4-110">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="a2bc4-111">Selecione **gerar nova senha**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-111">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="a2bc4-112">Na caixa de diálogo **nova senha gerada** , copie o conteúdo da caixa, pois você precisará dela mais tarde.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-112">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="a2bc4-113">**Importante:** Essa senha nunca é mostrada novamente, portanto, certifique-se de copiá-la agora.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-113">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Captura de tela da senha do aplicativo recém-criado](./images/arp-create-app-03.png)

1. <span data-ttu-id="a2bc4-115">Determine a URL do seu aplicativo do ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-115">Determine your ASP.NET app's URL.</span></span> <span data-ttu-id="a2bc4-116">No Gerenciador de soluções do Visual Studio, selecione o projeto do **tutorial de gráfico** .</span><span class="sxs-lookup"><span data-stu-id="a2bc4-116">In Visual Studio's Solution Explorer, select the **graph-tutorial** project.</span></span> <span data-ttu-id="a2bc4-117">Na janela **Propriedades** , localize o valor da **URL**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-117">In the **Properties** window, find the value of **URL**.</span></span> <span data-ttu-id="a2bc4-118">Copie esse valor.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-118">Copy this value.</span></span>

    ![Captura de tela da janela Propriedades do Visual Studio](./images/vs-project-url.png)

1. <span data-ttu-id="a2bc4-120">Role para baixo até a seção **plataformas** .</span><span class="sxs-lookup"><span data-stu-id="a2bc4-120">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="a2bc4-121">Selecione **Adicionar plataforma**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-121">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="a2bc4-122">Na caixa de diálogo **Adicionar plataforma** , selecione **Web**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-122">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Captura de tela criando uma plataforma para o aplicativo](./images/arp-create-app-04.png)

    1. <span data-ttu-id="a2bc4-124">Na caixa plataforma **da Web** , digite a URL que você copiou das propriedades do projeto do Visual Studio para as **URLs**de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-124">In the **Web** platform box, enter the URL you copied from the Visual Studio project's properties for the **Redirect URLs**.</span></span>

        ![Captura de tela da nova plataforma Web adicionada para o aplicativo](./images/arp-create-app-05.png)

1. <span data-ttu-id="a2bc4-126">Role até a parte inferior da página e selecione **salvar**.</span><span class="sxs-lookup"><span data-stu-id="a2bc4-126">Scroll to the bottom of the page and select **Save**.</span></span>