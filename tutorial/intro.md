<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f39e0-101">Este tutorial ensina como criar um aplicativo Web do ASP.NET MVC que usa a API do Microsoft Graph para recuperar informações de calendário de um usuário.</span><span class="sxs-lookup"><span data-stu-id="f39e0-101">This tutorial teaches you how to build an ASP.NET MVC web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="f39e0-102">Se preferir baixar o tutorial concluído, você pode baixá-lo de duas maneiras.</span><span class="sxs-lookup"><span data-stu-id="f39e0-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="f39e0-103">Baixe o [início rápido do ASP.net](https://developer.microsoft.com/graph/quick-start?platform=option-dotnet) para obter o código de trabalho em minutos.</span><span class="sxs-lookup"><span data-stu-id="f39e0-103">Download the [ASP.NET quick start](https://developer.microsoft.com/graph/quick-start?platform=option-dotnet) to get working code in minutes.</span></span>
> - <span data-ttu-id="f39e0-104">Baixe ou clone o [repositório do GitHub](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span><span class="sxs-lookup"><span data-stu-id="f39e0-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f39e0-105">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="f39e0-105">Prerequisites</span></span>

<span data-ttu-id="f39e0-106">Antes de iniciar este tutorial, você deve ter o [Visual Studio](https://visualstudio.microsoft.com/vs/) instalado em sua máquina de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="f39e0-106">Before you start this tutorial, you should have [Visual Studio](https://visualstudio.microsoft.com/vs/) installed on your development machine.</span></span> <span data-ttu-id="f39e0-107">Se você não tiver o Visual Studio, visite o link anterior para opções de download.</span><span class="sxs-lookup"><span data-stu-id="f39e0-107">If you do not have Visual Studio, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="f39e0-108">Este tutorial foi escrito com o Visual Studio 2017 versão 15,81.</span><span class="sxs-lookup"><span data-stu-id="f39e0-108">This tutorial was written with Visual Studio 2017 version 15.81.</span></span> <span data-ttu-id="f39e0-109">As etapas deste guia podem funcionar com outras versões, mas que não foram testadas.</span><span class="sxs-lookup"><span data-stu-id="f39e0-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="f39e0-110">Comentários</span><span class="sxs-lookup"><span data-stu-id="f39e0-110">Feedback</span></span>

<span data-ttu-id="f39e0-111">Forneça comentários sobre este tutorial no [repositório do GitHub](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span><span class="sxs-lookup"><span data-stu-id="f39e0-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp).</span></span>