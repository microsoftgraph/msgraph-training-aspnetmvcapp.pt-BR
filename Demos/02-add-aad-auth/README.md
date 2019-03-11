# <a name="completed-module-add-azure-ad-authentication"></a>Módulo concluído: Adicionar autenticação do Azure AD

A versão do projeto nesse diretório reflete a conclusão do tutorial para a [adição da autenticação do Azure ad](https://docs.microsoft.com/graph/training/aspnet-tutorial?tutorial-step=3). Se você usar esta versão do projeto, precisará concluir o restante do tutorial em [obter dados do calendário](https://docs.microsoft.com/graph/training/aspnet-tutorial?tutorial-step=4).

> **Observação:** Presume-se que você já tenha registrado um aplicativo no portal de registro de aplicativo conforme especificado em [registrar o aplicativo no portal](https://docs.microsoft.com/graph/training/aspnet-tutorial?tutorial-step=2). Você precisa configurar esta versão do exemplo da seguinte maneira:
>
> 1. ReNomear `PrivateSettings.config.example` o `PrivateSettings.config`arquivo para.
> 1. Edite `PrivateSettings.config` o arquivo e faça as seguintes alterações.
>     1. Substitua `YOUR APP ID HERE` pela **ID do aplicativo** obtida do portal de registro do aplicativo.
>     1. Substitua `YOUR APP PASSWORD HERE` pela senha obtida do portal de registro do aplicativo.