Introdu��o � Fabric.js: Parte 4
==================

**[Juriy Zaytsev](https://github.com/rodrigopandini/articles-fabric.js/blob/master/Part-1.md#sobre-o-autor) | January 18, 2012**

**Tradu��o (pt_BR): [Rodrigo Pandini](http://twitter.com/rodrigopandini) | 20 Janeiro, 2013**

N�s cobrimos muitos t�picos nas s�ries anteriores; desde a manipula��o b�sica de objetos � anima��es, eventos, filtros, grupos e subclasses. Mas ainda h� algumas coisas muito interessantes e �teis a serem discutidas!

### Desenho livre

Se h� uma coisa no ```<canvas>``` que realmente brilha � o excelente suporte a desenho livre! Uma vez que o canvas � simplesmente um bitmap 2D - um papel para se pintar - realizar desenho livre � muito natural. E claro, Fabric cuida disso para gente.

O modo de desenho livre � habilitado simplesmente definindo a propriedade ```isDrawingMode``` do canvas Fabric como ```true```. Isso imediatamente faz com que qualquer clique futuro e movimento no canvas seja interpretado como um pincel/brush.

Voc� pode pintar no canvas quantas vezes voc� quiser, enquanto ```isDrawingMode``` estiver ```true```. Mas assim que voc� realiza um movimento, seguido por um evento "mouseup", Fabric dispara o evento "path:created" e transforma a forma rec�m desenhada em uma inst�ncia de fabric.Path!

Se, em algum momento, voc� define ```isDrawingMode``` de novo para ```false```, voc� ter� todos os objetos path criados ainda presentes no canvas. E uma vez que eles s�o os bons e velhos objetos ```fabric.Path```, voc� pode modific�-los da maneira que voc� quiser - mover, rotacionar, redimensionar, etc.

H� tamb�m 2 propriedades dispon�veis para customizar o desenho livre - ```freeDrawingColor``` e ```freeDrawingWidth```. Ambos est�o dispon�veis na inst�ncia canvas da Fabric. ```freeDrawingColor``` pode ser qualquer valor regular de cor e representa a cor do brush. ```freeDrawingWidth``` � um n�mero regular de pixel e representa a espessura do brush. 

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img01.png">

***Figura 1: Exemplos de desenho feitos "� m�o livre" - v�rias cores e espessuras***

No futuro pr�ximo, n�s estamos planejando adicionar mais op��es para o desenho livre - v�rias vers�es de brush (por exemplo, estilo spray ou chalk). Tamb�m padr�es de brush customizados e uma op��o para extender com o seu pr�prio, similar aos filtros de imagens da Fabric.

### Customiza��o

Uma das coisas fant�ticas sobre a Fabric � o qu�o customiz�vel ela �. Voc� pode modificar dezenas dos v�rios par�metros do canvas ou dos objetos do canvas, de modo a fazer as coisas se comportarem exatamente da maneira que voc� deseja. Vamos das uma olhada em alguns deles.

### Trancando objetos

Cada objeto no canvas pode ser trancado de alguma forma. "lockMovementX", "lockMovementY", "lockRotation", "lockScaling" s�o propriedades que trancam as a��es correspondentes do objeto. Assim, definindo ```object.lockMovementX``` para ```true``` previne o objeto de ser movido horizontalmente. Voc� pode ainda mover o objeto verticalmente. Similarmente, ```lockRotation``` previne a rota��o e ```lockScaling``` - o redimensionamento. Todos estes s�o acumulativos. Voc� pode combin�-los juntos da forma que quiser.

### Mudando as bordas e os cantos

Voc� pode controlar a visibilidade das bordas e dos cantos por meio das propriedades "hasControls" e "hasBorders". Simplesmente defina-os como false e o objeto istantaneamente � renderizado "naked".

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

Voc� pode tamb�m alterar sua apar�ncia tweaking as propriedades "borderColor", "cornerColor", e "cornerSize".

```javascript
object.set({
  borderColor: 'red',
  cornerColor: 'green',
  cornerSize: 6
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img04.png">

***Figura 4: Objeto com as bordas e os cantos customizados***

### Disabilitando a sele��o

Voc� pode desabilitar a sele��o de objetos no canvas definindo a propriedade "selection" do canvas como ```false```. Isso previne a sele��o de absolutamente tudo exibido no canvas. Se voc� s� precisa que certos objetos sejam n�o selecion�veis, voc� pode mudar a propriedade "selectable" dos objetos. Simplesmente defina-a como ```false``` e o objeto perde sua interatividade.

### Customizando a sele��o

Agora, e se voc� n�o quer desabilitar a sele��o, mas ao inv�s disso alterar sua apar�ncia? Sem problemas.

H� 4 propriedades no canvas que controla sua apresenta��o - "selectionColor", "selectionBorderColor", "selectionLikeWidth", and "selectionDashArray". Isso deveria ser bem auto explicativo, ent�o vamos ver um exemplo:

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));

canvas.selectionColor = 'rgba(0,255,0,0.3)';
canvas.selectionBorderColor = 'red';
canvas.selectionLineWidth = 5;
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img05.png">

***Figura 5: Exemplo de sele��o customizada***

A �ltima propriedade - "selectionDashArray" - � n�o t�o simples. Ela nos permite fazer a sele��o com linhas tracejadas. O forma de definir o padr�o de tra�os � especificando os intervalos via um array. Assim, para criar um padr�o onde h� um tra�o longo seguido por um curto, n�s poderiamos usar alguma coisa do tipo [10, 5] como "selectionDashArray". Isso ir� desenhar uma linha que � 10px longa e ent�o saltar 5px, desenhar uma linha de 10px de novo, saltar 5px e assim continua. Se n�s usarmos um array [2, 4, 6], o padr�o ir� ser� criado desenhando uma linha de 2px, salta 4px, desenha uma linha de 6px, ent�o salta 2px, desenha uma linha de 4px, salta 6px e assim por diante. Voc� entendeu o sentido. Como exemplo, isso � como o padr�o [5, 10] se parece:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img06.png">

***Figura 6: Exemplo de sele��o tracejada com o padr�o [5, 10]***

### Contorno tracejado

Similarmente a "selectionDashArray" no canvas, todos os objetos Fabric possuem a propriedade "strokeDashArray" respons�vel pelo padr�o tracejado de qualquer contorno realizado em um objeto.
 
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

### �rea clic�vel

Como voc� sabe, todos os objetos Fabric possuem uma caixa de contorno que � utilizada para arrastar o objeto, ou rotacion�-lo ou redimesion�-lo, quando os controles/cantos est�o presente. Voc� talvez tenha notado que objetos podem ser arrastados at� mesmo clicando em um espa�o da caixa de contorno onde n�o h� nada desenhado.

Vejamos esta imagem:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img08.png">

***Figura 8: Objeto e sua caixa de contorno***

Por padr�o, todos os objeto Fabric no canvas podem ser arrastados pela caixa de contorno. Entretando, se voc� quer um comportamento diferente - clicando/arrastando objetos somente pelo seu real conte�do, voc� pode usar a propriedade "perPixelTargetFind" do objeto. Simplesmente defina como ```true``` para ter o comportamento desejado.

### Ponto de Rota��o

Desda vers�o 1.0, Fabric usa uma UI alternativa por padr�o - objetos podem n�o mais serem redimensionados e rotacionados ao mesmo tempo. Ao inv�s disso, h� um controle de rota��o separado em cada objeto. A propriedade correspondente para este controle � "hasRotationPoint". Voc� pode customizar a dist�ncia relativa ao objeto via a propriedade num�rica "rotationPointOffset".

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img09.png">

***Figura 9: Ponto de rota��o de um objeto***

### Transforma��o de objeto

H� um n�mero de outras propriedades relacionadas a transforma��o dispon�veis na Fabric desde a vers�o 1.0. Uma delas � a "uniScaleTransform". Ela � ```false``` por padr�o e pode ser usada para habilitar o redimensionamento n�o uniforme do objeto; em outras palavras, isso permite alterar as propor��es do objeto quando arrastado pelos seus cantos.

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img10.png">

***Figura 10: Transforma��o de um objeto***

Tamb�m h� a "centerTransform". ```false``` por padr�o, especifica o centro do objeto que devo ser usado como origem da transforma��o. Quando definido como ```true```, ele difine o mesmo comportamente anterior a vers�o 1.0, onde o objeto � sempre redimensionado/rotacionado em rela��o ao seu centro. Desde a vers�o 1.0, a origem da tranforma��o � din�mica, o que permite um controle mais fino quando redimensionar objetos.

O �ltimo par de novas propriedades � "originX" e "originY". Definido como "center" por padr�o, ele permite alterar a origem da transforma��o programaticamente. Quando voc� arrasta os cantos do objeto, � esta propriedade que se altera din�micamente sob os panos.

Ent�o quando n�s iremos alter�-los manualmente? Por exemplo, quando trabalhamos com objetos do tipo texto. Quando alteramos o texto din�micamente, e as dimens�es da caixa almentam, "originX" e "originY" ditam para onde a caixa devee crescer. Assim, se voc� precisa fixar o objeto texto a esquerda, voc� deve definir "originX" para "left". Para fix�-lo a direita, voc� deve definir "originX" para "right". E assim vai. Este comportamento se assemelha a "position: absolute" do CSS.

### Canvas background and overlay

Como voc� deve se lembrar da parte 1, voc� pode definir uma cor para todo o preenchimento do background do canvas. Simplesmente defina qualquer valor regular de cor para a propriedade "backgroundColor" do canvas.

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));
canvas.backgroundColor = 'rgba(0,0,255,0.3)';
canvas.renderAll();
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img11.png">

***Figura 11: Cor de background do canvas***

Voc� pode ir ainda mais longe e definir uma imagem como background. Voc� vai precisar de usar o m�todo ```setBackgroundImage``` para isso, passando uma url e uma fun��o callback opcional, chamada quando completar o carregamento da imagem.

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));
canvas.setBackgroundImage('../assets/pug.jpg', canvas.renderAll.bind(canvas));
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img12.png">

***Figura 12: Imagem de background do canvas***

Finalmente, voc� tamb�m pode definir uma imagem como para sobrepor o canvas, neste caso ela sempre ir� aparecer sobre qualquer objeto renderizado no canvas. Simplemente use ```setOverlayImage```, passando uma url e uma fun��o de callback opcional chamada quando o carregamento da imagem estiver completo.

```javascript
canvas.add(new fabric.Circle({ radius: 30, fill: '#f55', top: 100, left: 100 }));
canvas.setOverlayImage('../assets/jail_cell_bars.png', canvas.renderAll.bind(canvas));
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img13.png">

***Figura 13: Imagem de overlay do canvas***

### Fabric no Node.js

Um dos aspectos �nicos da Fabric � que ela pode funcionar n�o somente no cliente (browser), mas tamb�m no servidor! Isso pode ser �til quando voc� quer enviar dados do cliente e criar uma imagem desses dados diretamento no servidor. Ou se voc� simplesmente quer usar a API Fabric no console - por velocidade, conveni�ncia ou por outras raz�es.

Vamos ver como configurar o ambiente Node e iniciar a Fabric.

Primeiro, voc� precisa de instalar o [Node.js](http://nodejs.org/), se voc� ainda n�o fez isso. H� algumas formas de instalar o Node, dependendo da sua plataforma. Voc� pode seguir [estas instru��es](http://howtonode.org/how-to-install-nodejs) ou [essas aqui](https://github.com/joyent/node/wiki/Installation).

Uma vez que o Node est� instalado, n�s precisamos de instalar a biblioteca [node-canvas](https://github.com/LearnBoost/node-canvas). node-canvas � uma implementa��o do Canvas para Node.js. Ela depende da [Cairo](http://cairographics.org/) - uma biblioteca de gr�fico em 2D que pode rodar em Mac, Linux ou Windows. node-canvas tem [instru��es de instala��o](https://github.com/LearnBoost/node-canvas/wiki) dedicadas dependendo da plataforma que voc� escolher.

Uma vez que Fabric roda em cima do Node, ela vem como um pacote NPM.  Assim, o pr�ximo passo � instalar o NPM. Voc� pode encontrar instru��es de instala��o no [reposit�rio do github](https://github.com/isaacs/npm).

O �ltimo passo � instalar o pacote [Fabric](https://npmjs.org/package/fabric), usando NPM. Isso � feito simplesmente rodando o comando ```npm install fabric``` (ou ```npm install -g fabric``` para instalar o pacote globalmente).

Se voc� rodar o console agora, voc� dever� ter ambos node-canvas e Fabric dispon�veis para uso:

```javascript
> node
...
> typeof require('canvas'); // "function"
> typeof require('fabric'); // "object"
```

Agora que tudo est� pronto, n�s podemos tentar simplesmente um teste "hello world". Vamos criar um arquivo helloworld.js:

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

e ent�o rodar ele com ```node helloworld.js```. Abrindo a imagem helloworld.png temos isso:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img14.png">

***Figura 14: Imagem do canvas gerado pela Fabric rodando sobre o Node***

Ent�o, o que temos aqui? Vamos rever as partes importantes deste c�digo.

Primeiro, n�s estamos incluindo a Fabric (```fabric = require('fabric').fabric```). Em seguida, n�s criamos o bom e velho canvas Fabric, usando o comando ```fabric.createCanvasForNode()``` ao inv�s do usual new ```fabric.Canvas()```. Este m�todo recebe os par�metros largura e altura e cria um canvas com estas dimens�es (no caso, com 200x200x).

Em seguida h� a cria��o de um objeto (```new fabric.Text()```) e adi��os dele ao canvas (```canvas.add(text)```).

Tudo isso � uma simples cria��o do canvas Fabric e renderiza��o do texto nele. Agora, como criamos uma imagem de tudo o que esta renderizado no canvas? Usando o m�todo ```createPNGStream``` dispon�vel diretamente da inst�ncia canvas. ```createPNGStream``` retorna um [objeto stream](http://nodejs.org/api/stream.html) do Node e ent�o pode-se colocar um arquivo de imagem usando ```on('data')``` e escrevendo no stream correspondente um arquivo de imagem (```fs.createWriteStream()```).

```fabric.createCanvasForNode``` e ```fabric.Canvas#createPNGStream``` s�o muito mais do que somente 2 m�todos espec�ficos para Node. Tudo mais funciona da mesma forma - voc� pode ainda criar objetos da forma usual e ent�o adicion�-los ao canvas, modific�-los, renderiz�-los, etc. Vale a pena mencionar que quando voc� cria o canvas via ```fabric.createCanvasForNode``` ele � extendido com a propriedade ```nodeCanvas``` a qual � a refer�ncia a inst�ncia node-canvas original.

### Servidor Node e Fabric

Como um exemplo, vamos criar um simples servidor node que ir� ouvir as requisi��es vindo com dados da Fabric no formato JSON e criar uma imagem deste dado. O script inteiro tem somente 25 linhas!

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

A maioria deste c�digo deve soar familiar para voc�.  est� dentro da resposta do servidor. N�s estamos criando um canvas Fabric, carregando os dados JSON nele, renderizando-o e finalmente criando um stream como resultado final da resposta do servidor.

Para testar, vamos pegar os dados para um ret�ngulo verde levemente rotacionado:

```javascript
{"objects":[{"type":"rect","left":103.85,"top":98.85,"width":50,"height":50,"fill":"#9ae759","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1.39,"scaleY":1.39,"angle":30,"flipX":false,"flipY":false,"opacity":0.8,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0}],"background":"rgba(0, 0, 0, 0)"}
```

Codificando como URL, temos:

```javascript
%7B"objects"%3A%5B%7B"type"%3A"rect"%2C"left"%3A103.85%2C"top"%3A98.85%2C"width"%3A50%2C"height"%3A50%2C"fill"%3A"%239ae759"%2C"overlayFill"%3Anull%2C"stroke"%3Anull%2C"strokeWidth"%3A1%2C"strokeDashArray"%3Anull%2C"scaleX"%3A1.39%2C"scaleY"%3A1.39%2C"angle"%3A30%2C"flipX"%3Afalse%2C"flipY"%3Afalse%2C"opacity"%3A0.8%2C"selectable"%3Atrue%2C"hasControls"%3Atrue%2C"hasBorders"%3Atrue%2C"hasRotatingPoint"%3Afalse%2C"transparentCorners"%3Atrue%2C"perPixelTargetFind"%3Afalse%2C"rx"%3A0%2C"ry"%3A0%7D%5D%2C"background"%3A"rgba(0%2C%200%2C%200%2C%200)"%7D
```

E passando para o servidor via par�metro "data". A resposta imediata, retornando com Content-type "image/png", se parece como isso:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/img15.png">

***Figura 13: Imagem do canvas criado e retornado pelo servidor rodadno no Node***

Como voc� pode ver, trabalhar com Fabric no servidor � muito f�cil e simples. Sinta-se livre para fazer experi�ncias com este pequeno c�digo. Tente mudar as dimens�es do canvas com os par�metros da URL ou modificar os dados do cliente antes de retornar a imagem como resposta.

Assim chegamos ao final da parte 4 desta s�rie sobre a Fabric. Eu espero que voc� esteja agora equipada com conhecimento suficiente para criar alguma coisa interessante, legal, �til, divertida, desafiadora e exitante!

#### Sobre o autor

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_4/author.png">

Juriy Zaytsev � um apaixonado desenvolvedor JavaScript morando em New York. Ele � ex membro do n�cleo Prototype.js, blogueiro em [perfectionkills.com](http://perfectionkills.com/), e criador da biblioteca para canvas [Fabric.js](http://fabricjs.com). No momento, Juriy trabalha em sua startup [Printio.ru](http://printio.ru) e faz a Fabric ainda mais divertida para se usar.

***Encontre Juriy em:***
- Twitter - [@kangax](http://twitter.com/kangax/)
- [Juriy's Blog](http://perfectionkills.com/)
