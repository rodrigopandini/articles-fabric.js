Introdução à Fabric.js: Parte 4
==================

**[Juriy Zaytsev](https://github.com/rodrigopandini/articles-fabric.js/blob/master/Part-1.md#sobre-o-autor) | January 18, 2012**

**Tradução (pt_BR): [Rodrigo Pandini](http://twitter.com/rodrigopandini) | 20 Janeiro, 2013**

Nós cobrimos muitos tópicos nas séries anteriores; desde a manipulação básica de objetos à animações, eventos, filtros, grupos e subclasses. Mas ainda há algumas coisas muito interessantes e úteis a serem discutidas!

### Desenho livre

Se há uma coisa no ```<canvas>``` que realmente brilha é o excelente suporte a desenho livre! Uma vez que o canvas é simplesmente um bitmap 2D - um papel para se pintar - realizar desenho livre é muito natural. E claro, Fabric cuida disso para gente.

O modo de desenho livre é habilitado simplesmente definindo a propriedade ```isDrawingMode``` do canvas Fabric como ```true```. Isso imediatamente faz com que qualquer clique futuro e movimento no canvas seja interpretado como um pincel/brush.

Você pode pintar no canvas quantas vezes você quiser, enquanto ```isDrawingMode``` estiver ```true```. Mas assim que você realiza um movimento, seguido por um evento "mouseup", Fabric dispara o evento "path:created" e transforma a forma recém desenhada em uma instância de fabric.Path!

Se, em algum momento, você define ```isDrawingMode``` de novo para ```false```, você terá todos os objetos path criados ainda presentes no canvas. E uma vez que eles são os bons e velhos objetos ```fabric.Path```, você pode modificá-los da maneira que você quiser - mover, rotacionar, redimensionar, etc.

Há também 2 propriedades disponíveis para customizar o desenho livre - ```freeDrawingColor``` e ```freeDrawingWidth```. Ambos estão disponíveis na instância canvas da Fabric. ```freeDrawingColor``` pode ser qualquer valor regular de cor e representa a cor do brush. ```freeDrawingWidth``` é um número regular de pixel e representa a espessura do brush. 

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img01.png">

***Figura 1: Exemplos de desenho feitos "à mão livre" - várias cores e espessuras***

No futuro próximo, nós estamos planejando adicionar mais opções para o desenho livre - várias versões de brush (por exemplo, estilo spray ou chalk). Também padrões de brush customizados e uma opção para extender com o seu próprio, similar aos filtros de imagens da Fabric.

### Customização

Uma das coisas fantáticas sobre a Fabric é o quão customizável ela é. Você pode modificar dezenas dos vários parâmetros do canvas ou dos objetos do canvas, de modo a fazer as coisas se comportarem exatamente da maneira que você deseja. Vamos das uma olhada em alguns deles.

### Trancando objetos

Cada objeto no canvas pode ser trancado de alguma forma. "lockMovementX", "lockMovementY", "lockRotation", "lockScaling" são propriedades que trancam as ações correspondentes do objeto. Assim, definindo ```object.lockMovementX``` para ```true``` previne o objeto de ser movido horizontalmente. Você pode ainda mover o objeto verticalmente. Similarmente, ```lockRotation``` previne a rotação e ```lockScaling``` - o redimensionamento. Todos estes são acumulativos. Você pode combiná-los juntos da forma que quiser.

### Mudando as bordas e os cantos

Você pode controlar a visibilidade das bordas e dos cantos por meio das propriedades "hasControls" e "hasBorders". Simplesmente defina-os como false e o objeto istantaneamente é renderizado "naked".

```javascript
object.hasBorders = false;
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img02.png">

***Figura 2: Objeto sem as bordas***

```javascript
object.hasControls = false;
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img03.png">

***Figura 3: Objeto sem os cantos***

Você pode também alterar sua aparência tweaking as propriedades "borderColor", "cornerColor", e "cornerSize".

```javascript
object.set({
  borderColor: 'red',
  cornerColor: 'green',
  cornerSize: 6
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img04.png">

***Figura 4: Objeto com as bordas e os cantos customizados***

### Disabilitando a seleção

Você pode desabilitar a seleção de objetos no canvas definindo a propriedade "selection" do canvas como ```false```. Isso previne a seleção de absolutamente tudo exibido no canvas. Se você só precisa que certos objetos sejam não selecionáveis, você pode mudar a propriedade "selectable" dos objetos. Simplesmente defina-a como ```false``` e o objeto perde sua interatividade.

### Customizando a seleção

Agora, e se você não quer desabilitar a seleção, mas ao invés disso alterar sua aparência? Sem problemas.

Há 4 propriedades no canvas que controla sua apresentação - "selectionColor", "selectionBorderColor", "selectionLikeWidth", and "selectionDashArray". Isso deveria ser bem auto explicativo, então vamos ver um exemplo:

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));

canvas.selectionColor = 'rgba(0,255,0,0.3)';
canvas.selectionBorderColor = 'red';
canvas.selectionLineWidth = 5;
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img05.png">

***Figura 5: Exemplo de seleção customizada***

A última propriedade - "selectionDashArray" - é não tão simples. Ela nos permite fazer a seleção com linhas tracejadas. O forma de definir o padrão de traços é especificando os intervalos via um array. Assim, para criar um padrão onde há um traço longo seguido por um curto, nós poderiamos usar alguma coisa do tipo [10, 5] como "selectionDashArray". Isso irá desenhar uma linha que é 10px longa e então saltar 5px, desenhar uma linha de 10px de novo, saltar 5px e assim continua. Se nós usarmos um array [2, 4, 6], o padrão irá será criado desenhando uma linha de 2px, salta 4px, desenha uma linha de 6px, então salta 2px, desenha uma linha de 4px, salta 6px e assim por diante. Você entendeu o sentido. Como exemplo, isso é como o padrão [5, 10] se parece:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img06.png">

***Figura 6: Exemplo de seleção tracejada com o padrão [5, 10]***

### Contorno tracejado

Similarmente a "selectionDashArray" no canvas, todos os objetos Fabric possuem a propriedade "strokeDashArray" responsável pelo padrão tracejado de qualquer contorno realizado em um objeto.
 
```javascript
var rect = new fabric.Rect({
  fill: '#06538e',
  width: 125,
  height: 125,
  stroke: 'red',
  strokeDashArray: [5, 5]
});
canvas.add(rect);
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img07.png">

***Figura 07: Objeto com o contorno tracejado***

### Área clicável

Como você sabe, todos os objetos Fabric possuem uma caixa de contorno que é utilizada para arrastar o objeto, ou rotacioná-lo ou redimesioná-lo, quando os controles/cantos estão presente. Você talvez tenha notado que objetos podem ser arrastados até mesmo clicando em um espaço da caixa de contorno onde não há nada desenhado.

Vejamos esta imagem:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img08.png">

***Figura 8: Objeto e sua caixa de contorno***

Por padrão, todos os objeto Fabric no canvas podem ser arrastados pela caixa de contorno. Entretando, se você quer um comportamento diferente - clicando/arrastando objetos somente pelo seu real conteúdo, você pode usar a propriedade "perPixelTargetFind" do objeto. Simplesmente defina como ```true``` para ter o comportamento desejado.

### Ponto de Rotação

Desda versão 1.0, Fabric usa uma UI alternativa por padrão - objetos podem não mais serem redimensionados e rotacionados ao mesmo tempo. Ao invés disso, há um controle de rotação separado em cada objeto. A propriedade correspondente para este controle é "hasRotationPoint". Você pode customizar a distância relativa ao objeto via a propriedade numérica "rotationPointOffset".

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img09.png">

***Figura 9: Ponto de rotação de um objeto***

### Transformação de objeto

Há um número de outras propriedades relacionadas a transformação disponíveis na Fabric desde a versão 1.0. Uma delas é a "uniScaleTransform". Ela é ```false``` por padrão e pode ser usada para habilitar o redimensionamento não uniforme do objeto; em outras palavras, isso permite alterar as proporções do objeto quando arrastado pelos seus cantos.

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img10.png">

***Figura 10: Transformação de um objeto***

Também há a "centerTransform". ```false``` por padrão, especifica o centro do objeto que devo ser usado como origem da transformação. Quando definido como ```true```, ele difine o mesmo comportamente anterior a versão 1.0, onde o objeto é sempre redimensionado/rotacionado em relação ao seu centro. Desde a versão 1.0, a origem da tranformação é dinâmica, o que permite um controle mais fino quando redimensionar objetos.

O último par de novas propriedades é "originX" e "originY". Definido como "center" por padrão, ele permite alterar a origem da transformação programaticamente. Quando você arrasta os cantos do objeto, é esta propriedade que se altera dinâmicamente sob os panos.

Então quando nós iremos alterá-los manualmente? Por exemplo, quando trabalhamos com objetos do tipo texto. Quando alteramos o texto dinâmicamente, e as dimensões da caixa almentam, "originX" e "originY" ditam para onde a caixa devee crescer. Assim, se você precisa fixar o objeto texto a esquerda, você deve definir "originX" para "left". Para fixá-lo a direita, você deve definir "originX" para "right". E assim vai. Este comportamento se assemelha a "position: absolute" do CSS.

### Canvas background and overlay

Como você deve se lembrar da parte 1, você pode definir uma cor para todo o preenchimento do background do canvas. Simplesmente defina qualquer valor regular de cor para a propriedade "backgroundColor" do canvas.

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));
canvas.backgroundColor = 'rgba(0,0,255,0.3)';
canvas.renderAll();
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img11.png">

***Figura 11: Cor de background do canvas***

Você pode ir ainda mais longe e definir uma imagem como background. Você vai precisar de usar o método ```setBackgroundImage``` para isso, passando uma url e uma função callback opcional, chamada quando completar o carregamento da imagem.

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));
canvas.setBackgroundImage('../assets/pug.jpg', canvas.renderAll.bind(canvas));
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img12.png">

***Figura 12: Imagem de background do canvas***

Finalmente, você também pode definir uma imagem como para sobrepor o canvas, neste caso ela sempre irá aparecer sobre qualquer objeto renderizado no canvas. Simplemente use ```setOverlayImage```, passando uma url e uma função de callback opcional chamada quando o carregamento da imagem estiver completo.

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));
canvas.setOverlayImage('../assets/jail_cell_bars.png', canvas.renderAll.bind(canvas));
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img13.png">

***Figura 13: Imagem de overlay do canvas***

### Fabric no Node.js

Um dos aspectos únicos da Fabric é que ela pode funcionar não somente no cliente (browser), mas também no servidor! Isso pode ser útil quando você quer enviar dados do cliente e criar uma imagem desses dados diretamento no servidor. Ou se você simplesmente quer usar a API Fabric no console - por velocidade, conveniência ou por outras razões.

Vamos ver como configurar o ambiente Node e iniciar a Fabric.

Primeiro, você precisa de instalar o [Node.js](http://nodejs.org/), se você ainda não fez isso. Há algumas formas de instalar o Node, dependendo da sua plataforma. Você pode seguir [estas instruções](http://howtonode.org/how-to-install-nodejs) ou [essas aqui](https://github.com/joyent/node/wiki/Installation).

Uma vez que o Node está instalado, nós precisamos de instalar a biblioteca [node-canvas](https://github.com/LearnBoost/node-canvas). node-canvas é uma implementação do Canvas para Node.js. Ela depende da [Cairo](http://cairographics.org/) - uma biblioteca de gráfico em 2D que pode rodar em Mac, Linux ou Windows. node-canvas tem [instruções de instalação](https://github.com/LearnBoost/node-canvas/wiki) dedicadas dependendo da plataforma que você escolher.

Uma vez que Fabric roda em cima do Node, ela vem como um pacote NPM.  Assim, o próximo passo é instalar o NPM. Você pode encontrar instruções de instalação no [repositório do github](https://github.com/isaacs/npm).

O último passo é instalar o pacote [Fabric](https://npmjs.org/package/fabric), usando NPM. Isso é feito simplesmente rodando o comando ```npm install fabric``` (ou ```npm install -g fabric``` para instalar o pacote globalmente).

Se você rodar o console agora, você deverá ter ambos node-canvas e Fabric disponíveis para uso:

```javascript
> node
...
> typeof require('canvas'); // "function"
> typeof require('fabric'); // "object"
```

Agora que tudo está pronto, nós podemos tentar simplesmente um teste "hello world". Vamos criar um arquivo helloworld.js:

```javascript
var fs = require('fs'),
    fabric = require('fabric').fabric,
    out = fs.createWriteStream(__dirname + '/helloworld.png');

var canvas = fabric.createCanvasForNode(200, 200);
var text = new fabric.Text('Hello world', {
  left: 100,
  top: 100,
  fill: '#f55',
  angle: 15
});
canvas.add(text);

var stream = canvas.createPNGStream();
stream.on('data', function(chunk) {
  out.write(chunk);
});
```

e então rodar ele com ```node helloworld.js```. Abrindo a imagem helloworld.png temos isso:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img14.png">

***Figura 14: Imagem do canvas gerado pela Fabric rodando sobre o Node***

Então, o que temos aqui? Vamos rever as partes importantes deste código.

Primeiro, nós estamos incluindo a Fabric (```fabric = require('fabric').fabric```). Em seguida, nós criamos o bom e velho canvas Fabric, usando o comando ```fabric.createCanvasForNode()``` ao invés do usual new ```fabric.Canvas()```. Este método recebe os parâmetros largura e altura e cria um canvas com estas dimensões (no caso, com 200x200x).

Em seguida há a criação de um objeto (```new fabric.Text()```) e adiçãos dele ao canvas (```canvas.add(text)```).

Tudo isso é uma simples criação do canvas Fabric e renderização do texto nele. Agora, como criamos uma imagem de tudo o que esta renderizado no canvas? Usando o método ```createPNGStream``` disponível diretamente da instância canvas. ```createPNGStream``` retorna um [objeto stream](http://nodejs.org/api/stream.html) do Node e então pode-se colocar um arquivo de imagem usando ```on('data')``` e escrevendo no stream correspondente um arquivo de imagem (```fs.createWriteStream()```).

```fabric.createCanvasForNode``` e ```fabric.Canvas#createPNGStream``` são muito mais do que somente 2 métodos específicos para Node. Tudo mais funciona da mesma forma - você pode ainda criar objetos da forma usual e então adicioná-los ao canvas, modificá-los, renderizá-los, etc. Vale a pena mencionar que quando você cria o canvas via ```fabric.createCanvasForNode``` ele é extendido com a propriedade ```nodeCanvas``` a qual é a referência a instância node-canvas original.

### Servidor Node e Fabric

Como um exemplo, vamos criar um simples servidor node que irá ouvir as requisições vindo com dados da Fabric no formato JSON e criar uma imagem deste dado. O script inteiro tem somente 25 linhas!

```javascript
var fabric = require('fabric').fabric,
    http = require('http'),
    url = require('url'),
    PORT = 8124;

var server = http.createServer(function (request, response) {
  var params = url.parse(request.url, true);
  var canvas = fabric.createCanvasForNode(200, 200);

  response.writeHead(200, { 'Content-Type': 'image/png' });

  canvas.loadFromJSON(params.query.data, function() {
    canvas.renderAll();

    var stream = canvas.createPNGStream();
    stream.on('data', function(chunk) {
      response.write(chunk);
    });
    stream.on('end', function() {
      response.end();
    });
  });
});

server.listen(PORT);
```

A maioria deste código deve soar familiar para você.  está dentro da resposta do servidor. Nós estamos criando um canvas Fabric, carregando os dados JSON nele, renderizando-o e finalmente criando um stream como resultado final da resposta do servidor.

Para testar, vamos pegar os dados para um retângulo verde levemente rotacionado:

```javascript
{"objects":[{"type":"rect","left":103.85,"top":98.85,"width":50,"height":50,"fill":"#9ae759","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1.39,"scaleY":1.39,"angle":30,"flipX":false,"flipY":false,"opacity":0.8,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0}],"background":"rgba(0, 0, 0, 0)"}
```

Codificando como URL, temos:

```javascript
%7B"objects"%3A%5B%7B"type"%3A"rect"%2C"left"%3A103.85%2C"top"%3A98.85%2C"width"%3A50%2C"height"%3A50%2C"fill"%3A"%239ae759"%2C"overlayFill"%3Anull%2C"stroke"%3Anull%2C"strokeWidth"%3A1%2C"strokeDashArray"%3Anull%2C"scaleX"%3A1.39%2C"scaleY"%3A1.39%2C"angle"%3A30%2C"flipX"%3Afalse%2C"flipY"%3Afalse%2C"opacity"%3A0.8%2C"selectable"%3Atrue%2C"hasControls"%3Atrue%2C"hasBorders"%3Atrue%2C"hasRotatingPoint"%3Afalse%2C"transparentCorners"%3Atrue%2C"perPixelTargetFind"%3Afalse%2C"rx"%3A0%2C"ry"%3A0%7D%5D%2C"background"%3A"rgba(0%2C%200%2C%200%2C%200)"%7D
```

E passando para o servidor via parâmetro "data". A resposta imediata, retornando com Content-type "image/png", se parece como isso:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img15.png">

***Figura 13: Imagem do canvas criado e retornado pelo servidor rodadno no Node***

Como você pode ver, trabalhar com Fabric no servidor é muito fácil e simples. Sinta-se livre para fazer experiências com este pequeno código. Tente mudar as dimensões do canvas com os parâmetros da URL ou modificar os dados do cliente antes de retornar a imagem como resposta.

Assim chegamos ao final da parte 4 desta série sobre a Fabric. Eu espero que você esteja agora equipada com conhecimento suficiente para criar alguma coisa interessante, legal, útil, divertida, desafiadora e exitante!

#### Sobre o autor

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/author.png">

Juriy Zaytsev é um apaixonado desenvolvedor JavaScript morando em New York. Ele é ex membro do núcleo Prototype.js, blogueiro em [perfectionkills.com](http://perfectionkills.com/), e criador da biblioteca para canvas [Fabric.js](http://fabricjs.com). No momento, Juriy trabalha em sua startup [Printio.ru](http://printio.ru) e faz a Fabric ainda mais divertida para se usar.

***Encontre Juriy em:***
- Twitter - [@kangax](http://twitter.com/kangax/)
- [Juriy's Blog](http://perfectionkills.com/)
