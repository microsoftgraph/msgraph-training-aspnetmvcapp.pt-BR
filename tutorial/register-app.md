<!-- markdownlint-disable MD002 MD041 -->

Neste exercício, você criará um novo registro de aplicativo Web do Azure AD usando o ARP (portal de registro de aplicativo).

1. Abra um navegador e navegue até o [portal de registro do aplicativo](https://apps.dev.microsoft.com). Faça logon usando uma **conta pessoal** (aka: conta da Microsoft) ou **conta corporativa ou de estudante**.

1. Selecione **Adicionar um aplicativo** na parte superior da página.

    > [!NOTE]
    > Se você vir mais de um botão **Adicionar um aplicativo** na página, selecione aquele que corresponde à lista **aplicativos convergidos** .

1. Na página **registrar seu aplicativo** , defina o **nome do aplicativo** como **tutorial de gráfico do ASP.net** e selecione **criar**.

    ![Captura de tela da criação de um novo aplicativo no site do portal de registro de aplicativo](./images/arp-create-app-01.png)

1. Na página **registro do tutorial do ASP.net Graph** , na seção **Propriedades** , copie a **ID do aplicativo** , pois você precisará dela mais tarde.

    ![Captura de tela da ID do aplicativo recém-criado](./images/arp-create-app-02.png)

1. Role para baixo até a seção **segredos do aplicativo** .

    1. Selecione **gerar nova senha**.
    1. Na caixa de diálogo **nova senha gerada** , copie o conteúdo da caixa, pois você precisará dela mais tarde.

        > **Importante:** Essa senha nunca é mostrada novamente, portanto, certifique-se de copiá-la agora.

    ![Captura de tela da senha do aplicativo recém-criado](./images/arp-create-app-03.png)

1. Determine a URL do seu aplicativo do ASP.NET. No Gerenciador de soluções do Visual Studio, selecione o projeto do **tutorial de gráfico** . Na janela **Propriedades** , localize o valor da **URL**. Copie esse valor.

    ![Captura de tela da janela Propriedades do Visual Studio](./images/vs-project-url.png)

1. Role para baixo até a seção **plataformas** .

    1. Selecione **Adicionar plataforma**.
    1. Na caixa de diálogo **Adicionar plataforma** , selecione **Web**.

        ![Captura de tela criando uma plataforma para o aplicativo](./images/arp-create-app-04.png)

    1. Na caixa plataforma **da Web** , digite a URL que você copiou das propriedades do projeto do Visual Studio para as **URLs**de redirecionamento.

        ![Captura de tela da nova plataforma Web adicionada para o aplicativo](./images/arp-create-app-05.png)

1. Role até a parte inferior da página e selecione **salvar**.