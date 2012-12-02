Introdução à Fabric.js: Parte 2
==================

**[Juriy Zaytsev](https://github.com/rodrigopandini/articles-fabric.js/blob/master/Part-1.md#sobre-o-autor) | November 29, 2012**

**Tradução (pt_BR): [Rodrigo Pandini](http://twitter.com/rodrigopandini) | 2 Dezembro, 2012**

No primeiro [artigo desta série](https://github.com/rodrigopandini/articles-fabric.js/blob/master/Part-1.md), eu mostrei as razões para se usar Fabric.js, como o modelo de objetos e a hierarquia deles e os diferente tipos de entidades disponíveis na Fabric - formas simples, imagens e paths complexas. Eu também descrevi como fazer operações simples com objetos Fabric no canvas. Agora que a maioria dos princípios estão fora do caminho, vamos começar a diversão.


### Animação

Nenhuma biblioteca respeitável de canvas segue em frente sem facilitar a animação e Fabric não é exceção. Dado o poderoso modelo de objetos e as facilidades gráficas da Fabric, seria vergonhoso não ter facilitadores de animação nela.

Lembra como é fácil alterar uma propriedade de algum objeto? Você simplesmente chama o método set passando o valor correspondente:

```javascript
rect.set('angle', 45);
```

Animar um objeto é igualmente fácil. Cada objeto Fabric tem seu próprio método de animação que, bem... anima o objeto:

```javascript
rect.animate('angle', 45, {
  onChange: canvas.renderAll.bind(canvas)
});
```

O primeiro argumento é a propriedade que se deseja animar e o segundo argumento é o valor final da animação. Se um retângulo tem um ângulo de -15° e você passa 45 no segundo argumento, o retângulo é animado de -15° até 45°. O terceiro argumento é opcional, um objeto que especifica detalhadamente a animação, como duração, funções de retorno (callbacks), easing e assim por diante. Eu vou mostrar alguns exemplos sobre isso em breve.

Uma característica conveniente do método de animação é que ela suporta valores relativos. Por exemplo, se você quer animar a propriedade left de um objeto por 100px, você pode fazer dessa forma:

```javascript
rect.animate('left', '+100', { onChange: canvas.renderAll.bind(canvas) });
```

Do mesmo jeito, rotacionar um objeto em 5 graus no sentido anti-horário pode ser feito assim:

```javascript
rect.animate('angle', '-5', { onChange: canvas.renderAll.bind(canvas) });
```

Talvez você esteja se perguntando porque eu sempre especifiquei uma função de retorno onChange aqui. Como eu mencionei, o terceiro argumento é opcional, mas chamando canvas.renderAll em cada quadro da animação é o que lhe permite ver a animação atual. Quando você chama o método de animação, ele anima somente o valor da propriedade ao longo do tempo, seguindo o algoritmo especificado (por exemplo, easing). Assim, rect.animate(‘angle’, 45) muda o ângulo do objeto, mas não “re-renderiza” o canvas depois de cada mudança do ângulo. E claro, você precisa desta “re-renderização” para ver a animação.

Lembre-se que há um inteiro modelo de objeto sob a superfície do canvas. Objetos tem suas próprias propriedades e relacionamentos e o canvas é responsável somente por projetar a existência destes objetos para fora do mundo.

A razão pela qual o método animate não “re-renderiza” o canvas após cada alteração é a performance. Ao final, você pode ter centenas ou milhares de objetos animados no canvas e não seria muito inteligente se cada um deles tratasse de renderizar a tela. Na maioria das vezes, você provavelmente precisa especificar explicitamente canvas.renderAll como a função de retorno onChange.

Outras opções que você pode passar para a animação são as seguintes:

-   ***from***    Permite a você especificar o valor de começo da propriedade a ser animada (se você não quer usar o valor corrente)
-   ***duration***    O padrão é 500 ms. Esta opção pode ser usada para mudar a duração da animação.
-   ***onComplete***    A função de retorno que é invocada quando a animação termina.
-   ***easing***    A função easing

Todas estas propriedades deveriam ser auto explicáveis, exceto talvez a easing. Vamos examinar melhor ela.
Por padrão, a animação usa uma função linear para animar. Se isso não é o que você precisa, há uma enorme quantidade de opções para easing disponíveis em fabric.util.ease. Por exemplo, se você quer mover um objeto para a direita ao estilo bouncy (quicando), faça isso:

```javascript
rect.animate('left', 500, {
  onChange: canvas.renderAll.bind(canvas),
  duration: 1000,
  easing: fabric.util.ease.easeOutBounce
});
```

Note que fabric.util.ease.easeOutBounce é uma opção de easing. Outras opções notáveis incluem easeInCubic, easeOutCubic, easeInElastic, easeOutElastic, easeInBounce e easeOutExpo. Só para te dar uma ideia do que é possível fazer com animação em Fabric, você pode animar o ângulo de um objeto para fazê-lo rotacionar; animar as propriedades left e top para fazê-lo se mover; animar a largura e altura para fazê-lo encolher e crescer; animar a opacidade para fazê-lo aparecer ou emaecer (apagar); etc.


### Filtro de imagens

No primeiro artigo desta série, você viu como trabalhar com imagens em Fabric. Há o construtor fabric.Image que aceita uma elemento imagem. Também há o método fabric.Image.fromURL o qual cria uma instância de uma imagem a partir de uma string de URL. Qualquer uma destas imagens podem ser postas e renderizadas no canvas como qualquer outro objeto.

Mas tão legal quanto se trabalhar com imagens é, ainda mais legal, aplicar filtros de imagens nelas. Fabric dispõe de uns poucos filtros por padrão (você pode ver eles [aqui](http://fabricjs.com/image-filters/)) e faz com que a definição de seus próprios filtros fique fácil. Alguns dos filtros nativos que você talvez já esteja familiar são o filtro para remover o branco de fundo, o filtro para tons de cinza ou filtros para de inversão ou brilho. Outros que talvez sejam menos familiares como transparência em gradiente, sépia e noise.

Cada instância de fabric.Image tem a propriedade filters, que é um simples array de filtros. Cada um dos filtros neste array é uma instância de um dos filtros de Fabric ou uma instância de um filtro customizável.

Aqui está o código que você usa para criar uma imagem em tons de cinza. A ***Figura 1*** mostra o resultado.

```javascript
fabric.Image.fromURL('pug.jpg', function(img) {
  // add filter
  img.filters.push(new fabric.Image.filters.Grayscale());
  // apply filters and re-render canvas when done
  img.applyFilters(canvas.renderAll.bind(canvas));
  // add image onto canvas
  canvas.add(img);
});
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img01.png">

***Figura 1. Aplicando filtro de tons de cinza a imagem***

E aqui está como criar a versão sépia de uma imagem, o que resulta em uma imagem com o efeito exibido na ***Figura 2***.

```javascript
fabric.Image.fromURL('pug.jpg', function(img) {
  img.filters.push(new fabric.Image.filters.Sepia());
  img.applyFilters(canvas.renderAll.bind(canvas));
  canvas.add(img);
});
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img02.png">

***Figura 2. Aplicando filtro sépia a imagem***

Como a propriedade filters é um simples array, você pode fazer qualquer operação que deseja com ele da forma usual - remover um filtro (via pop, splice ou shift), adicionar um filtro (via push, splice, unshift), ou ainda combinar múltiplos filtros. Qualquer filtro presente no array de filtros será aplicado um por um quando você chamar applyFilters.
Aqui está como você cria uma imagem com ambos sépia e brilho. A ***Figura 3*** mostra o resultados.

```javascript
fabric.Image.fromURL('pug.jpg', function(img) {
  img.filters.push(
    new fabric.Image.filters.Sepia(),
    new fabric.Image.filters.Brightness({ brightness: 100 }));
  img.applyFilters(canvas.renderAll.bind(canvas));
  canvas.add(img);
});
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img03.png">

***Figure 3. Combiando os filtros sépia e brilho na imagem***

Note que eu também passei o objeto { brightness: 100 } para o filtro brilho. Isso porque alguns filtros podem ser aplicados sem nenhuma configuração adicional (por exemplo, grayscale, invert, sepia) e outros provêem um controle mais fino para o seu comportamento. Para o filtro brilho é o nível atual de brilho (0 - 255). Para o filtro noise é o valor de noise (0-1000). Para o filtro de remover branco são os valores de limiar e distância. E assim por diante.

Agora que você está familiar com os filtros Fabric é hora de sair da caixa e criar o seu próprio. O template para criar um filtro é muito simples. Você precisa criar a classe e então definir um método applyTo. Opcionalmente, você pode dar ao filtro o método toJSON (para suportar a serialização em JSON) ou o método initialize (para suporte aos parâmetros opcionais). Abaixo está um exemplo do código, com os resultados exibidos na ***Figura 4***.

```javascript
fabric.Image.filters.Redify = fabric.util.createClass({
  type: 'Redify',
  applyTo: function(canvasEl) {
    var context = canvasEl.getContext('2d'),
      imageData = context.getImageData(0, 0, 
        canvasEl.width, canvasEl.height),
      data = imageData.data;
    for (var i = 0, len = data.length; i < len; i += 4) {
      data[i + 1] = 0;
      data[i + 2] = 0;
    }
    context.putImageData(imageData, 0, 0);
  }
});
fabric.Image.filters.Redify.fromObject = function(object) {
  return new fabric.Image.filters.Redify(object);
};
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img04.png">

***Figura 4. Aplicando um filtro customizável a imagem***

Sem entrar muito em detalhes no código, a principal ação acontece no loop, onde eu substituí os componentes verde (data[i+1]) e azul (data[i+1]) de cada pixel por 0, removendo-os essencialmente. O valor do componente vermelho do padrão RGB permanece intacto, essencialmente pintando toda a imagem de vermelho. 

Como você pode ver, no método applyTo é passado o elemento canvas principal, representando toda a imagem. Com isso, você percorre os pixels (getImageData().data), modificando-os da forma que você quiser.


### Cores

Se você está mais confortável em trabalhar com cores em hexadecimal, RGB ou RGBA, Fabric provê uma base sólida de cores para ajudar você a se expressar mais naturalmente. Aqui estão algumas formas com as quais você pode definir uma cor em Fabric:

```javascript
new fabric.Color('#f55');
new fabric.Color('#123123');
new fabric.Color('356735');
new fabric.Color('rgb(100,0,100)');
new fabric.Color('rgba(10, 20, 30, 0.5)');
```

Conversões também são muito simples. O método toHex() converte a instância de cor para a representação em hexadecimal, toRGB() para cores em RGB e toRGBA() para cores em RGB com o canal alpha (transparência).

```javascript
new fabric.Color('#f55').toRgb(); // "rgb(255,85,85)"
new fabric.Color('rgb(100,100,100)').toHex(); // "646464"
new fabric.Color('fff').toHex(); // "FFFFFF"
```

Conversão não é somente um passo simples que você pode fazer com as cores. Você pode também sobrepor uma cor com outra ou torná-la em uma versão em tons de cinza.

```javascript
var redish = new fabric.Color('#f55');
var greenish = new fabric.Color('#5f5');
redish.overlayWith(greenish).toHex(); // "AAAA55"
redish.toGrayscale().toHex(); // "A1A1A1"
```


### Gradientes

Uma forma ainda mais expressiva para se trabalhar com cores é via gradientes. Gradientes permitem que você misture uma cor com outra, criando um efeito gráfico deslumbrante.

Chamar setGradientFill é como definir o valor de preenchimento de um objeto, exceto que você preenche o objeto com um gradiente ao invés de uma única cor. 

Abaixo está um código de exemplo, com o efeito visual exibido na ***Figura 5***.

```javascript
var circle = new fabric.Circle({
  left: 100,
  top: 100,
  radius: 50
});
circle.setGradientFill({
  x1: 0,
  y1: 0,
  x2: 0,
  y2: circle.height,
  colorStops: {
    0: '#000',
    1: '#fff'
  }
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img05.png">

***Figura 5. Aplicando um preenchimento de gradiente ao objeto***

Neste exemplo, eu criei um círculo na localização 100,100 com 50px de raio. Eu então defini o seu preenchimento com um gradiente do preto para o branco que se estende por toda a altura deste círculo.

O argumento passado para o método é um objeto com as opções, o qual se espera dois pares de coordenadas (x1, y1 e x2, y2), assim como um objeto colorStops. 

As coordenadas especificam onde um gradiente começa e onde ele termina. O objeto colorStops especifica com quais cores o gradiente é feito. Você pode definir quantas cores de parada você quiser, ao longo do intervalo de 0 a 1 (por exemplo, 0, 0.1, 0.3, 0.5, 0.75, 1). Zero (0) representa o início do gradiente e 1 representa o fim.

Aqui está o código que cria um gradiente da esquerda para a direita, do vermelho para o azul. A ***Figura 6*** mostra o resultado.

```javascript
circle.setGradientFill({
  x1: 0,
  y1: circle.height / 2,
  x2: circle.width,
  y2: circle.height / 2,
  colorStops: {
    0: "red",
    1: "blue"
  }
});
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img06.png">

***Figura 6. Um gradiente criado usando Color Stops***

O código abaixo mostra um gradiente de arco-íris com cinco cores de paradas, com as cores se estendendo por um intervalo de 20 por cento. A Figura 7 mostra o resultado.

```javascript
circle.setGradientFill({
  x1: 0,
  y1: circle.height / 2,
  x2: circle.width,
  y2: circle.height / 2,
  colorStops: {
    0: "red",
    0.2: "orange",
    0.4: "yellow",
    0.6: "green",
    0.8: "blue",
    1: "purple"
  }
});
```
<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img07.png">

***Figura 7. Um gradiente ao estilo arco-íris***

Qual versão legal você pode fazer?


### Texto

E se você quiser exibir não somente imagens ou formas vetorias no canvas mas também texto? Fabric também tem objetos do tipo fabric.Text.

Há duas razões para prover abstração de texto na Fabric. Primeiro, ela permite que você trabalhe com o texto ao estilo orientado a objetos. Métodos nativos do canvas - como usual - só permitem que você trabalhe com o preenchimento ou o contorno em muito baixo nível. Criando instâncias de objetos fabric.Text, você pode trabalhar com o texto da mesma forma que você trabalha com qualquer outro objeto Fabric - movê-lo, redimencioná-lo, alterar suas propriedades e assim por diante.

A segunda razão é para prover funcionalidade mais ricas do que o elemento canvas pode nos dá. Algumas adições da Fabric incluem:

- ***Suporte a multilinhas***    Métods nativos de texto, infelizmente, simplesmente ignoram novas linhas.
- ***Alinhamenteo de texto***    Esquerda, centro, direita. Útil quando estamos trabalhando com texto de múltiplas linhas.
- ***Background de texto****    Background também respeitam alinhamento de texto.
- ***Decoração de texto***    Sublinhado, sobre linha e traçado.
- ***Altura de linha***    Útil qunado trabalhamos com mútiplas linhas de texto.

Aqui está um exemplo “hello world”:

```javascript
var text = new fabric.Text('hello world', { left: 100, top: 100 });
canvas.add(text);
```

É isso! Exibir um texto no canvas é simplesmente adicionar uma instância de fabric.Text em um lugar específico. Como você pode ver, o único parâmetro requerido é a string atual do texto. O segundo argumento é o objeto usual de opções, o qual pode ter qualquer uma das propriedades usuais, como esquerda, topo, preenchimento e assim por diante.

Mas, claro, objetos do tipo texto tem suas próprias propriedades relacionadas. Vamos ver algumas delas.

###### fontFamily

Definido como Times New Roman por padrão, a propriedade fontFamily permite que você mude a família da fonte a ser usada para renderizar o objeto texto. Mudando a propriedade imediatamente renderiza o texto com a nova fonte. A ***Figura 8*** mostra o efeito criado usando o seguinte código:

```javascript
var comicSansText = new fabric.Text("I'm in Comic Sans", {
  fontFamily: 'Comic Sans'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img08.png">

***Figura 8. Uma mudança da propriedade fontFamily***

###### fontSize

fontSize controla o tamanho do texto renderizado. Note que diferente de outros objetos em Fabric, você não pode mudar as propriedades largura e a altura diretamente. Ao invés disso, você precisa de mudar o valor de fontSize para fazer o texto maior, como você pode ver na ***Figura 9***. (Desta forma, ou você pode usar as propriedades scaleX, scaleY)

```javascript
var text40 = new fabric.Text("I'm at fontSize 40", {
  fontSize: 40
});
var text20 = new fabric.Text("I'm at fontSize 20", {
  fontSize: 20
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img09.png">

***Figura 9. Controlando o tamanho da fonte***

###### fontWeight

fontWeight permite que você faça o texto mais grosso ou mais fino. Assim como em CSS, você usa as palavras chaves como normal ou bold - veja a ***Figura 10*** por exemplo) ou números (100, 200, 300, 400, 600, 800). A possibilidade de você usar certos pesos irá depender da disponibilidade do peso na fonte escolhida. Se você está usando uma fonte remota, você precisa ter certeza que você disponibilizou ambas, normal e bold (assim como qualquer outro peso requerido), nas definições da fonte.

```javascript
var normalText = new fabric.Text("I'm a normal text", {
  fontWeight: 'normal'
});
var boldText = new fabric.Text("I'm at bold text", {
  fontWeight: 'bold'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img10.png">

***Figura 10. Peso de fonte pode ser controlado por palavras chaves ou por valores numéricos***

###### textDecoration

Você usa textDecoration para adicionar sublinhado, sobrelinha ou traçado no texto. De novo, isto é similar ao CSS, mas Fabric vai um pouco mais a diante e permite que você use qualquer combinação destas decorações juntas. Assim, você pode ter um texto com ambas sublinhado e sobrelinha, sublinhado e traçado, e assim por diante, como você pode ver na ***Figura 11***.

```javascript
var underlineText = new fabric.Text("I'm underlined text", {
  textDecoration: 'underline'
});
var strokeThroughText = new fabric.Text("I'm stroke-through text", {
  textDecoration: 'line-through'
});
var overlineText = new fabric.Text("I'm overlined text", {
  textDecoration: 'overline'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img11.png">

***Figure 11. Examples of text decorations***

###### textShadow

textShadows consiste em quatro componentes: cor, distância horizontal, distância vertical, e tamanho do desfoque. Este efeitos devem ser muito familiares se você já trabalhou com sombras em CSS. Muitas combinações são possíveis alterando estes valores (veja a ***Figura 12***).

```javascript
var shadowText1 = new fabric.Text("I'm a text with shadow", {
  textShadow: 'rgba(0,0,0,0.3) 5px 5px 5px'
});
var shadowText2 = new fabric.Text("And another shadow", {
  textShadow: 'rgba(0,0,0,0.2) 0 0 5px'
});
var shadowText3 = new fabric.Text("Lorem ipsum dolor sit", {
  textShadow: 'green -5px -5px 3px'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img12.png">

***Figura 12. Exemplos de sombras no textos***

###### fontStyle

A propriedade fontStyle pode ter dois valores: normal ou itálico. Isso é similar a propriedade CSS de mesmo nome. O seguinte código mostra alguns exemplos de uso fontStyle e a ***Figura 13*** mostra os resultados.

```javascript
var italicText = new fabric.Text("A very fancy italic text", {
  fontStyle: 'italic',
  fontFamily: 'Delicious'
});
var anotherItalicText = new fabric.Text("another italic text", {
  fontStyle: 'italic',
  fontFamily: 'Hoefler Text'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img13.png">

***Figura 13. Exemplos de estilo itálico nas fontes***

###### strokeStyle e strokeWidth

Combinando strokeStyle (cor de contorno) e strokeWidth (sua largura), você pode fazer alguns efeitos interessantes no texto, como exibido na ***Figura 14***. 

Aqui estão alguns códigos de exemplos:

```javascript
var textWithStroke = new fabric.Text("Text with a stroke", {
  strokeStyle: '#ff1318',
  strokeWidth: 1
});
var loremIpsumDolor = new fabric.Text("Lorem ipsum dolor", {
  fontFamily: 'Impact',
  strokeStyle: '#c3bfbf',
  strokeWidth: 3
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img14.png">

***Figura 14. Efeitos de texto usando strokeStyle e strokeWidth***

###### textAlign

Alinhamento de texto é útil quando você está trabalhando com objetos texto de múltiplas linhas. Com um objeto texto de linha única, a largura da caixa de contorno sempre será igual a largura da linha, assim, não é nada para ser alinhado.
Os valores permitidos para textAlign são left, center e right. A ***Figura 15*** mostra um texto alinhado a direita.

```javascript
var text = 'this is\na multiline\ntext\naligned right!';
var alignedRightText = new fabric.Text(text, {
  textAlign: 'right'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img15.png">

***Figura 15. Texto alinhado a direita***

###### lineHeight

Outra propriedade que talvez seja familiar do CSS é a lineHeight (altura de linha). Ela permite que você altere o espaçamento entre as linhas do texto em um texto de múltipla linhas. No exemplo seguinte, o primeiro bloco de texto tem lineHeight definido como 3 e o segundo bloco tem definido como 1. Os resultados 

são exibido na ***Figura 16***.

```javascript
var lineHeight3 = new fabric.Text('Lorem ipsum ...', {
  lineHeight: 3
});
var lineHeight1 = new fabric.Text('Lorem ipsum ...', {
  lineHeight: 1
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img16.png">

***Figura 16. Exemplos de alturas de linha***

###### backgroundColor

Finalmente, backgroundColor é o que permite a você definir um background para o texto. Note que o preenchimento do background ocupa o espaço somente dos caracteres do texto e não de toda a caixa de contorno do texto, como mostra a ***Figura 17***. Isso significa que o alinhamento do texto altera a forma com que o background do texto é renderizado - e da mesma forma a altura de linha, porque o background respeita o espaçamento vertical entre as linhas criado por lineHeight.

```javascript
var text = 'this is\na multiline\ntext\nwith\ncustom lineheight\n&background';
var textWithBackground = new fabric.Text(text, {
  backgroundColor: 'rgb(0,200,0)'
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/img17.png">

***Figura 17. Efeito de background no texto***


#### Sobre o autor

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_2/author.png">

Juriy Zaytsev é um apaixonado desenvolvedor JavaScript morando em New York. Ele é ex membro do núcleo Prototype.js, blogueiro em [perfectionkills.com]
(http://perfectionkills.com/), e criador da biblioteca para canvas [Fabric.js](http://fabricjs.com). No momento, Juriy trabalha em sua startup [Printio.ru](http://printio.ru) e faz a Fabric ainda mais divertida para se usar.

***Find Juriy on:***
- Twitter - [@kangax](http://twitter.com/kangax/)
- [Juriy's Blog](http://perfectionkills.com/)
