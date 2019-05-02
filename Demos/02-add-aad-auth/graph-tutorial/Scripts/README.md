<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center">Popper. js</h1>

<p align="center">
    <strong>Uma biblioteca usada para posicionar os contenhantes nos aplicativos Web.</strong>
</p>

<p align="center">
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <img src="http://img.badgesize.io/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://www.bithound.io/github/FezVrasta/popper.js"><img src="https://www.bithound.io/github/FezVrasta/popper.js/badges/score.svg" alt="bitHound Overall Score"></a>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://gitter.im/FezVrasta/popper.js" target="_blank"><img src="https://img.shields.io/gitter/room/nwjs/nw.js.svg" alt="Get support or discuss"/></a>
    <br />
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest&iphone=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a>Wut? Pop-up?

Um popper é um elemento na tela que "retira" do fluxo natural do seu aplicativo.  
Exemplos comuns de Complementos são dicas de ferramentas, popovers e suspensos.


## <a name="so-yet-another-tooltip-library"></a>Então, outra biblioteca de dicas de ferramentas?

Bem, basicamente, **não**.  
Popper. js é um **mecanismo de posicionamento**, seu objetivo é calcular a posição de um elemento para tornar possível posicioná-lo próximo de um determinado elemento Reference.  

O mecanismo é completamente modular e a maioria dos seus recursos é **** implementada como modificadores (semelhante a middlewares ou plugins).  
A base de código inteira é escrita em ES2015 e seus recursos são testados automaticamente em navegadores reais graças ao [SauceLabs](https://saucelabs.com/) e [TravisCI](https://travis-ci.org/).

Popper. js tem zero dependências. Nenhum jQuery, sem LoDash, Nothing.  
Ele é usado por grandes empresas como o [Twitter em Bootstrap v4](https://getbootstrap.com/), [Microsoft no webclipping](https://github.com/OneNoteDev/WebClipper) e [Atlassian no AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).

### <a name="popperjs"></a>Popper. js

Este é o mecanismo, a biblioteca que computa e, opcionalmente, aplica os estilos aos pop-os.

Alguns dos principais pontos são:

- Elementos position mantê-los em seu contexto DOM original (não bagunça com seu DOM!);
- Permite exportar o informations computado para integração com as bibliotecas de reagir e outras.
- Oferece suporte a elementos de sombra DOM;
- Completamente personalizável graças à estrutura baseada em modificadores;

Visite nossa [página de projeto](https://fezvrasta.github.io/popper.js) para ver vários exemplos do que você pode fazer com o Popper. js!

Encontre [a documentação aqui](/docs/_includes/popper-documentation.md).


### <a name="tooltipjs"></a>ToolTip. js

Como muitos usuários precisam apenas de uma maneira simples de integrar dicas de ferramenta poderosas em seus projetos, criamos **ToolTip. js**.  
É uma pequena biblioteca que facilita a criação automática de dicas de ferramentas usando como mecanismo Popper. js.  
Sua API é quase idêntica ao famoso sistema de dica de ferramenta de inicialização, dessa forma será fácil integrá-lo em seus projetos.  
As dicas de ferramentas geradas por ToolTip. js estão acessíveis graças `aria` às marcas.

Encontre [a documentação aqui](/docs/_includes/tooltip-documentation.md).


## <a name="installation"></a>Instalação
Popper. js está disponível nos seguintes gerenciadores de pacotes e CDNs:

| Source |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install popper.js --save`                                                   |
| PNPM   | `yarn add popper.js`                                                             |
| NuGet  | `PM> Install-Package popper.js`                                                  |
| Bower  | `bower install popper.js --save`                     |
| unpkg  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

ToolTip. js também:

| Source |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install tooltip.js --save`                                                  |
| PNPM   | `yarn add tooltip.js`                                                            |
| Bower* | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| unpkg  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

\*: O Bower não é oficialmente suportado, pode ser usado para instalar ToolTip. js apenas Trough a CDN do unpkg.com. Este método tem a limitação de não ser capaz de definir uma versão específica da biblioteca. Bower e Popper. js sugerem usar o NPM ou o PNPM para seus projetos.  
Para obter mais informações, [Leia o problema relacionado](https://github.com/FezVrasta/popper.js/issues/390).

### <a name="dist-targets"></a>Destinos de dist

Popper. js atualmente é fornecido com 3 destinos em mente: UMD, ESM e ESNext.

- UMD-definição de módulo universal: AMD, RequireJS e globais;
- Módulos ESM-ES: para webpack/ROLLUP ou navegador que ofereça suporte à especificação;
- ESNext: disponível no `dist/`, pode ser usado com o webpack `babel-preset-env`e;

Certifique-se de usar o mais adequado para suas necessidades. Se você quiser importá-lo `<script>` com uma marca, use UMD.

## <a name="usage"></a>Uso

Dado um nó DOM Popper existente, peça ao Popper. js para posicioná-lo próximo de seu botão

```js
var reference = document.querySelector('.my-button');
var popper = document.querySelector('.my-popper');
var anotherPopper = new Popper(
    reference,
    popper,
    {
        // popper options here
    }
);
```

### <a name="callbacks"></a>Retornos

Popper. js oferece suporte a dois tipos de retornos `onCreate` de chamada, o retorno de chamada é chamado após a Popper ter sido initalized. O `onUpdate` único é chamado em qualquer atualização subsequente.

```js
const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    onCreate: (data) => {
        // data is an object containing all the informations computed
        // by Popper.js and used to style the popper and its arrow
        // The complete description is available in Popper.js documentation
    },
    onUpdate: (data) => {
        // same as `onCreate` but called on subsequent updates
    }
});
```

### <a name="writing-your-own-modifiers"></a>Escrever seus próprios modificadores

O Popper. js se baseia em uma arquitetura "do tipo de plug-in", a maioria dos seus recursos são totalmente encapsulados "modificadores".  
Um modificador é uma função que é chamada a cada vez que Popper. js precisa calcular a posição do Popper. Por esse motivo, os modificadores devem ser muito deformativos para evitar afunilamentos.  

Para saber como criar um modificador, [Leia a documentação dos modificadores](docs/_includes/popper-documentation.md#modifiers--object)


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a>Reagir, Vue. js, angular, AngularJS, Ember. js (etc...) integração

A integração de bibliotecas de terceiros em reagir ou outras bibliotecas pode ser um problema porque elas geralmente alteram o DOM e orientam as bibliotecas.  
Popper. js limita todas as suas modificações de DOM `applyStyle` dentro do modificador, basta desabilitá-lo e aplicar manualmente as coordenadas do Popper usando sua biblioteca de opções.  

Para obter uma lista abrangente de bibliotecas que permitem que você use o Popper. js em estruturas existentes, visite a página de [menção](/MENTIONS.md) .

Como alternativa, você pode até mesmo substituir por `applyStyles` seu próprio com seu personalizado e integrar o Popper. js sozinho!

```js
function applyReactStyle(data) {
    // export data in your framework and use its content to apply the style to your popper
};

const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    modifiers: {
        applyStyle: { enabled: false },
        applyReactStyle: {
            enabled: true,
            fn: applyReactStyle,
            order: 800,
        },
    },
});

```

### <a name="migration-from-popperjs-v0"></a>Migração do Popper. js V0

Como a API foi alterada, preparamos algumas instruções de migração para facilitar a atualização para o Popper. js v1.  

https://github.com/FezVrasta/popper.js/issues/62

Sinta-se livre para comentar dentro do problema se você tiver alguma dúvida.

### <a name="performances"></a>Desempenho

Popper. js é muito deformativo. Normalmente, leva 0,5 ms para calcular a posição de um popper (em um iMac com o Intel Core i5 de 3,5 G GHz).  
Isso significa que não causará nenhum [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), levando a uma experiência de usuário tranqüila.

## <a name="notes"></a>Observações

### <a name="libraries-using-popperjs"></a>Bibliotecas usando Popper. js

O objetivo do Popper. js é fornecer um mecanismo de posicionamento estável e poderoso pronto para ser usado em bibliotecas de terceiros.  

Visite a página de [menção](/MENTIONS.md) para obter uma lista atualizada de projetos.


### <a name="credits"></a>Créditos
Quero agradecer a alguns amigos e projetos o trabalho que faziam:

- [@AndreaScn](https://github.com/AndreaScn) para seu trabalho na página do GitHub e o teste manual que ele fez durante o desenvolvimento;
- [@vampolo](https://github.com/vampolo) a idéia original e o nome da biblioteca;
- [Sysdig](https://github.com/Draios) para todas as coisas incríveis aprendidas durante estes anos que tornaram possível escrever esta biblioteca;
- O [compartilhamento de Internet. js](http://github.hubspot.com/tether/) para me inspirar em escrever uma biblioteca de posicionamento pronta para o mundo real;
- [Os colaboradores](https://github.com/FezVrasta/popper.js/graphs/contributors) de suas solicitações pull e relatórios de erros muito apreciados;
- **você** para a estrela que você dará a este projeto e por ser tão incrível para dar a este projeto uma tentativa🙂

### <a name="copyright-and-license"></a>Direitos autorais e licença
Código e documentação Copyright 2016 **Federico Zivolo**. Código lançado sob a [licença MIT](LICENSE.md). Docs lançados no Creative Commons.
