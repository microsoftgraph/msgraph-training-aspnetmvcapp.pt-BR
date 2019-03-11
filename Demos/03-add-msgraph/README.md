# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="1a896-101">Como executar o projeto concluído</span><span class="sxs-lookup"><span data-stu-id="1a896-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a896-102">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="1a896-102">Prerequisites</span></span>

<span data-ttu-id="1a896-103">Para executar o projeto concluído nessa pasta, você precisará do seguinte:</span><span class="sxs-lookup"><span data-stu-id="1a896-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="1a896-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="1a896-104">[Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="1a896-105">Se você não tiver o Visual Studio, visite o link anterior para opções de download.</span><span class="sxs-lookup"><span data-stu-id="1a896-105">If you do not have Visual Studio, visit the previous link for download options.</span></span> <span data-ttu-id="1a896-106">(**Observação:** este tutorial foi escrito com o Visual Studio 2017 versão 15,81.</span><span class="sxs-lookup"><span data-stu-id="1a896-106">(**Note:** This tutorial was written with Visual Studio 2017 version 15.81.</span></span> <span data-ttu-id="1a896-107">As etapas deste guia podem funcionar com outras versões, mas que não foram testadas.</span><span class="sxs-lookup"><span data-stu-id="1a896-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="1a896-108">Uma conta pessoal da Microsoft com uma caixa de correio no Outlook.com ou uma conta corporativa ou de estudante da Microsoft.</span><span class="sxs-lookup"><span data-stu-id="1a896-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="1a896-109">Se você não tem uma conta da Microsoft, há algumas opções para obter uma conta gratuita:</span><span class="sxs-lookup"><span data-stu-id="1a896-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="1a896-110">Você pode [se inscrever para uma nova conta pessoal da Microsoft](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span><span class="sxs-lookup"><span data-stu-id="1a896-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="1a896-111">Você pode [se inscrever no programa para desenvolvedores do office 365](https://developer.microsoft.com/office/dev-program) para obter uma assinatura gratuita do Office 365.</span><span class="sxs-lookup"><span data-stu-id="1a896-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="1a896-112">Registrar um aplicativo Web com o portal de registro do aplicativo</span><span class="sxs-lookup"><span data-stu-id="1a896-112">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="1a896-113">Abra um navegador e navegue até o [portal de registro do aplicativo](https://apps.dev.microsoft.com).</span><span class="sxs-lookup"><span data-stu-id="1a896-113">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="1a896-114">Faça logon usando uma **conta pessoal** (aka: conta da Microsoft) ou **conta corporativa ou de estudante**.</span><span class="sxs-lookup"><span data-stu-id="1a896-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="1a896-115">Selecione **Adicionar um aplicativo** na parte superior da página.</span><span class="sxs-lookup"><span data-stu-id="1a896-115">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="1a896-116">**Observação:** Se você vir mais de um botão **Adicionar um aplicativo** na página, selecione aquele que corresponde à lista **aplicativos convergidos** .</span><span class="sxs-lookup"><span data-stu-id="1a896-116">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="1a896-117">Na página **registrar seu aplicativo** , defina o **nome do aplicativo** como **tutorial de gráfico do ASP.net** e selecione **criar**.</span><span class="sxs-lookup"><span data-stu-id="1a896-117">On the **Register your application** page, set the **Application Name** to **ASP.NET Graph Tutorial** and select **Create**.</span></span>

    ![Captura de tela da criação de um novo aplicativo no site do portal de registro de aplicativo](/tutorial/images/arp-create-app-01.png)

1. <span data-ttu-id="1a896-119">Na página **registro do tutorial do ASP.net Graph** , na seção **Propriedades** , copie a **ID do aplicativo** , pois você precisará dela mais tarde.</span><span class="sxs-lookup"><span data-stu-id="1a896-119">On the **ASP.NET Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![Captura de tela da ID do aplicativo recém-criado](/tutorial/images/arp-create-app-02.png)

1. <span data-ttu-id="1a896-121">Role para baixo até a seção **segredos do aplicativo** .</span><span class="sxs-lookup"><span data-stu-id="1a896-121">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="1a896-122">Selecione **gerar nova senha**.</span><span class="sxs-lookup"><span data-stu-id="1a896-122">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="1a896-123">Na caixa de diálogo **nova senha gerada** , copie o conteúdo da caixa, pois você precisará dela mais tarde.</span><span class="sxs-lookup"><span data-stu-id="1a896-123">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="1a896-124">**Importante:** Essa senha nunca é mostrada novamente, portanto, certifique-se de copiá-la agora.</span><span class="sxs-lookup"><span data-stu-id="1a896-124">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![Captura de tela da senha do aplicativo recém-criado](/tutorial/images/arp-create-app-03.png)

1. <span data-ttu-id="1a896-126">Role para baixo até a seção **plataformas** .</span><span class="sxs-lookup"><span data-stu-id="1a896-126">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="1a896-127">Selecione **Adicionar plataforma**.</span><span class="sxs-lookup"><span data-stu-id="1a896-127">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="1a896-128">Na caixa de diálogo **Adicionar plataforma** , selecione **Web**.</span><span class="sxs-lookup"><span data-stu-id="1a896-128">In the **Add Platform** dialog, select **Web**.</span></span>

        ![Captura de tela criando uma plataforma para o aplicativo](/tutorial/images/arp-create-app-04.png)

    1. <span data-ttu-id="1a896-130">Na caixa plataforma **da Web** , digite a URL `http://localhost:64107/` das **URLs**de redirecionamento.</span><span class="sxs-lookup"><span data-stu-id="1a896-130">In the **Web** platform box, enter the URL `http://localhost:64107/` for the **Redirect URLs**.</span></span>

        ![Captura de tela da nova plataforma Web adicionada para o aplicativo](/tutorial/images/arp-create-app-05.png)

1. <span data-ttu-id="1a896-132">Role até a parte inferior da página e selecione **salvar**.</span><span class="sxs-lookup"><span data-stu-id="1a896-132">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="1a896-133">Configurar o exemplo</span><span class="sxs-lookup"><span data-stu-id="1a896-133">Configure the sample</span></span>

1. <span data-ttu-id="1a896-134">ReNomear `PrivateSettings.config.example` o `PrivateSettings.config`arquivo para.</span><span class="sxs-lookup"><span data-stu-id="1a896-134">Rename the `PrivateSettings.config.example` file to `PrivateSettings.config`.</span></span>
1. <span data-ttu-id="1a896-135">Edite `PrivateSettings.config` o arquivo e faça as seguintes alterações.</span><span class="sxs-lookup"><span data-stu-id="1a896-135">Edit the `PrivateSettings.config` file and make the following changes.</span></span>
    1. <span data-ttu-id="1a896-136">Substitua `YOUR_APP_ID_HERE` pela **ID do aplicativo** obtida do portal de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1a896-136">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="1a896-137">Substitua `YOUR_APP_PASSWORD_HERE` pela senha obtida do portal de registro do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="1a896-137">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="1a896-138">Abra `graph-tutorial.sln` no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="1a896-138">Open `graph-tutorial.sln` in Visual Studio.</span></span> <span data-ttu-id="1a896-139">No Gerenciador de soluções, clique com o botão direito do mouse na solução do **tutorial do gráfico** e escolha **restaurar pacotes NuGet**.</span><span class="sxs-lookup"><span data-stu-id="1a896-139">In Solution Explorer, right-click the **graph-tutorial** solution and choose **Restore NuGet Packages**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="1a896-140">Executar o exemplo</span><span class="sxs-lookup"><span data-stu-id="1a896-140">Run the sample</span></span>

<span data-ttu-id="1a896-141">No Visual Studio, pressione **F5** ou escolha **depurar > iniciar depuração**.</span><span class="sxs-lookup"><span data-stu-id="1a896-141">In Visual Studio, press **F5** or choose **Debug > Start Debugging**.</span></span>