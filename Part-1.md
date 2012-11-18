Introdu��o � Fabric.js: Parte 1
==================

**[Juriy Zaytsev](http://msdn.microsoft.com/en-us/magazine/jj714178.aspx) | October 14, 2012**

**Tradu��o (pt_BR): [Rodrigo Pandini](http://twitter.com/rodrigopandini) | 18 Novembro, 2012**

Neste artigo irei apresentar a voc�s a Fabric.js - uma poderosa biblioteca Javascript que faz o trabalho com o elemento canvas do HTML5 ficar muito mais f�cil. Fabric oferece um modelo de objetos que faltava para o canvas, assim como um parser para SVG, uma camada de interatividade e todo um suite de outras ferramentas. � um projeto totalmente open-source, licenciado sob MIT, com muitas contribui��es ao longo dos anos.

Eu comecei desenvolvendo a Fabric tr�s anos atr�s depois de descobrir a dificuldade que era trabalhar com a API nativa do canvas. Eu estava criando um editor interativo de desing para a [printio.ru](http://printio.ru/) - minha startup que permite ao usu�rio desenhar sua pr�pria arte para a roupa. Este tipo de interatividade que eu estava procurando s� existia em aplicativos Flash naquela �poca. Hoje em dia, poucas bibliotecas fazem o que � poss�vel fazer com a Fabric, ent�o, vamos dar uma olhada mais de perto.

### Porque Fabric?

Canvas lhe permite criar alguns [gr�ficos](http://artatm.com/2012/01/23-truly-amazing-and-unbelievable-html5-canvas-and-javascript-experiments/) [absolutamente](http://net.tutsplus.com/articles/web-roundups/21-ridiculously-impressive-html5-canvas-experiments/) [incr�veis](http://speckyboy.com/2011/12/07/20-amazing-implementations-of-html5-canvas/) na Web hoje em dia, mas a API que ela oferece � [decepcionantemente de baixo n�vel](http://dev.w3.org/html5/2dcontext). Ela serve para voc� desenhar poucas formas b�sicas e esquec�-las. Se voc� precisa de algum tipo de intera��o, mudar uma foto para algum ponto ou desenhar formas mais complexas, a situa��o muda dramaticamente. Fabric visa resolver este problema.

M�todos nativos do canvas permitem que voc� dispare simples comandos gr�ficos, modificando cegamente todo o bitmap do canvas. Voc� quer desenhar um ret�ngulo? Use fillRect(left, top, width, height). Quer desenhar uma linha? Use a combina��o de moveTo(left, top) e lineTo(x, y). � como se voc� estivesse pintando um quadro com um pincel, passando camadas e mais camadas de tinta �leo ou acr�lica em cima, com pouqu�ssimo controle.

Ao inv�s de operar em t�o baixo n�vel, Fabric prov� um simples mas poderoso modelo de objetos em cima dos m�todos nativos. Ele toma conta do estado do canvas e da renderiza��o e deixa voc� trabalhar com objetos diretamente.

Aqui est� um simples exemplo que demonstra esta diferen�a. Digamos que voc� quer desenhar um ret�ngulo vermelho em algum lugar do canvas. Aqui esta a forma como voc� faria isso com a API nativa do canvas:

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
O c�digo abaixo mostra como fazer a mesma coisa com Fabric. O resultado de ambas as abordagens � exibido na ***Figura 1***.

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
***Figura 1: Ret�ngulo vermelho desenhado com Fabric ou M�todos Nativos do Canvas***

At� este ponto, quase n�o h� diferen�a em tamanho do c�digo - os dois exemplos s�o muito similares. Entretanto, voc� j� pode ver como � diferente a abordagem para se trabalhar com o canvas. Com m�todos nativos, voc� opera com o contexto - um objeto que representa o bitmap do canvas. No Fabric, voc� opera com objetos - voc� instancia-os, altera suas propriedades e adiciona-os ao canvas. Voc� pode ver que estes objetos s�o os cidad�os de primeira classe no territ�rio do Fabric.

Renderizar um ret�ngulo plano � muito simples. Voc� pode ao menos ter alguma divers�o com ele rotacionando-o levemente. Vamos tentar 45 graus, primeiramente usando m�todos nativos do canvas:

```javascript
var canvasEl = document.getElementById('c');
var ctx = canvasEl.getContext('2d');
ctx.fillStyle = 'red';
ctx.translate(100, 100);
ctx.rotate(Math.PI / 180 * 45);
ctx.fillRect(-10, -10, 20, 20);
```
E aqui est� como fazer isso com Fabric (Ver ***Figura 2*** com os resultados).

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
***Figura 2: Ret�ngulo vermelho desenhado com Fabric ou M�todos Nativos do Canvas***

O que est� acontecendo aqui? Tudo que voc� tem que fazer no Fabric � mudar o valor do �ngulo do objeto para 45. Com m�todos nativos, entretanto, um maior trabalho � necess�rio. Lembre-se que voc� n�o opera com objetos. Ao inv�s disso, voc� tem que mudar a posi��o e �ngulo de todo o bitmap do canvas (ctx.translate, ctx.rotate) para adequar �s suas necessidades. Voc� ent�o desenha o seu ret�ngulo novamente, lembrando-se de deslocar o bitmap adequadamente (-10, -10), ent�o ainda � renderizado no ponto 100, 100. Como b�nus, voc� tem que transformar graus em radianos quando estiver rotacionando o bitmap do canvas.

Tenho certeza que voc� est� come�ando a ver porque Fabric existe e o quanto de opera��es de baixo n�vel ele esconde.

Vamos dar uma olhada em um outro exemplo: mantendo a sequ�ncia do estado do canvas.

E se em algum ponto voc� deseja mover o ret�ngulo para uma posi��o levemente diferente no canvas? Como voc� pode fazer isso sem estar apto a operar com objetos? Voc� apenas chamaria outro fillRect no bitmap do canvas? N�o exatamente. Chamando outro comando fillRect, na verdade desenha um ret�ngulo em cima de qualquer coisa que j� esteja desenhada no canvas. Para mover o ret�ngulo, voc� precisa primeiro apagar qualquer conte�do que foi previamente desenhado e ent�o desenhar o ret�ngulo em uma nova localiza��o (ver ***Figura 3***).

```javascript
var canvasEl = document.getElementById('c');
...
ctx.strokRect(100, 100, 20, 20);
...
// erase entire canvas area
ctx.clearRect(0, 0, canvasEl.width, canvasEl.height);
ctx.fillRect(20, 50, 20, 20);
```

Aqui est� como voc� poderia fazer isso com Fabric:


```javascript
var canvas = new fabric.Canvas('c');
...
canvas.add(rect);
...
rect.set({ left: 20, top: 50 });
canvas.renderAll();
```

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev03(en-us,MSDN.10).png">
***Figura 3: Ret�ngulo vermelho desenhado em uma nova localiza��o***

Note uma importante diferen�a: com Fabric, voc� n�o precisa apagar o conte�do antes de tentar modificar qualquer conte�do. Voc� ainda trabalha com objetos simplesmente alterando suas propriedades e ent�o renderiza o canvas novamente para obter uma nova imagem.

### Objetos

Voc� viu na �ltima se��o como trabalhar com ret�ngulos instanciando o construtor fabric.Rect. Fabric, � claro, cobre outras formas b�sicas tamb�m - c�rculos, tri�ngulo, elipses e por ai vai. As formas s�o expostas sob o �namespace� fabric, como fabric.Circle, fabric.Triangle, fabric.Ellipse, etc. Fabric prov� sete formas b�sicas:

- fabric.Circle
- fabric.Ellipse
- fabric.Line
- fabric.Polygon
- fabric.Polyline
- fabric.Rect
- fabric.Triangle

Para desenhar um c�rculo, simplesmente crie um objeto c�rculo e adicione-o ao canvas.

```javascript
var circle = new fabric.Circle({
  radius: 20, fill: 'green', left: 100, top: 100
});
var triangle = new fabric.Triangle({
  width: 20, height: 30, fill: 'blue', left: 50, top: 50
});
canvas.add(circle, triangle);
```

Voc� faz a mesma coisa com qualquer outra forma b�sica. A Figura 4 mostra um exemplo de um c�rculo verde desenhado na localiza��o 100,100 e um tri�ngulo azul em 50,50.

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev04(en-us,MSDN.10).png">
***Figura 4: Um tri�ngulo azul e um c�rculo verde desenhados com Fabric***

### Manipulating objects

Criar objetos gr�ficos - ret�ngulos, c�rculos ou outros - � s� o come�o. Em algum momento, voc� provavelmente vai precisar modificar os seus objetos. Talvez uma determinada a��o vai provocar uma mudan�a de estado ou reproduzir uma anima��o de algum tipo. Ou voc� pode querer alterar as propriedades do objeto (como cor, opacidade, tamanho, posi��o) em certas intera��es do mouse.

Fabric cuida da renderiza��o do canvas e do gerenciamento do estado por voc�. N�s s� precisamos de modificar os objetos por si s�. O exemplo anterior demonstra o m�todo set e como a chamada set({ left:20, top: 50 }) move o objeto da sua posi��o anterior. De um modo similar, voc� pode mudar qualquer propriedade de um objeto.

Como era de se esperar, objetos Fabric tem propriedades relacionadas a posi��o (left, top), dimens�es (width, height), renderiza��o (preenchimento, opacidade, contorno, espessura do contorno), redimensionamento e rota��o (scaleX, scaleY, angle) e invers�o (flipX, flipY). Sim, criar objetos invertidos no Fabric � f�cil como definir a propriedade flip* para true.

Voc� pode ler qualquer uma dessas propriedades via o m�todo get e defin�-las via set. Aqui est� um exemplo de como mudar algumas propriedades do ret�ngulo vermelho. ***Figura 5*** mostra os resultados.

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

Primeiro, o valor do preenchimento � definido para �red�. A pr�xima atribui��o define os valores da espessura e da cor de contorno, dando ao ret�ngulo 5px de espessura e uma cor verde p�lida para ela. Finalmente, o c�digo muda as propriedades �ngulo e invers�o em Y. Note como cada uma das tr�s atribui��es usa uma sintaxe um pouco diferente.

Isso demonstra que o set � um m�todo universal. Voc� provavelmente vai us�-lo muitas vezes, e est� destinado a ser o mais conveniente poss�vel. E sobre os getters? H� um m�todo get gen�rico e tamb�m um n�mero de gets espec�ficos. Para ler a propriedade largura de um objeto, voc� usa get('width') or getWidth(). Para pegar o valor scaleX, voc� usaria get('scaleX'), getScaleX() e por ai vai. H� um m�todo como getWidth ou getScaleX para cada uma das propriedades �p�blicas� do objeto (stroke, strokeWidth, �ngulo e outras).

Voc� deve ter notado nos exemplos anteriores, que objetos foram criados com o mesmo hash de configura��o como o que acabamos de usar no m�todo set. Voc� pode �configurar� um objeto em tempo de cria��o ou usar o m�todo set posteriormente:

```javascript
var rect = new fabric.Rect({ width: 10, height: 20, fill: '#f55', opacity: 0.7 });
// or functionally identical
var rect = new fabric.Rect();
rect.set({ width: 10, height: 20, fill: '#f55', opacity: 0.7 });
```

### Op��es padr�o

At� este momento, voc� deve estar imaginando o que acontece quando voc� cria um objeto sem passar nenhum objeto de �configura��o�. Ele ainda vai ter aquelas propriedades?

Sim. Quando as configura��es espec�ficas s�o omitidas durante a cria��o, os objetos no Fabric sempre tem padr�es definidos de propriedades. Voc� pode usar o seguinte c�digo e ver por voc� mesmo:

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

Este ret�ngulo tem as propriedades definidas como padr�o. Ele � posicionado em 0,0, � preto, totalmente opaco, n�o tem contorno e nem dimens�es (largura e altura s�o 0). Pelo fato de n�o ter dimens�es, voc� n�o pode v�-lo no canvas. Dando a ele qualquer valor positivo para a largura e altura ir� revelar um ret�ngulo preto no canto superior esquerdo do canvas, como mostrado na ***Figura 6***.

<img src="http://msdn.microsoft.com/en-us/magazine/jj714178.aspx#author">
***Figura 6: Como um ret�ngulo padr�o se parece quando definido suas dimens�es***

### Hierarquia e Heran�a

Objetos Fabric n�o existem independentes uns dos outros. Eles seguem uma hierarquia muito precisa. A maioria dos objetos herdam do objeto raiz fabric.Object. O objeto raiz fabric.Object representa (mais ou menos) uma forma bi-dimensional, posicionada em um canvas plano bi-dimensional. Ele � a entidade que tem as propriedades left/top e width/height, bem como uma s�rie de outras caracter�sticas gr�ficas. As propriedades listadas para objetos - fill, stroke, angle, opacity, flip* e outras - s�o comuns a todos os objetos Fabric que herdam de fabric.Object.

Essa heran�a permite a voc� definir m�todos no fabric.Object e compartilh�-los entre todas as �classes� filhas. Por exemplo, se voc� quer ter o m�todo getAngleInRadians em todos os objetos, voc� pode simplesmente cri�-la no fabric.Object.prototype, como segue:

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

Como voc� pode ver, o m�todo imediatamente tornou-se dispon�vel em todas as inst�ncias. Apesar das �classes� filhas herdarem de fabric.Object, elas frequentemente tamb�m definem seus pr�prios m�todos e propriedades. Por exemplo, fabric.Circle precisa de uma propriedade raio e fabric.Image - que n�s vamos examinar daqui a pouco - precisa dos m�todos getElement e setElement para acessar e configurar o elemento HTML <img> da qual a inst�ncia da imagem � origin�ria.

### Canvas

Agora que voc� aprendeu sobre objetos em detalhe, vamos retornar ao canvas.

A primeira coisa que voc� v� em todos os exemplos do Fabric � a cria��o do objeto canvas - new fabric.Canvas(�...�). O objeto fabric.Canvas serve como um empacotador sobre o elemento <canvas> e � respons�vel por gerenciar todos os objetos do Fabric que pertencem ao canvas. Ele recebe o ID do elemento e retorna uma inst�ncia de fabric.Canvas.

Voc� pode adicionar objetos nele, referenciar a partir dele ou remov�-lo, como mostrado aqui:

```javascript
var canvas = new fabric.Canvas('c');
var rect = new fabric.Rect();
canvas.add(rect); // add object
canvas.item(0); // reference fabric.Rect added earlier (first object)
canvas.getObjects(); // get all objects on canvas (rect will be first and only)
canvas.remove(rect); // remove previously-added fabric.Rect
```

Gerenciar objetos � o prop�sito principal da fabric.Canvas, mas ele tamb�m serve como configura��o do elemento HTML <canvas>. Voc� precisa definir a cor ou a imagem de fundo para todo o canvas, recortar todo o conte�do em uma certa �rea, definir uma largura e altura diferentes ou ainda especificar o canvas para ser interativo ou n�o? Todas essas op��es (e outras) podem ser definidas no fabric.Canvas, at� mesmo em tempo de cria��o ou depois. O c�digo abaixo mostra um exemplo.

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

Um dos componentes �nicos contru�dos para a Fabric � a camada de interatividade em cima do modelo de objetos. O modelo de objetos existe para permitir programaticamente acessar e manipular os objetos no canvas, mas do lado de fora - ao n�vel do usu�rio - h� uma forma de manipular estes objetos via mouse (ou via toque nos dispositivos de toque). Assim que voc� inicializa um canvas pela chamada de fabric.Canvas(�...�), � poss�vel selecionar os objetos (ver ***Figura 7***), arrast�-los, redimension�-los ou rotacion�-los e at� mesmo agrup�-los (ver ***Figura 8***) para manipul�-los como um todo de uma vez!

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev08(en-us,MSDN.10).png">
***Figura 7: Ret�ngulo vermelho rotacionado no estado de selecionado (controles vis�veis)***

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev09(en-us,MSDN.10).png">
***Figura 8: Ret�ngulo e C�rculo agrupados (controles vis�veis)***

Se voc� deseja permitir aos usu�rios arrastar alguma coisa no canvas - digamos uma imagem - tudo que voc� precisa fazer � inicializar o canvas e adicionar objetos nele. Nenhuma configura��o adicional ou setup � necess�rio.

Para controlar essa interatividade, voc� pode usar a propriedade Booleana de sele��o do Fabric no objeto canvas em combina��o com a propriedade Booleana individual dos objetos:

```javascript
var canvas = new fabric.Canvas('c');
...
canvas.selection = false; // disable group selection
rect.set('selectable', false); // make object unselectable
```

Mas e se voc� n�o quer a camada de interatividade em tudo?  Neste caso, voc� pode sempre substituir fabric.Canvas por fabric.StaticCanvas. A sintaxe para inicializa��o � absolutamente a mesma:

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

Isso cria uma vers�o �leve� do canvas, sem qualquer l�gica de tratamento de eventos. Voc� ainda tem todo o modelo de objetos para trabalhar - adicionar, remover ou modificar os objetos, assim como mudar qualquer configura��o do canvas. Tudo isso ainda continua funcionando, somente o tratamento de eventos que foi removido.

Mais adiante neste artigo, quando eu falar sobre op��es de constru��o customizada, voc� vai ver que se a StaticCanvas � tudo o que voc� precisa, voc� pode at� mesmo criar uma vers�o mais leve da Fabric. Isso pode ser uma op��o legal se voc� precisa de alguma coisa como gr�ficos n�o interativos ou imagens n�o interativas com filtros para sua aplica��o.

### Imagens

Adicionar ret�ngulos e c�rculos ao canvas � divertido, mas como voc� pode imaginar at� agora, Fabric tamb�m faz o trabalho com imagens muito f�cil. Aqui est� como voc� pode instanciar o objeto fabric.Image e adicion�-lo ao canvas, primeiro em HTML e ent�o em JavaScript:

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

Note que voc� passa um elemento imagem para o construtor fabric.Image. Isso cria uma inst�ncia de fabric.Image que se parece como a imagem do document. Al�m disso, voc� imediatamente define os valores left/top para 100/100, angle para 30 e opacity para 0.85. Uma vez que a imagem � adicionada ao canvas, ela � renderizada no localiza��o 100,100 com o �ngulo de 30 graus e � levemente transparente (ver Figura 9). Nada mal!

***Figura 9: Imagem levemente transparente e rotacionada, renderizada com Fabric***

Se voc� n�o tem uma imagem no document mas somente a URL de uma imagem, voc� pode usar fabric.Image.fromURL:

```javascript
fabric.Image.fromURL('my_image.png', function(oImg) {
  canvas.add(oImg);
});
```

Parece muito simples, n�o �? Basta chamar fabric.Image.fromURL, com a URL de uma imagem, e dar a ela uma callback para ser invocada assim que a imagem � carregada e criada. A fun��o callback recebe o, j� criado, objeto fabric.Image como primeiro argumento. Neste momento, voc� pode adicion�-la ao canvas ou talvez alter�-lo primeiro e ent�o adicion�-lo, como mostrado abaixo: 

```javascript
fabric.Image.fromURL('my_image.png', function(oImg) {
  // scale image down, and flip it, before adding it onto canvas
  oImg.scale(0.5).setFlipX(true);
  canvas.add(oImg);
});
```

### Path and PathGroup

N�s examinamos at� agora formas simples e imagens. E formas com conte�do mais complexo e rico? Conhe�a a Path e PathGroup, uma dupla poderosa.

Paths em Fabric representa um esbo�o de uma forma, que pode ser preenchida, ter contorno e modificada de outras maneiras. Paths consistem em uma s�rie de comandos que essencialmente imitam uma caneta indo de um ponto a outro. Com a ajuda de comandos como move, line, curve e arc, Paths podem formar incr�veis formas complexas. E com a ajuda de grupos de Paths (PathGroup), as possibilidades se abrem ainda mais.

Paths em Fabric se assemelham aos [elementos SVG <path>](http://www.w3.org/TR/SVG/paths.html#PathElement). Eles usam o mesmo conjunto de comandos, podem ser criados a partir de elementos <path> e podem ser serializados para eles. Eu vou descrever mais sobre serializa��o e transforma��o de SVG depois, mas, por enquanto, vale a pena mencionar que voc� provavelmente n�o ir� criar inst�ncias de Path a m�o (ou raramente ir�). Ao inv�s disso, voc� vai usar o parser nativo de SVG da Fabric. Mas para entender o que s�o os objetos Path, vamos criar um simples a m�o (veja a ***Figura 10*** com os resultados)

```javascript
var canvas = new fabric.Canvas('c');
var path = new fabric.Path('M 0 0 L 200 100 L 170 200 z');
path.set({ left: 120, top: 120 });
canvas.add(path);
```

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev11(en-us,MSDN.10).png">
***Figura 10: Path simples renderizado pelo Fabric***

Aqui voc� instanciou o objeto fabric.Path e passou para ele a string com as instru��es do caminho. Isso pode parecer estranho, mas na verdade � f�cil de entender. M representa o comando de mover e diz para a caneta invis�vel para se mover para o ponto 0, 0. L � para linha e faz a caneta desenhar um linha at� o ponto 200, 100. Ent�o outro L cria a linha at� 170, 200. Por �ltimo, z for�a a caneta a fechar o desenho do caminho corrente e finalizar a forma.

Uma vez que fabric.Path � como qualquer outro objeto em Fabric, voc� pode tamb�m alterar algumas de suas propriedades, ou modific�-lo ainda mais, como mostrado aqui e na ***Figura 11***:

```javascript
...
var path = new fabric.Path('M 0 0 L 300 100 L 200 300 z');
...
path.set({ fill: 'red', stroke: 'green', opacity: 0.5 });
canvas.add(path);
```
<img src="http://i.msdn.microsoft.com/jj714178.zaytsev12(en-us,MSDN.10).png">
***Figura 11: Uma Path simples modificada***

Por curiosidade, vamos dar uma olhada em uma forma com uma sintaxe um pouco mais complexa. Voc� vai ver porque criar paths a m�o talvez n�o seja a melhor ideia:

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

Aqui, M ainda representa o comando mover, ent�o a caneta inicia sua jornada de desenhar no ponto 121.32, 0. Em seguida, h� um comando L (line) que traz a caneta para 44.58, 0. At� aqui tudo bem. Agora vem o comando C, que significa �cubic bezier�. Este comando faz a caneta desenhar uma curva bezier a partir do ponto corrente at� 36.67, 0. Ele usa o ponto 29.5, 3.22 como ponto de controle de come�o da linha e 24.31, 8.41 como ponto de controle de fim de linha. Toda esta opera��o � seguida por uma dezena de outros comandos, que finalmente cria a agrad�vel forma de uma seta, como mostrado na ***Figura 12***.

<img src="http://i.msdn.microsoft.com/jj714178.zaytsev13(en-us,MSDN.10).png">
***Figura 12: Path complexa renderizada com Fabric***

H� chances de voc� n�o querer trabalhar com estas aberra��es diretamente. Ao inv�s disso, voc� pode usar algo como os m�todos fabric.loadSVGFromString ou fabric.loadSVGFromURL para carregar todo o arquivo SVG e deixar o parser SVG do Fabric fazer o trabalho de percorrer todos os elementos SVG e criar os objetos Path correspondentes.

Neste contexto, enquanto o objeto Path do Fabric representa um elemento SVG <path>, uma cole��o de paths, frequentemente presente em documentos SVG, � representado como uma inst�ncia de PathGroup (fabric.PathGroup). PathGroup nada mais � do que um grupo de objetos Path e pelo fato de fabric.PathGroup herdar de fabric.Object, ele pode ser adicionado ao canvas assim como qualquer outro objeto e pode ser manipulado da mesma maneira.

Assim como Paths, voc� provavelmente n�o ir� trabalhar com PathGroup diretamente. Mas se voc� trope�ar em alguma ap�s transformar um documento SVG, voc� vai saber exatamente o que � e para que prop�sitos ela serve.

### Conclus�o por agora

Eu s� arranhei a superf�cie do que � poss�vel fazer com Fabric. Agora voc� pode criar facilmente qualquer formas simples, formas complexas ou imagens; adicion�-los ao canvas e modificar da maneira que voc� quiser - suas posi��es, dimens�es, �ngulos, cores, opacidade - voc� define isso.

No pr�ximo artigo desta s�rie, eu vou mostrar como trabalhar com grupos; anima��es; texto; parser, renderiza��o e serializa��o de SVG; eventos; filtro de imagens e muito mais.

Enquanto isso, sinta-se livre para olhar os [demos comentados](http://fabricjs.com/demos/) ou [benchmarks](http://fabricjs.com/benchmarks/), juntar-se a discuss�es no [Stack Overflow](http://stackoverflow.com/questions/tagged/fabricjs), no [grupo](https://groups.google.com/forum/?fromgroups#!forum/fabricjs) ou ir direto para o [docs](http://fabricjs.com/docs/), [wiki](https://github.com/kangax/fabric.js/wiki) ou [source](https://github.com/kangax/fabric.js). Voc� pode tamb�m aprender mais sobre HTML5 Canvas em [MSDN IE Developer Center](http://msdn.microsoft.com/en-us/library/ie/hh771733(v=vs.85).aspx), ou conferir o artigo [An Introduction to the HTML 5 Canvas Element](http://msdn.microsoft.com/en-us/magazine/ff961912.aspx) de Rey Bongo no Script Junkie.

Diverta-se experimentando Fabric! Espero que voc� curta o passeio.
