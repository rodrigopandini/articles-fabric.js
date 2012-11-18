Introdução à Fabric.js: Parte 1
==================

**[Juriy Zaytsev](http://msdn.microsoft.com/en-us/magazine/jj714178.aspx) | October 14, 2012**

**Tradução (pt_BR): [Rodrigo Pandini](http://twitter.com/rodrigopandini) | 18 Novembro, 2012**

Neste artigo irei apresentar a vocês a Fabric.js - uma poderosa biblioteca Javascript que faz o trabalho com o elemento canvas do HTML5 ficar muito mais fácil. Fabric oferece um modelo de objetos que faltava para o canvas, assim como um parser para SVG, uma camada de interatividade e todo um suite de outras ferramentas. É um projeto totalmente open-source, licenciado sob MIT, com muitas contribuições ao longo dos anos.

Eu comecei desenvolvendo a Fabric três anos atrás depois de descobrir a dificuldade que era trabalhar com a API nativa do canvas. Eu estava criando um editor interativo de desing para a [printio.ru](http://printio.ru/) - minha startup que permite ao usuário desenhar sua própria arte para a roupa. Este tipo de interatividade que eu estava procurando só existia em aplicativos Flash naquela época. Hoje em dia, poucas bibliotecas fazem o que é possível fazer com a Fabric, então, vamos dar uma olhada mais de perto.

### Porque Fabric?

Canvas lhe permite criar alguns [gráficos](http://artatm.com/2012/01/23-truly-amazing-and-unbelievable-html5-canvas-and-javascript-experiments/) [absolutamente](http://net.tutsplus.com/articles/web-roundups/21-ridiculously-impressive-html5-canvas-experiments/) [incríveis](http://speckyboy.com/2011/12/07/20-amazing-implementations-of-html5-canvas/) na Web hoje em dia, mas a API que ela oferece é [decepcionantemente de baixo nível](http://dev.w3.org/html5/2dcontext). Ela serve para você desenhar poucas formas básicas e esquecê-las. Se você precisa de algum tipo de interação, mudar uma foto para algum ponto ou desenhar formas mais complexas, a situação muda dramaticamente. Fabric visa resolver este problema.

Métodos nativos do canvas permitem que você dispare simples comandos gráficos, modificando cegamente todo o bitmap do canvas. Você quer desenhar um retângulo? Use fillRect(left, top, width, height). Quer desenhar uma linha? Use a combinação de moveTo(left, top) e lineTo(x, y). É como se você estivesse pintando um quadro com um pincel, passando camadas e mais camadas de tinta óleo ou acrílica em cima, com pouquíssimo controle.

Ao invés de operar em tão baixo nível, Fabric provê um simples mas poderoso modelo de objetos em cima dos métodos nativos. Ele toma conta do estado do canvas e da renderização e deixa você trabalhar com objetos diretamente.

Aqui está um simples exemplo que demonstra esta diferença. Digamos que você quer desenhar um retângulo vermelho em algum lugar do canvas. Aqui esta a forma como você faria isso com a API nativa do canvas:

```javascript
// reference canvas element (with id="c")
var canvasEl = document.getElementById('c');
// get 2d context to draw on (the "bitmap" mentioned earlier)
var ctx = canvasEl.getContext('2d');
// set fill color of context
ctx.fillStyle = 'red';
// create rectangle at a 100,100 point, with 20x20 dimensions
ctx.fillRect(100, 100, 20, 20);
```
O código abaixo mostra como fazer a mesma coisa com Fabric. O resultado de ambas as abordagens é exibido na ***Figura 1***.

```javascript
// create a wrapper around native canvas element (with id="c")
var canvas = new fabric.Canvas('c');
// create a rectangle object
var rect = new fabric.Rect({
  left: 100,
  top: 100,
  fill: 'red',
  width: 20,
  height: 20
});
// "add" rectangle onto canvas
canvas.add(rect);
```
<img src="http://i.msdn.microsoft.com/jj714178.zaytsev01(en-us,MSDN.10).png">
***Figura 1: Retângulo vermelho desenhado com Fabric ou Métodos Nativos do Canvas***

Até este ponto, quase não há diferença em tamanho do código - os dois exemplos são muito similares. Entretanto, você já pode ver como é diferente a abordagem para se trabalhar com o canvas. Com métodos nativos, você opera com o contexto - um objeto que representa o bitmap do canvas. No Fabric, você opera com objetos - você instancia-os, altera suas propriedades e adiciona-os ao canvas. Você pode ver que estes objetos são os cidadãos de primeira classe no território do Fabric.

Renderizar um retângulo plano é muito simples. Você pode ao menos ter alguma diversão com ele rotacionando-o levemente. Vamos tentar 45 graus, primeiramente usando métodos nativos do canvas:

```javascript
var canvasEl = document.getElementById('c');
var ctx = canvasEl.getContext('2d');
ctx.fillStyle = 'red';
ctx.translate(100, 100);
ctx.rotate(Math.PI / 180 * 45);
ctx.fillRect(-10, -10, 20, 20);
```
E aqui está como fazer isso com Fabric (Ver ***Figura 2*** com os resultados).

```javascript
var canvas = new fabric.Canvas('c');
// create a rectangle with angle=45
var rect = new fabric.Rect({
  left: 100,
  top: 100,
  fill: 'red',
  width: 20,
  height: 20,
  angle: 45
});
canvas.add(rect);
```
<img src="http://i.msdn.microsoft.com/jj714178.zaytsev02(en-us,MSDN.10).png">
***Figura 2: Retângulo vermelho desenhado com Fabric ou Métodos Nativos do Canvas***

O que está acontecendo aqui? Tudo que você tem que fazer no Fabric é mudar o valor do ângulo do objeto para 45. Com métodos nativos, entretanto, um maior trabalho é necessário. Lembre-se que você não opera com objetos. Ao invés disso, você tem que mudar a posição e ângulo de todo o bitmap do canvas (ctx.translate, ctx.rotate) para adequar às suas necessidades. Você então desenha o seu retângulo novamente, lembrando-se de deslocar o bitmap adequadamente (-10, -10), então ainda é renderizado no ponto 100, 100. Como bônus, você tem que transformar graus em radianos quando estiver rotacionando o bitmap do canvas.

Tenho certeza que você está começando a ver porque Fabric existe e o quanto de operações de baixo nível ele esconde.

Vamos dar uma olhada em um outro exemplo: mantendo a sequência do estado do canvas.

E se em algum ponto você deseja mover o retângulo para uma posição levemente diferente no canvas? Como você pode fazer isso sem estar apto a operar com objetos? Você apenas chamaria outro fillRect no bitmap do canvas? Não exatamente. Chamando outro comando fillRect, na verdade desenha um retângulo em cima de qualquer coisa que já esteja desenhada no canvas. Para mover o retângulo, você precisa primeiro apagar qualquer conteúdo que foi previamente desenhado e então desenhar o retângulo em uma nova localização (ver ***Figura 3***).

```javascript
var canvasEl = document.getElementById('c');
...
ctx.strokRect(100, 100, 20, 20);
...
// erase entire canvas area
ctx.clearRect(0, 0, canvasEl.width, canvasEl.height);
ctx.fillRect(20, 50, 20, 20);
```

Aqui está como você poderia fazer isso com Fabric:


```javascript
var canvas = new fabric.Canvas('c');
...
canvas.add(rect);
...
rect.set({ left: 20, top: 50 });
canvas.renderAll();
```

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev03(en-us,MSDN.10).png">
***Figura 3: Retângulo vermelho desenhado em uma nova localização***

Note uma importante diferença: com Fabric, você não precisa apagar o conteúdo antes de tentar modificar qualquer conteúdo. Você ainda trabalha com objetos simplesmente alterando suas propriedades e então renderiza o canvas novamente para obter uma nova imagem.

### Objetos

Você viu na última seção como trabalhar com retângulos instanciando o construtor fabric.Rect. Fabric, é claro, cobre outras formas básicas também - círculos, triângulo, elipses e por ai vai. As formas são expostas sob o “namespace” fabric, como fabric.Circle, fabric.Triangle, fabric.Ellipse, etc. Fabric provê sete formas básicas:

- fabric.Circle
- fabric.Ellipse
- fabric.Line
- fabric.Polygon
- fabric.Polyline
- fabric.Rect
- fabric.Triangle

Para desenhar um círculo, simplesmente crie um objeto círculo e adicione-o ao canvas.

```javascript
var circle = new fabric.Circle({
  radius: 20, fill: 'green', left: 100, top: 100
});
var triangle = new fabric.Triangle({
  width: 20, height: 30, fill: 'blue', left: 50, top: 50
});
canvas.add(circle, triangle);
```

Você faz a mesma coisa com qualquer outra forma básica. A Figura 4 mostra um exemplo de um círculo verde desenhado na localização 100,100 e um triângulo azul em 50,50.

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev04(en-us,MSDN.10).png">
***Figura 4: Um triângulo azul e um círculo verde desenhados com Fabric***

### Manipulating objects

Criar objetos gráficos - retângulos, círculos ou outros - é só o começo. Em algum momento, você provavelmente vai precisar modificar os seus objetos. Talvez uma determinada ação vai provocar uma mudança de estado ou reproduzir uma animação de algum tipo. Ou você pode querer alterar as propriedades do objeto (como cor, opacidade, tamanho, posição) em certas interações do mouse.

Fabric cuida da renderização do canvas e do gerenciamento do estado por você. Nós só precisamos de modificar os objetos por si só. O exemplo anterior demonstra o método set e como a chamada set({ left:20, top: 50 }) move o objeto da sua posição anterior. De um modo similar, você pode mudar qualquer propriedade de um objeto.

Como era de se esperar, objetos Fabric tem propriedades relacionadas a posição (left, top), dimensões (width, height), renderização (preenchimento, opacidade, contorno, espessura do contorno), redimensionamento e rotação (scaleX, scaleY, angle) e inversão (flipX, flipY). Sim, criar objetos invertidos no Fabric é fácil como definir a propriedade flip* para true.

Você pode ler qualquer uma dessas propriedades via o método get e definí-las via set. Aqui está um exemplo de como mudar algumas propriedades do retângulo vermelho. ***Figura 5*** mostra os resultados.

```javascript
var canvas = new fabric.Canvas('c');
...
canvas.add(rect);
rect.set('fill', 'red');
rect.set({ strokeWidth: 5, stroke: 'rgba(100,200,200,0.5)' });
rect.set('angle', 15).set('flipY', true);
```

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev05(en-us,MSDN.10).png">
***Figure 5 Red, Rotated, Stroked Rectangle Drawn with Fabric***

Primeiro, o valor do preenchimento é definido para “red”. A próxima atribuição define os valores da espessura e da cor de contorno, dando ao retângulo 5px de espessura e uma cor verde pálida para ela. Finalmente, o código muda as propriedades ângulo e inversão em Y. Note como cada uma das três atribuições usa uma sintaxe um pouco diferente.

Isso demonstra que o set é um método universal. Você provavelmente vai usá-lo muitas vezes, e está destinado a ser o mais conveniente possível. E sobre os getters? Há um método get genérico e também um número de gets específicos. Para ler a propriedade largura de um objeto, você usa get('width') or getWidth(). Para pegar o valor scaleX, você usaria get('scaleX'), getScaleX() e por ai vai. Há um método como getWidth ou getScaleX para cada uma das propriedades “públicas” do objeto (stroke, strokeWidth, ângulo e outras).

Você deve ter notado nos exemplos anteriores, que objetos foram criados com o mesmo hash de configuração como o que acabamos de usar no método set. Você pode “configurar” um objeto em tempo de criação ou usar o método set posteriormente:

```javascript
var rect = new fabric.Rect({ width: 10, height: 20, fill: '#f55', opacity: 0.7 });
// or functionally identical
var rect = new fabric.Rect();
rect.set({ width: 10, height: 20, fill: '#f55', opacity: 0.7 });
```

### Opções padrão

Até este momento, você deve estar imaginando o que acontece quando você cria um objeto sem passar nenhum objeto de “configuração”. Ele ainda vai ter aquelas propriedades?

Sim. Quando as configurações específicas são omitidas durante a criação, os objetos no Fabric sempre tem padrões definidos de propriedades. Você pode usar o seguinte código e ver por você mesmo:

```javascript
var rect = new fabric.Rect(); // notice no options passed in
rect.getWidth(); // 0
rect.getHeight(); // 0
rect.getLeft(); // 0
rect.getTop(); // 0
rect.getFill(); // rgb(0,0,0)
rect.getStroke(); // null
rect.getOpacity(); // 1
```

Este retângulo tem as propriedades definidas como padrão. Ele é posicionado em 0,0, é preto, totalmente opaco, não tem contorno e nem dimensões (largura e altura são 0). Pelo fato de não ter dimensões, você não pode vê-lo no canvas. Dando a ele qualquer valor positivo para a largura e altura irá revelar um retângulo preto no canto superior esquerdo do canvas, como mostrado na ***Figura 6***.

<img src="http://msdn.microsoft.com/en-us/magazine/jj714178.aspx#author">
***Figura 6: Como um retângulo padrão se parece quando definido suas dimensões***

### Hierarquia e Herança

Objetos Fabric não existem independentes uns dos outros. Eles seguem uma hierarquia muito precisa. A maioria dos objetos herdam do objeto raiz fabric.Object. O objeto raiz fabric.Object representa (mais ou menos) uma forma bi-dimensional, posicionada em um canvas plano bi-dimensional. Ele é a entidade que tem as propriedades left/top e width/height, bem como uma série de outras características gráficas. As propriedades listadas para objetos - fill, stroke, angle, opacity, flip* e outras - são comuns a todos os objetos Fabric que herdam de fabric.Object.

Essa herança permite a você definir métodos no fabric.Object e compartilhá-los entre todas as “classes” filhas. Por exemplo, se você quer ter o método getAngleInRadians em todos os objetos, você pode simplesmente criá-la no fabric.Object.prototype, como segue:

```javascript
fabric.Object.prototype.getAngleInRadians = function() {
  return this.getAngle() / 180 * Math.PI;
};
var rect = new fabric.Rect({ angle: 45 });
rect.getAngleInRadians(); // 0.785...
var circle = new fabric.Circle({ angle: 30, radius: 10 });
circle.getAngleInRadians(); // 0.523...
circle instanceof fabric.Circle; // true
circle instanceof fabric.Object; // true
```

Como você pode ver, o método imediatamente tornou-se disponível em todas as instâncias. Apesar das “classes” filhas herdarem de fabric.Object, elas frequentemente também definem seus próprios métodos e propriedades. Por exemplo, fabric.Circle precisa de uma propriedade raio e fabric.Image - que nós vamos examinar daqui a pouco - precisa dos métodos getElement e setElement para acessar e configurar o elemento HTML <img> da qual a instância da imagem é originária.

### Canvas

Agora que você aprendeu sobre objetos em detalhe, vamos retornar ao canvas.

A primeira coisa que você vê em todos os exemplos do Fabric é a criação do objeto canvas - new fabric.Canvas(‘...’). O objeto fabric.Canvas serve como um empacotador sobre o elemento <canvas> e é responsável por gerenciar todos os objetos do Fabric que pertencem ao canvas. Ele recebe o ID do elemento e retorna uma instância de fabric.Canvas.

Você pode adicionar objetos nele, referenciar a partir dele ou removê-lo, como mostrado aqui:

```javascript
var canvas = new fabric.Canvas('c');
var rect = new fabric.Rect();
canvas.add(rect); // add object
canvas.item(0); // reference fabric.Rect added earlier (first object)
canvas.getObjects(); // get all objects on canvas (rect will be first and only)
canvas.remove(rect); // remove previously-added fabric.Rect
```

Gerenciar objetos é o propósito principal da fabric.Canvas, mas ele também serve como configuração do elemento HTML <canvas>. Você precisa definir a cor ou a imagem de fundo para todo o canvas, recortar todo o conteúdo em uma certa área, definir uma largura e altura diferentes ou ainda especificar o canvas para ser interativo ou não? Todas essas opções (e outras) podem ser definidas no fabric.Canvas, até mesmo em tempo de criação ou depois. O código abaixo mostra um exemplo.

```javascript
var canvas = new fabric.Canvas('c', {
  backgroundColor: 'rgb(100,100,200)',
  selectionColor: 'blue',
  selectionLineWidth: 2
  // ...
});
// or
var canvas = new fabric.Canvas('c');
canvas.backgroundImage = 'http://...';
canvas.onFpsUpdate = function(){ /* ... */ };
// ...
```
### Interatividade

Um dos componentes únicos contruídos para a Fabric é a camada de interatividade em cima do modelo de objetos. O modelo de objetos existe para permitir programaticamente acessar e manipular os objetos no canvas, mas do lado de fora - ao nível do usuário - há uma forma de manipular estes objetos via mouse (ou via toque nos dispositivos de toque). Assim que você inicializa um canvas pela chamada de fabric.Canvas(‘...’), é possível selecionar os objetos (ver ***Figura 7***), arrastá-los, redimensioná-los ou rotacioná-los e até mesmo agrupá-los (ver ***Figura 8***) para manipulá-los como um todo de uma vez!

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev08(en-us,MSDN.10).png">
***Figura 7: Retângulo vermelho rotacionado no estado de selecionado (controles visíveis)***

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev09(en-us,MSDN.10).png">
***Figura 8: Retângulo e Círculo agrupados (controles visíveis)***

Se você deseja permitir aos usuários arrastar alguma coisa no canvas - digamos uma imagem - tudo que você precisa fazer é inicializar o canvas e adicionar objetos nele. Nenhuma configuração adicional ou setup é necessário.

Para controlar essa interatividade, você pode usar a propriedade Booleana de seleção do Fabric no objeto canvas em combinação com a propriedade Booleana individual dos objetos:

```javascript
var canvas = new fabric.Canvas('c');
...
canvas.selection = false; // disable group selection
rect.set('selectable', false); // make object unselectable
```

Mas e se você não quer a camada de interatividade em tudo?  Neste caso, você pode sempre substituir fabric.Canvas por fabric.StaticCanvas. A sintaxe para inicialização é absolutamente a mesma:

```javascript
var staticCanvas = new fabric.StaticCanvas('c');
staticCanvas.add(
  new fabric.Rect({
    width: 10, height: 20,
    left: 100, top: 100,
    fill: 'yellow',
    angle: 30
  }));
```

Isso cria uma versão “leve” do canvas, sem qualquer lógica de tratamento de eventos. Você ainda tem todo o modelo de objetos para trabalhar - adicionar, remover ou modificar os objetos, assim como mudar qualquer configuração do canvas. Tudo isso ainda continua funcionando, somente o tratamento de eventos que foi removido.

Mais adiante neste artigo, quando eu falar sobre opções de construção customizada, você vai ver que se a StaticCanvas é tudo o que você precisa, você pode até mesmo criar uma versão mais leve da Fabric. Isso pode ser uma opção legal se você precisa de alguma coisa como gráficos não interativos ou imagens não interativas com filtros para sua aplicação.

### Imagens

Adicionar retângulos e círculos ao canvas é divertido, mas como você pode imaginar até agora, Fabric também faz o trabalho com imagens muito fácil. Aqui está como você pode instanciar o objeto fabric.Image e adicioná-lo ao canvas, primeiro em HTML e então em JavaScript:

***HTML***

```html
<canvas id="c"></canvas>
<img src="my_image.png" id="my-image">
```

***Javascript***

```javascript
var canvas = new fabric.Canvas('c');
var imgElement = document.getElementById('my-img');
var imgInstance = new fabric.Image(imgElement, {
  left: 100,
  top: 100,
  angle: 30,
  opacity: 0.85
});
canvas.add(imgInstance);
```

Note que você passa um elemento imagem para o construtor fabric.Image. Isso cria uma instância de fabric.Image que se parece como a imagem do document. Além disso, você imediatamente define os valores left/top para 100/100, angle para 30 e opacity para 0.85. Uma vez que a imagem é adicionada ao canvas, ela é renderizada no localização 100,100 com o ângulo de 30 graus e é levemente transparente (ver Figura 9). Nada mal!

***Figura 9: Imagem levemente transparente e rotacionada, renderizada com Fabric***

Se você não tem uma imagem no document mas somente a URL de uma imagem, você pode usar fabric.Image.fromURL:

```javascript
fabric.Image.fromURL('my_image.png', function(oImg) {
  canvas.add(oImg);
});
```

Parece muito simples, não é? Basta chamar fabric.Image.fromURL, com a URL de uma imagem, e dar a ela uma callback para ser invocada assim que a imagem é carregada e criada. A função callback recebe o, já criado, objeto fabric.Image como primeiro argumento. Neste momento, você pode adicioná-la ao canvas ou talvez alterá-lo primeiro e então adicioná-lo, como mostrado abaixo: 

```javascript
fabric.Image.fromURL('my_image.png', function(oImg) {
  // scale image down, and flip it, before adding it onto canvas
  oImg.scale(0.5).setFlipX(true);
  canvas.add(oImg);
});
```

### Path and PathGroup

Nós examinamos até agora formas simples e imagens. E formas com conteúdo mais complexo e rico? Conheça a Path e PathGroup, uma dupla poderosa.

Paths em Fabric representa um esboço de uma forma, que pode ser preenchida, ter contorno e modificada de outras maneiras. Paths consistem em uma série de comandos que essencialmente imitam uma caneta indo de um ponto a outro. Com a ajuda de comandos como move, line, curve e arc, Paths podem formar incríveis formas complexas. E com a ajuda de grupos de Paths (PathGroup), as possibilidades se abrem ainda mais.

Paths em Fabric se assemelham aos [elementos SVG <path>](http://www.w3.org/TR/SVG/paths.html#PathElement). Eles usam o mesmo conjunto de comandos, podem ser criados a partir de elementos <path> e podem ser serializados para eles. Eu vou descrever mais sobre serialização e transformação de SVG depois, mas, por enquanto, vale a pena mencionar que você provavelmente não irá criar instâncias de Path a mão (ou raramente irá). Ao invés disso, você vai usar o parser nativo de SVG da Fabric. Mas para entender o que são os objetos Path, vamos criar um simples a mão (veja a ***Figura 10*** com os resultados)

```javascript
var canvas = new fabric.Canvas('c');
var path = new fabric.Path('M 0 0 L 200 100 L 170 200 z');
path.set({ left: 120, top: 120 });
canvas.add(path);
```

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev11(en-us,MSDN.10).png">
***Figura 10: Path simples renderizado pelo Fabric***

Aqui você instanciou o objeto fabric.Path e passou para ele a string com as instruções do caminho. Isso pode parecer estranho, mas na verdade é fácil de entender. M representa o comando de mover e diz para a caneta invisível para se mover para o ponto 0, 0. L é para linha e faz a caneta desenhar um linha até o ponto 200, 100. Então outro L cria a linha até 170, 200. Por último, z força a caneta a fechar o desenho do caminho corrente e finalizar a forma.

Uma vez que fabric.Path é como qualquer outro objeto em Fabric, você pode também alterar algumas de suas propriedades, ou modificá-lo ainda mais, como mostrado aqui e na ***Figura 11***:

```javascript
...
var path = new fabric.Path('M 0 0 L 300 100 L 200 300 z');
...
path.set({ fill: 'red', stroke: 'green', opacity: 0.5 });
canvas.add(path);
```
<img src="http://i.msdn.microsoft.com/jj714178.zaytsev12(en-us,MSDN.10).png">
***Figura 11: Uma Path simples modificada***

Por curiosidade, vamos dar uma olhada em uma forma com uma sintaxe um pouco mais complexa. Você vai ver porque criar paths a mão talvez não seja a melhor ideia:

```javascript
...
var path = new fabric.Path('M121.32,0L44.58,0C36.67,0,29.5,3.22,24.31,8.41\
c-5.19,5.19-8.41,12.37-8.41,20.28c0,15.82,12.87,28.69,28.69,28.69c0,0,4.4,\
0,7.48,0C36.66,72.78,8.4,101.04,8.4,101.04C2.98,106.45,0,113.66,0,121.32\
c0,7.66,2.98,14.87,8.4,20.29l0,0c5.42,5.42,12.62,8.4,20.28,8.4c7.66,0,14.87\
-2.98,20.29-8.4c0,0,28.26-28.25,43.66-43.66c0,3.08,0,7.48,0,7.48c0,15.82,\
12.87,28.69,28.69,28.69c7.66,0,14.87-2.99,20.29-8.4c5.42-5.42,8.4-12.62,8.4\
-20.28l0-76.74c0-7.66-2.98-14.87-8.4-20.29C136.19,2.98,128.98,0,121.32,0z');
canvas.add(path.set({ left: 100, top: 200 }));
```

Aqui, M ainda representa o comando mover, então a caneta inicia sua jornada de desenhar no ponto 121.32, 0. Em seguida, há um comando L (line) que traz a caneta para 44.58, 0. Até aqui tudo bem. Agora vem o comando C, que significa “cubic bezier”. Este comando faz a caneta desenhar uma curva bezier a partir do ponto corrente até 36.67, 0. Ele usa o ponto 29.5, 3.22 como ponto de controle de começo da linha e 24.31, 8.41 como ponto de controle de fim de linha. Toda esta operação é seguida por uma dezena de outros comandos, que finalmente cria a agradável forma de uma seta, como mostrado na ***Figura 12***.

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev13(en-us,MSDN.10).png">
***Figura 12: Path complexa renderizada com Fabric***

Há chances de você não querer trabalhar com estas aberrações diretamente. Ao invés disso, você pode usar algo como os métodos fabric.loadSVGFromString ou fabric.loadSVGFromURL para carregar todo o arquivo SVG e deixar o parser SVG do Fabric fazer o trabalho de percorrer todos os elementos SVG e criar os objetos Path correspondentes.

Neste contexto, enquanto o objeto Path do Fabric representa um elemento SVG <path>, uma coleção de paths, frequentemente presente em documentos SVG, é representado como uma instância de PathGroup (fabric.PathGroup). PathGroup nada mais é do que um grupo de objetos Path e pelo fato de fabric.PathGroup herdar de fabric.Object, ele pode ser adicionado ao canvas assim como qualquer outro objeto e pode ser manipulado da mesma maneira.

Assim como Paths, você provavelmente não irá trabalhar com PathGroup diretamente. Mas se você tropeçar em alguma após transformar um documento SVG, você vai saber exatamente o que é e para que propósitos ela serve.

### Conclusão por agora

Eu só arranhei a superfície do que é possível fazer com Fabric. Agora você pode criar facilmente qualquer formas simples, formas complexas ou imagens; adicioná-los ao canvas e modificar da maneira que você quiser - suas posições, dimensões, ângulos, cores, opacidade - você define isso.

No próximo artigo desta série, eu vou mostrar como trabalhar com grupos; animações; texto; parser, renderização e serialização de SVG; eventos; filtro de imagens e muito mais.

Enquanto isso, sinta-se livre para olhar os [demos comentados](http://fabricjs.com/demos/) ou [benchmarks](http://fabricjs.com/benchmarks/), juntar-se a discussões no [Stack Overflow](http://stackoverflow.com/questions/tagged/fabricjs), no [grupo](https://groups.google.com/forum/?fromgroups#!forum/fabricjs) ou ir direto para o [docs](http://fabricjs.com/docs/), [wiki](https://github.com/kangax/fabric.js/wiki) ou [source](https://github.com/kangax/fabric.js). Você pode também aprender mais sobre HTML5 Canvas em [MSDN IE Developer Center](http://msdn.microsoft.com/en-us/library/ie/hh771733(v=vs.85).aspx), ou conferir o artigo [An Introduction to the HTML 5 Canvas Element](http://msdn.microsoft.com/en-us/magazine/ff961912.aspx) de Rey Bongo no Script Junkie.

Diverta-se experimentando Fabric! Espero que você curta o passeio.
