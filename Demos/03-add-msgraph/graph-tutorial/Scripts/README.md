<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center"><span data-ttu-id="14be9-101">Popper. js</span><span class="sxs-lookup"><span data-stu-id="14be9-101">Popper.js</span></span></h1>

<p align="center"><span data-ttu-id="14be9-102">
    <strong>Uma biblioteca usada para posicionar os contenhantes nos aplicativos Web.</strong>
</span><span class="sxs-lookup"><span data-stu-id="14be9-102">
    <strong>A library used to position poppers in web applications.</strong>
</span></span></p>

<p align="center">
    <img src="http://badge-size.now.sh/https://unpkg.com/popper.js/dist/popper.min.js?compression=brotli" alt="Stable Release Size"/>
  <img src="http://badge-size.now.sh/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://www.npmjs.com/browse/depended/popper.js"><img src="https://badgen.net/npm/dependents/popper.js" alt="Dependents packages" /></a>
    <a href="https://spectrum.chat/popper-js" target="_blank"><img src="https://img.shields.io/badge/chat-on_spectrum-6833F9.svg?logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyBpZD0iTGl2ZWxsb18xIiBkYXRhLW5hbWU9IkxpdmVsbG8gMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB2aWV3Qm94PSIwIDAgMTAgOCI%2BPGRlZnM%2BPHN0eWxlPi5jbHMtMXtmaWxsOiNmZmY7fTwvc3R5bGU%2BPC9kZWZzPjx0aXRsZT5zcGVjdHJ1bTwvdGl0bGU%2BPHBhdGggY2xhc3M9ImNscy0xIiBkPSJNNSwwQy40MiwwLDAsLjYzLDAsMy4zNGMwLDEuODQuMTksMi43MiwxLjc0LDMuMWgwVjcuNThhLjQ0LjQ0LDAsMCwwLC42OC4zNUw0LjM1LDYuNjlINWM0LjU4LDAsNS0uNjMsNS0zLjM1UzkuNTgsMCw1LDBaTTIuODMsNC4xOGEuNjMuNjMsMCwxLDEsLjY1LS42M0EuNjQuNjQsMCwwLDEsMi44Myw0LjE4Wk01LDQuMThhLjYzLjYzLDAsMSwxLC42NS0uNjNBLjY0LjY0LDAsMCwxLDUsNC4xOFptMi4xNywwYS42My42MywwLDEsMSwuNjUtLjYzQS42NC42NCwwLDAsMSw3LjE3LDQuMThaIi8%2BPC9zdmc%2B" alt="Get support or discuss"/></a>
    <br />
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a><span data-ttu-id="14be9-103">Wut?</span><span class="sxs-lookup"><span data-stu-id="14be9-103">Wut?</span></span> <span data-ttu-id="14be9-104">Pop-up?</span><span class="sxs-lookup"><span data-stu-id="14be9-104">Poppers?</span></span>

<span data-ttu-id="14be9-105">Um popper é um elemento na tela que "retira" do fluxo natural do seu aplicativo.</span><span class="sxs-lookup"><span data-stu-id="14be9-105">A popper is an element on the screen which "pops out" from the natural flow of your application.</span></span>  
<span data-ttu-id="14be9-106">Exemplos comuns de Complementos são dicas de ferramentas, popovers e suspensos.</span><span class="sxs-lookup"><span data-stu-id="14be9-106">Common examples of poppers are tooltips, popovers and drop-downs.</span></span>


## <a name="so-yet-another-tooltip-library"></a><span data-ttu-id="14be9-107">Então, outra biblioteca de dicas de ferramentas?</span><span class="sxs-lookup"><span data-stu-id="14be9-107">So, yet another tooltip library?</span></span>

<span data-ttu-id="14be9-108">Bem, basicamente, **não**.</span><span class="sxs-lookup"><span data-stu-id="14be9-108">Well, basically, **no**.</span></span>  
<span data-ttu-id="14be9-109">Popper. js é um **mecanismo de posicionamento**, seu objetivo é calcular a posição de um elemento para tornar possível posicioná-lo próximo de um determinado elemento Reference.</span><span class="sxs-lookup"><span data-stu-id="14be9-109">Popper.js is a **positioning engine**, its purpose is to calculate the position of an element to make it possible to position it near a given reference element.</span></span>  

<span data-ttu-id="14be9-110">O mecanismo é completamente modular e a maioria dos seus recursos é implementada como **modificadores** (semelhante a middlewares ou plugins).</span><span class="sxs-lookup"><span data-stu-id="14be9-110">The engine is completely modular and most of its features are implemented as **modifiers** (similar to middlewares or plugins).</span></span>  
<span data-ttu-id="14be9-111">A base de código inteira é escrita em ES2015 e seus recursos são testados automaticamente em navegadores reais graças ao [SauceLabs](https://saucelabs.com/) e [TravisCI](https://travis-ci.org/).</span><span class="sxs-lookup"><span data-stu-id="14be9-111">The whole code base is written in ES2015 and its features are automatically tested on real browsers thanks to [SauceLabs](https://saucelabs.com/) and [TravisCI](https://travis-ci.org/).</span></span>

<span data-ttu-id="14be9-112">Popper. js tem zero dependências.</span><span class="sxs-lookup"><span data-stu-id="14be9-112">Popper.js has zero dependencies.</span></span> <span data-ttu-id="14be9-113">Nenhum jQuery, sem LoDash, Nothing.</span><span class="sxs-lookup"><span data-stu-id="14be9-113">No jQuery, no LoDash, nothing.</span></span>  
<span data-ttu-id="14be9-114">Ele é usado por grandes empresas como o [Twitter em Bootstrap v4](https://getbootstrap.com/), [Microsoft no webclipping](https://github.com/OneNoteDev/WebClipper) e [Atlassian no AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span><span class="sxs-lookup"><span data-stu-id="14be9-114">It's used by big companies like [Twitter in Bootstrap v4](https://getbootstrap.com/), [Microsoft in WebClipper](https://github.com/OneNoteDev/WebClipper) and [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span></span>

### <a name="popperjs"></a><span data-ttu-id="14be9-115">Popper. js</span><span class="sxs-lookup"><span data-stu-id="14be9-115">Popper.js</span></span>

<span data-ttu-id="14be9-116">Este é o mecanismo, a biblioteca que computa e, opcionalmente, aplica os estilos aos pop-os.</span><span class="sxs-lookup"><span data-stu-id="14be9-116">This is the engine, the library that computes and, optionally, applies the styles to the poppers.</span></span>

<span data-ttu-id="14be9-117">Alguns dos principais pontos são:</span><span class="sxs-lookup"><span data-stu-id="14be9-117">Some of the key points are:</span></span>

- <span data-ttu-id="14be9-118">Elementos position mantê-los em seu contexto DOM original (não bagunça com seu DOM!);</span><span class="sxs-lookup"><span data-stu-id="14be9-118">Position elements keeping them in their original DOM context (doesn't mess with your DOM!);</span></span>
- <span data-ttu-id="14be9-119">Permite exportar o informations computado para integração com as bibliotecas de reagir e outras.</span><span class="sxs-lookup"><span data-stu-id="14be9-119">Allows to export the computed informations to integrate with React and other view libraries;</span></span>
- <span data-ttu-id="14be9-120">Oferece suporte a elementos de sombra DOM;</span><span class="sxs-lookup"><span data-stu-id="14be9-120">Supports Shadow DOM elements;</span></span>
- <span data-ttu-id="14be9-121">Completamente personalizável graças à estrutura baseada em modificadores;</span><span class="sxs-lookup"><span data-stu-id="14be9-121">Completely customizable thanks to the modifiers based structure;</span></span>

<span data-ttu-id="14be9-122">Visite nossa [página de projeto](https://fezvrasta.github.io/popper.js) para ver vários exemplos do que você pode fazer com o Popper. js!</span><span class="sxs-lookup"><span data-stu-id="14be9-122">Visit our [project page](https://fezvrasta.github.io/popper.js) to see a lot of examples of what you can do with Popper.js!</span></span>

<span data-ttu-id="14be9-123">Encontre [a documentação aqui](/docs/_includes/popper-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="14be9-123">Find [the documentation here](/docs/_includes/popper-documentation.md).</span></span>


### <a name="tooltipjs"></a><span data-ttu-id="14be9-124">ToolTip. js</span><span class="sxs-lookup"><span data-stu-id="14be9-124">Tooltip.js</span></span>

<span data-ttu-id="14be9-125">Como muitos usuários precisam apenas de uma maneira simples de integrar dicas de ferramenta poderosas em seus projetos, criamos **ToolTip. js**.</span><span class="sxs-lookup"><span data-stu-id="14be9-125">Since lots of users just need a simple way to integrate powerful tooltips in their projects, we created **Tooltip.js**.</span></span>  
<span data-ttu-id="14be9-126">É uma pequena biblioteca que facilita a criação automática de dicas de ferramentas usando como mecanismo Popper. js.</span><span class="sxs-lookup"><span data-stu-id="14be9-126">It's a small library that makes it easy to automatically create tooltips using as engine Popper.js.</span></span>  
<span data-ttu-id="14be9-127">Sua API é quase idêntica ao famoso sistema de dica de ferramenta de inicialização, dessa forma será fácil integrá-lo em seus projetos.</span><span class="sxs-lookup"><span data-stu-id="14be9-127">Its API is almost identical to the famous tooltip system of Bootstrap, in this way it will be easy to integrate it in your projects.</span></span>  
<span data-ttu-id="14be9-128">As dicas de ferramentas geradas por ToolTip. js estão acessíveis graças `aria` às marcas.</span><span class="sxs-lookup"><span data-stu-id="14be9-128">The tooltips generated by Tooltip.js are accessible thanks to the `aria` tags.</span></span>

<span data-ttu-id="14be9-129">Encontre [a documentação aqui](/docs/_includes/tooltip-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="14be9-129">Find [the documentation here](/docs/_includes/tooltip-documentation.md).</span></span>


## <a name="installation"></a><span data-ttu-id="14be9-130">Instalação</span><span class="sxs-lookup"><span data-stu-id="14be9-130">Installation</span></span>
<span data-ttu-id="14be9-131">Popper. js está disponível nos seguintes gerenciadores de pacotes e CDNs:</span><span class="sxs-lookup"><span data-stu-id="14be9-131">Popper.js is available on the following package managers and CDNs:</span></span>

| <span data-ttu-id="14be9-132">Origem</span><span class="sxs-lookup"><span data-stu-id="14be9-132">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="14be9-133">npm</span><span class="sxs-lookup"><span data-stu-id="14be9-133">npm</span></span>    | `npm install popper.js --save`                                                   |
| <span data-ttu-id="14be9-134">PNPM</span><span class="sxs-lookup"><span data-stu-id="14be9-134">yarn</span></span>   | `yarn add popper.js`                                                             |
| <span data-ttu-id="14be9-135">NuGet</span><span class="sxs-lookup"><span data-stu-id="14be9-135">NuGet</span></span>  | `PM> Install-Package popper.js`                                                  |
| <span data-ttu-id="14be9-136">Bower</span><span class="sxs-lookup"><span data-stu-id="14be9-136">Bower</span></span>  | `bower install popper.js --save`                     |
| <span data-ttu-id="14be9-137">unpkg</span><span class="sxs-lookup"><span data-stu-id="14be9-137">unpkg</span></span>  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| <span data-ttu-id="14be9-138">cdnjs</span><span class="sxs-lookup"><span data-stu-id="14be9-138">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="14be9-139">ToolTip. js também:</span><span class="sxs-lookup"><span data-stu-id="14be9-139">Tooltip.js as well:</span></span>

| <span data-ttu-id="14be9-140">Origem</span><span class="sxs-lookup"><span data-stu-id="14be9-140">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="14be9-141">npm</span><span class="sxs-lookup"><span data-stu-id="14be9-141">npm</span></span>    | `npm install tooltip.js --save`                                                  |
| <span data-ttu-id="14be9-142">PNPM</span><span class="sxs-lookup"><span data-stu-id="14be9-142">yarn</span></span>   | `yarn add tooltip.js`                                                            |
| <span data-ttu-id="14be9-143">Bower\*</span><span class="sxs-lookup"><span data-stu-id="14be9-143">Bower\*</span></span> | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| <span data-ttu-id="14be9-144">unpkg</span><span class="sxs-lookup"><span data-stu-id="14be9-144">unpkg</span></span>  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| <span data-ttu-id="14be9-145">cdnjs</span><span class="sxs-lookup"><span data-stu-id="14be9-145">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="14be9-146">\*: O Bower não é oficialmente suportado, pode ser usado para instalar ToolTip. js apenas Trough a CDN do unpkg.com.</span><span class="sxs-lookup"><span data-stu-id="14be9-146">\*: Bower isn't officially supported, it can be used to install Tooltip.js only trough the unpkg.com CDN.</span></span> <span data-ttu-id="14be9-147">Este método tem a limitação de não ser capaz de definir uma versão específica da biblioteca.</span><span class="sxs-lookup"><span data-stu-id="14be9-147">This method has the limitation of not being able to define a specific version of the library.</span></span> <span data-ttu-id="14be9-148">Bower e Popper. js sugerem usar o NPM ou o PNPM para seus projetos.</span><span class="sxs-lookup"><span data-stu-id="14be9-148">Bower and Popper.js suggests to use npm or Yarn for your projects.</span></span>  
<span data-ttu-id="14be9-149">Para obter mais informações, [Leia o problema relacionado](https://github.com/FezVrasta/popper.js/issues/390).</span><span class="sxs-lookup"><span data-stu-id="14be9-149">For more info, [read the related issue](https://github.com/FezVrasta/popper.js/issues/390).</span></span>

### <a name="dist-targets"></a><span data-ttu-id="14be9-150">Destinos de dist</span><span class="sxs-lookup"><span data-stu-id="14be9-150">Dist targets</span></span>

<span data-ttu-id="14be9-151">Popper. js atualmente é fornecido com 3 destinos em mente: UMD, ESM e ESNext.</span><span class="sxs-lookup"><span data-stu-id="14be9-151">Popper.js is currently shipped with 3 targets in mind: UMD, ESM and ESNext.</span></span>

- <span data-ttu-id="14be9-152">UMD-definição de módulo universal: AMD, RequireJS e globais;</span><span class="sxs-lookup"><span data-stu-id="14be9-152">UMD - Universal Module Definition: AMD, RequireJS and globals;</span></span>
- <span data-ttu-id="14be9-153">Módulos ESM-ES: para webpack/ROLLUP ou navegador que ofereça suporte à especificação;</span><span class="sxs-lookup"><span data-stu-id="14be9-153">ESM - ES Modules: For webpack/Rollup or browser supporting the spec;</span></span>
- <span data-ttu-id="14be9-154">ESNext: disponível no `dist/`, pode ser usado com o webpack `babel-preset-env`e;</span><span class="sxs-lookup"><span data-stu-id="14be9-154">ESNext: Available in `dist/`, can be used with webpack and `babel-preset-env`;</span></span>

<span data-ttu-id="14be9-155">Certifique-se de usar o mais adequado para suas necessidades.</span><span class="sxs-lookup"><span data-stu-id="14be9-155">Make sure to use the right one for your needs.</span></span> <span data-ttu-id="14be9-156">Se você quiser importá-lo `<script>` com uma marca, use UMD.</span><span class="sxs-lookup"><span data-stu-id="14be9-156">If you want to import it with a `<script>` tag, use UMD.</span></span>

## <a name="usage"></a><span data-ttu-id="14be9-157">Uso</span><span class="sxs-lookup"><span data-stu-id="14be9-157">Usage</span></span>

<span data-ttu-id="14be9-158">Dado um nó DOM Popper existente, peça ao Popper. js para posicioná-lo próximo de seu botão</span><span class="sxs-lookup"><span data-stu-id="14be9-158">Given an existing popper DOM node, ask Popper.js to position it near its button</span></span>

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

### <a name="callbacks"></a><span data-ttu-id="14be9-159">Retornos</span><span class="sxs-lookup"><span data-stu-id="14be9-159">Callbacks</span></span>

<span data-ttu-id="14be9-160">Popper. js oferece suporte a dois tipos de retornos `onCreate` de chamada, o retorno de chamada é chamado depois que o Popper foi inicializado.</span><span class="sxs-lookup"><span data-stu-id="14be9-160">Popper.js supports two kinds of callbacks, the `onCreate` callback is called after the popper has been initialized.</span></span> <span data-ttu-id="14be9-161">O `onUpdate` único é chamado em qualquer atualização subsequente.</span><span class="sxs-lookup"><span data-stu-id="14be9-161">The `onUpdate` one is called on any subsequent update.</span></span>

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

### <a name="writing-your-own-modifiers"></a><span data-ttu-id="14be9-162">Escrever seus próprios modificadores</span><span class="sxs-lookup"><span data-stu-id="14be9-162">Writing your own modifiers</span></span>

<span data-ttu-id="14be9-163">O Popper. js se baseia em uma arquitetura "do tipo de plug-in", a maioria dos seus recursos são totalmente encapsulados "modificadores".</span><span class="sxs-lookup"><span data-stu-id="14be9-163">Popper.js is based on a "plugin-like" architecture, most of its features are fully encapsulated "modifiers".</span></span>  
<span data-ttu-id="14be9-164">Um modificador é uma função que é chamada a cada vez que Popper. js precisa calcular a posição do Popper.</span><span class="sxs-lookup"><span data-stu-id="14be9-164">A modifier is a function that is called each time Popper.js needs to compute the position of the popper.</span></span> <span data-ttu-id="14be9-165">Por esse motivo, os modificadores devem ser muito deformativos para evitar afunilamentos.</span><span class="sxs-lookup"><span data-stu-id="14be9-165">For this reason, modifiers should be very performant to avoid bottlenecks.</span></span>  

<span data-ttu-id="14be9-166">Para saber como criar um modificador, [Leia a documentação dos modificadores](docs/_includes/popper-documentation.md#modifiers--object)</span><span class="sxs-lookup"><span data-stu-id="14be9-166">To learn how to create a modifier, [read the modifiers documentation](docs/_includes/popper-documentation.md#modifiers--object)</span></span>


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a><span data-ttu-id="14be9-167">Reagir, Vue. js, angular, AngularJS, Ember. js (etc...) integração</span><span class="sxs-lookup"><span data-stu-id="14be9-167">React, Vue.js, Angular, AngularJS, Ember.js (etc...) integration</span></span>

<span data-ttu-id="14be9-168">A integração de bibliotecas de terceiros em reagir ou outras bibliotecas pode ser um problema porque elas geralmente alteram o DOM e orientam as bibliotecas.</span><span class="sxs-lookup"><span data-stu-id="14be9-168">Integrating 3rd party libraries in React or other libraries can be a pain because they usually alter the DOM and drive the libraries crazy.</span></span>  
<span data-ttu-id="14be9-169">Popper. js limita todas as suas modificações de DOM `applyStyle` dentro do modificador, basta desabilitá-lo e aplicar manualmente as coordenadas do Popper usando sua biblioteca de opções.</span><span class="sxs-lookup"><span data-stu-id="14be9-169">Popper.js limits all its DOM modifications inside the `applyStyle` modifier, you can simply disable it and manually apply the popper coordinates using your library of choice.</span></span>  

<span data-ttu-id="14be9-170">Para obter uma lista abrangente de bibliotecas que permitem que você use o Popper. js em estruturas existentes, visite a página de [menção](/MENTIONS.md) .</span><span class="sxs-lookup"><span data-stu-id="14be9-170">For a comprehensive list of libraries that let you use Popper.js into existing frameworks, visit the [MENTIONS](/MENTIONS.md) page.</span></span>

<span data-ttu-id="14be9-171">Como alternativa, você pode até mesmo substituir por `applyStyles` seu próprio com seu personalizado e integrar o Popper. js sozinho!</span><span class="sxs-lookup"><span data-stu-id="14be9-171">Alternatively, you may even override your own `applyStyles` with your custom one and integrate Popper.js by yourself!</span></span>

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

### <a name="migration-from-popperjs-v0"></a><span data-ttu-id="14be9-172">Migração do Popper. js V0</span><span class="sxs-lookup"><span data-stu-id="14be9-172">Migration from Popper.js v0</span></span>

<span data-ttu-id="14be9-173">Como a API foi alterada, preparamos algumas instruções de migração para facilitar a atualização para o Popper. js v1.</span><span class="sxs-lookup"><span data-stu-id="14be9-173">Since the API changed, we prepared some migration instructions to make it easy to upgrade to Popper.js v1.</span></span>  

https://github.com/FezVrasta/popper.js/issues/62

<span data-ttu-id="14be9-174">Sinta-se livre para comentar dentro do problema se você tiver alguma dúvida.</span><span class="sxs-lookup"><span data-stu-id="14be9-174">Feel free to comment inside the issue if you have any questions.</span></span>

### <a name="performances"></a><span data-ttu-id="14be9-175">Desempenho</span><span class="sxs-lookup"><span data-stu-id="14be9-175">Performances</span></span>

<span data-ttu-id="14be9-176">Popper. js é muito deformativo.</span><span class="sxs-lookup"><span data-stu-id="14be9-176">Popper.js is very performant.</span></span> <span data-ttu-id="14be9-177">Normalmente, leva 0,5 ms para calcular a posição de um popper (em um iMac com o Intel Core i5 de 3,5 G GHz).</span><span class="sxs-lookup"><span data-stu-id="14be9-177">It usually takes 0.5ms to compute a popper's position (on an iMac with 3.5G GHz Intel Core i5).</span></span>  
<span data-ttu-id="14be9-178">Isso significa que não causará nenhum [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), levando a uma experiência de usuário tranqüila.</span><span class="sxs-lookup"><span data-stu-id="14be9-178">This means that it will not cause any [jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), leading to a smooth user experience.</span></span>

## <a name="notes"></a><span data-ttu-id="14be9-179">Observações</span><span class="sxs-lookup"><span data-stu-id="14be9-179">Notes</span></span>

### <a name="libraries-using-popperjs"></a><span data-ttu-id="14be9-180">Bibliotecas usando Popper. js</span><span class="sxs-lookup"><span data-stu-id="14be9-180">Libraries using Popper.js</span></span>

<span data-ttu-id="14be9-181">O objetivo do Popper. js é fornecer um mecanismo de posicionamento estável e poderoso pronto para ser usado em bibliotecas de terceiros.</span><span class="sxs-lookup"><span data-stu-id="14be9-181">The aim of Popper.js is to provide a stable and powerful positioning engine ready to be used in 3rd party libraries.</span></span>  

<span data-ttu-id="14be9-182">Visite a página de [menção](/MENTIONS.md) para obter uma lista atualizada de projetos.</span><span class="sxs-lookup"><span data-stu-id="14be9-182">Visit the [MENTIONS](/MENTIONS.md) page for an updated list of projects.</span></span>


### <a name="credits"></a><span data-ttu-id="14be9-183">Créditos</span><span class="sxs-lookup"><span data-stu-id="14be9-183">Credits</span></span>
<span data-ttu-id="14be9-184">Quero agradecer a alguns amigos e projetos o trabalho que faziam:</span><span class="sxs-lookup"><span data-stu-id="14be9-184">I want to thank some friends and projects for the work they did:</span></span>

- <span data-ttu-id="14be9-185">[@AndreaScn](https://github.com/AndreaScn) para seu trabalho na página do GitHub e o teste manual que ele fez durante o desenvolvimento;</span><span class="sxs-lookup"><span data-stu-id="14be9-185">[@AndreaScn](https://github.com/AndreaScn) for his work on the GitHub Page and the manual testing he did during the development;</span></span>
- <span data-ttu-id="14be9-186">[@vampolo](https://github.com/vampolo) a idéia original e o nome da biblioteca;</span><span class="sxs-lookup"><span data-stu-id="14be9-186">[@vampolo](https://github.com/vampolo) for the original idea and for the name of the library;</span></span>
- <span data-ttu-id="14be9-187">[Sysdig](https://github.com/Draios) para todas as coisas incríveis aprendidas durante estes anos que tornaram possível escrever esta biblioteca;</span><span class="sxs-lookup"><span data-stu-id="14be9-187">[Sysdig](https://github.com/Draios) for all the awesome things I learned during these years that made it possible for me to write this library;</span></span>
- <span data-ttu-id="14be9-188">O [compartilhamento de Internet. js](http://github.hubspot.com/tether/) para me inspirar em escrever uma biblioteca de posicionamento pronta para o mundo real;</span><span class="sxs-lookup"><span data-stu-id="14be9-188">[Tether.js](http://github.hubspot.com/tether/) for having inspired me in writing a positioning library ready for the real world;</span></span>
- <span data-ttu-id="14be9-189">[Os colaboradores](https://github.com/FezVrasta/popper.js/graphs/contributors) de suas solicitações pull e relatórios de erros muito apreciados;</span><span class="sxs-lookup"><span data-stu-id="14be9-189">[The Contributors](https://github.com/FezVrasta/popper.js/graphs/contributors) for their much appreciated Pull Requests and bug reports;</span></span>
- <span data-ttu-id="14be9-190">**você** para a estrela que você dará a este projeto e por ser tão incrível para dar a este projeto uma tentativa🙂</span><span class="sxs-lookup"><span data-stu-id="14be9-190">**you** for the star you'll give to this project and for being so awesome to give this project a try 🙂</span></span>

### <a name="copyright-and-license"></a><span data-ttu-id="14be9-191">Direitos autorais e licença</span><span class="sxs-lookup"><span data-stu-id="14be9-191">Copyright and license</span></span>
<span data-ttu-id="14be9-192">Código e documentação Copyright 2016 **Federico Zivolo**.</span><span class="sxs-lookup"><span data-stu-id="14be9-192">Code and documentation copyright 2016 **Federico Zivolo**.</span></span> <span data-ttu-id="14be9-193">Código lançado sob a [licença MIT](LICENSE.md).</span><span class="sxs-lookup"><span data-stu-id="14be9-193">Code released under the [MIT license](LICENSE.md).</span></span> <span data-ttu-id="14be9-194">Docs lançados no Creative Commons.</span><span class="sxs-lookup"><span data-stu-id="14be9-194">Docs released under Creative Commons.</span></span>
