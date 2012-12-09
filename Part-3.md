Introdução à Fabric.js: Parte 3
==================

**[Juriy Zaytsev](https://github.com/rodrigopandini/articles-fabric.js/blob/master/Part-1.md#sobre-o-autor) | December 7, 2012**

**Tradução (pt_BR): [Rodrigo Pandini](http://twitter.com/rodrigopandini) | 8 Dezembro, 2012**

Nós cobrimos o mais básico na primeira e segunda parte desta série. Vamos seguir em frente e ver coisas mais avançadas.

### Grupos

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_3/img01.png">

***Figura 1: Trabalhando com grupos***

A primeira coisa que nós vamos ver é a respeito de grupos. Grupos são um dos componentes mais poderosos da Fabric. Eles são exatamente o que o nome diz ser - uma forma simples de agrupar qualquer objeto Fabric em uma única entidade. Porque precisaríamos disso? Para estarmos aptos a trabalhar com esses objetos como se fosse uma única unidade, claro!

Você se lembra que qualquer número de objetos Fabric no canvas podem ser agrupados com o mouse, formando uma seleção única? Uma vez agrupados, todos os objetos podem ser movidos e também modificados juntos. Eles formam um grupo. Nós podemos redimensionar este grupo, rotacioná-lo e também alterar suas propriedades de apresentação - cor, transparência, bordas, etc.

Isso é exatamente o que os grupos são e toda vez que você ver uma seleção desta no canvas, Fabric cria um grupo implícito de objetos por de trás da cena. Isso só faz sentido para prover acesso programaticamente ao grupo que se está trabalhando. É para isso que a fabric.Group serve.

Vamos criar um grupo de dois objetos, círculo e texto:

```javascript
var text = new fabric.Text('hello world', {
  fontSize: 30
});

var circle = new fabric.Circle({
  radius: 100,
  fill: '#eef',
  scaleY: 0.5
});

var group = new fabric.Group([ text, circle ], {
  left: 150,
  top: 100,
  angle: -10
});

canvas.add(group);
```

Primeiro nós criamos o objeto texto “hello world”. Em seguida, um círculo com 100px de raio, com cor de preenchimento “#eef” e achatado verticalmente (scaleY=0.5). E então criamos uma instância de fabric.Group, passando o array com estes objetos, e dando a ele a posição 150,100 e ângulo de -10. Finalmente, o grupo foi adicionado ao canvas como qualquer outros objeto deveria ser (com canvas.add()).

E voila! Você vê um objeto no canvas que se parece com uma elipse rotulada. Note como, em ordem de modificar o objeto, nós simplesmente alteramos as propriedades do grupo, dando a ele valores customizados left, top e angle. Você pode agora trabalhar com o objeto como uma entidade única.

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_3/img02.png">

***Figura 2: Grupo com texto e círculo achatado***

Agora que nós temos um grupo no canvas, vamos alterar ele um pouco:

```javascript
group.item(0).set({
  text: 'trololo',
  fill: 'white'
});
group.item(1).setFill('red');
```

O que está acontecendo aqui? Nós estamos acessando individualmente os objetos do grupo por meio do método item() e modificando suas propriedades. O primeiro objeto é o texto e o segundo é o círculo achatado. Vamos ver o que acontece:

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_3/img03.png">

***Figura 3: Grupo com objetos modificados***

Uma coisa importante que você provavelmente notou até agora é que os objetos no grupo são todos posicionados em relação ao centro do grupo. Quando nós alteramos o texto do objeto texto, ele permanece centralizado mesmo depois de mudarmos sua largura. Se você não quer este comportamento, você precisa de especificar as coordenadas left e top do objeto. Neste caso, eles serão agrupados juntos de acordo com suas coordenadas.

Vamos criar e agrupar 3 círculos e então posicioná-los horizontalmente um em relação ao outro:

```javascript
var circle1 = new fabric.Circle({
  radius: 50,
  fill: 'red',
  left: 0
});
var circle2 = new fabric.Circle({
  radius: 50,
  fill: 'green',
  left: 100
});
var circle3 = new fabric.Circle({
  radius: 50,
  fill: 'blue',
  left: 200
});

var group = new fabric.Group([ circle1, circle2, circle3 ], {
  left: 200,
  top: 100
});

canvas.add(group);
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_3/img04.png">

***Figura 4: Elementos com posições relativas no grupo***

Outra coisa a se ter em mente quando trabalhamos com grupos é o estado dos objetos. Por exemplo, quando formamos um grupo com imagens, você precisa ter certeza que estas imagens foram completamente carregadas. Uma vez que Fabric já disponibiliza métodos de ajuda para ter certeza que a imagem foi carregada, isso se torna bastante fácil:

```javascript
fabric.Image.fromURL('/assets/pug.jpg', function(img) {
  var img1 = img.scale(0.1).set({ left: 100, top: 100 });

  fabric.Image.fromURL('/assets/pug.jpg', function(img) {
    var img2 = img.scale(0.1).set({ left: 175, top: 175 });

    fabric.Image.fromURL('/assets/pug.jpg', function(img) {
      var img3 = img.scale(0.1).set({ left: 250, top: 250 });

      canvas.add(new fabric.Group([ img1, img2, img3], { left: 200, top: 200 }))
    });
  });
});
```

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_3/img05.png">

***Figura 5: Grupo de imagens com posições relativas***

Então quais outros métodos estão disponíveis quando trabalhamos com grupos? Há o método getObjects(), o qual funciona exatamente da mesma forma que fabric.Canvas#getObjects() e retorna um array com todos os objetos do grupo. Tem o método size() que representa a quantidade de objetos no grupo. Há também o contains() que permite verificar se um objeto particular está presente no grupo. Há o  item(), que como nós vimos anteriormente, permite pegar um objeto específico em um grupo. Também temos o forEachObject(), denovo refletindo fabric.Canvas#forEachObject, somente em relação aos objetos do grupo. Finalmente temos os métodos add() e o remove() que adiciona e remove objetos do grupo, nesta ordem. 

Você pode adicionar/remover objetos do grupo de 2 formas - com ou sem atualização das dimensões/posição do grupo.

Para adicionar um retângulo no centro de um grupo (left=0, top=0):

```javascript
group.add(new fabric.Rect({
  ...
}));
```

Para adicionar um retângulo deslocado 100px do centro de um grupo:

```javascript
group.add(new fabric.Rect({
  ...
  left: 100,
  top: 100
}));
```

Para adicionar um retângulo no centro do grupo E atualizar as dimensões do grupo:

```javascript
group.addWithUpdate(new fabric.Rect({
  ...
  left: group.getLeft(),
  top: group.getTop()
}));
```

Para adicionar um retângulo deslocado 100px do centro do grupo E atualizar as dimensões do grupo:

```javascript
group.addWithUpdate(new fabric.Rect({
  ...
  left: group.getLeft() + 100,
  top: group.getTop() + 100
}));
```

Finalmente, se você quiser criar um grupo com objetos que já estão presentes no canvas, você precisará de cloná-los primeiro:

```javascript
// create a group with copies of existing (2) objects
var group = new fabric.Group([
  canvas.item(0).clone(),
  canvas.item(1).clone()
]);

// remove all objects and re-render
canvas.clear().renderAll();

// add group onto canvas
canvas.add(group);
```

### Serialização

Assim que você começa a construir um aplicativo que possa manter o estado, em algum momento, você vai precisar fazer a serialização do canvas, seja para permitir ao usuário salvar o conteúdo do canvas no servidor ou para transmitir o conteúdo para outro cliente. Então como enviar o conteúdo do canvas? Claro que você tem sempre a opção de exportar o canvas como uma imagem, mas fazer o upload desta imagem para o servidor certamente consome muita largura de banda. Dessa forma, como nada mais supera texto quando se trata de tamanho, é exatamente por isso que a Fabric poporciona um excelente suporte para a serialização e deserialização do canvas.
toObject, toJSON

A espinha dorsal da serialização do canvas em Fabric são os métodos fabric.Canvas#toObject() e fabric.Canvas#toJSON(). Vejamos um exemplo simples, primeiro serializando um canvas vazio:

```javascript
var canvas = new fabric.Canvas('c');
JSON.stringify(canvas); // '{"objects":[],"background":"rgba(0, 0, 0, 0)"}'
```

Nós estamos usando o método JSON.stringify() da ES5, o qual implicitamente chama o método toJSON do objeto passado, se o método existir. Uma vez que a instância do canvas na Fabric tem o método toJSON, é como se nós estivéssemos chamando JSON.stringify(canvas.toJSON()).

Note a string de retorno que representa o canvas vazio. Ela está no formato JSON e essencialmente consiste das propriedades “objects” e “background”. “objects” está vazio no momento, uma vez que não há nada no canvas e o background tem o valor transparente por padrão ("rgba(0, 0, 0, 0)").

Vamos dar ao canvas um background diferente e ver como as coisas mudam.

```javascript
canvas.backgroundColor = 'red';
JSON.stringify(canvas); // '{"objects":[],"background":"red"}'
```

Como era de se esperar, a representação do canvas agora refete a nova cor de background. Vamos adicionar agora alguns objetos!

```javascript
canvas.add(new fabric.Rect({
  left: 50,
  top: 50,
  height: 20,
  width: 20,
  fill: 'green'
}));
console.log(JSON.stringify(canvas));
```

… e o log da saída é:

```javascript
'{"objects":[{"type":"rect","left":50,"top":50,"width":20,"height":20,"fill":"green","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0}],"background":"rgba(0, 0, 0, 0)"}'
```

Woah. A primeira impressão é que mudou muita coisa, mas olhando de perto nós vemos que o novo objeto adicionado agora faz parte do array de objetos, serializado no JSON. Note como essa representação agora incluí todos os aspectos visuais — left, top, width, height, fill, stroke, etc. Se nós adicionarmos outro objeto — digamos, um círculo vermelho próximo ao retângulo você vai ver que a representação muda de acordo.

```javascript
canvas.add(new fabric.Circle({
  left: 100,
  top: 100,
  radius: 50,
  fill: 'red'
}));
console.log(JSON.stringify(canvas));
```

... e o log  da saída é:

```javascript
'{"objects":[{"type":"rect","left":50,"top":50,"width":20,"height":20,"fill":"green","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0},{"type":"circle","left":100,"top":100,"width":100,"height":100,"fill":"red","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"radius":50}],"background":"rgba(0, 0, 0, 0)"}'
```

Eu realcei as partes "type":"rect" e "type":"circle", assim você pode ver melhor onde esses objetos estão. Mesmo que a princípio possa parecer um monte de saída, isso não é nada comparado com o que você teria com a serialização como imagem. Só para comparação, vamos ver cerca de um décimo (!) da string que você teria com canvas.toDataURL('png')

```javascript
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAyAAAAK8CAYAAAAXo9vkAAAgAElEQVR4Xu3dP4xtBbnG4WPAQOQ2YBCLK1qpoQE1/m+NVlCDwUACicRCEuysrOwkwcJgAglEItRQaWz9HxEaolSKtxCJ0FwMRIj32zqFcjm8e868s2fNWo/Jygl+e397rWetk5xf5pyZd13wPwIECBAgQIAAAQIECBxI4F0H+hwfQ4AAAQIECBAgQIAAgQsCxENAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECAsQzQIAAAQIECBAgQIDAwQQEyMGofRABAgQIECBAgAABAgLEM0CAAAECBAgQIECAwMEEBMjBqH0QAQIECBAgQIAAAQICxDNAgAABAgQIECBAgMDBBATIwah9EAECBAgQIECAAAECyw+Qb134R/U2fevC8q+5esGWESBAgAABAgQIEFiOwPL/MC5AlvO0OBMCBAgQIECAAAECJxQQICcE9HYCBAgQIECAAAECBPYXECD7W3klAQIECBAgQIAAAQInFBAgJwT0dgIECBAgQIAAAQIE9hcQIPtbeSUBAgQIECBAgAABAicUECAnBPR2AgQIECBAgAABAgT2FxAg+1t5JQECBAgQIECAAAECJxQQICcE9HYCBAgQIECAAAECBPYXECD7W3klAQIECBAgQIAAAQInFBAgJwTc9+3z49yvmNd+dI7PzPHJOW6Y4wNzXD3HlXNc9pZdb85/vzbHK3P8aY7n5vj1HL+Y43dz417f97O9jgABAgQIECBAgMBSBATIKd2JCY5dWNwyx5fn+PwcV5U/6tXZ99M5fjjHk3Mjd6HifwQIECBAgAABAgQWLSBAirdnouP6WXfvHHfOcU1x9T6rXp4XPTLHA3NTX9jnDV5DgAABAgQIECBA4NACAuSE4hMdl8+Kr83xzTmuO+G61ttfnEXfnuN7c4PfaC21hwABAgQIECBAgMBJBQTIJQpOeFw7b71/jtsvccWh3vbYfNB9c6NfOtQH+hwCBAgQIECAAAECFxMQIMd8No7C4+F5283HfOtZv/ypOYG7hMhZ3wafT4AAAQIECBDYtoAA2fP+H/1Vqwd3f4jf8y1Lfdkunu7xV7OWenucFwECBAgQIEBg3QICZI/7O/Fxx7xs9wf3t36r3D3evciX7L7F7+6rIY8u8uycFAECBAgQIE
```

...e aproximadamente mais 17000 caracteres.

Você deve estar pensando porque há também o método fabric.Canvas#toObject. Simples, toObject retorna a mesma representação que toJSON, só que na forma de objeto real, sem a string de serialização. Por exemplo, pegando o canvas do exemplo anterior, com somente um retângulo verde, a saída de `canvas.toObject()` é:

```javascript
{ "background" : "rgba(0, 0, 0, 0)",
  "objects" : [
    {
      "angle" : 0,
      "fill" : "green",
      "flipX" : false,
      "flipY" : false,
      "hasBorders" : true,
      "hasControls" : true,
      "hasRotatingPoint" : false,
      "height" : 20,
      "left" : 50,
      "opacity" : 1,
      "overlayFill" : null,
      "perPixelTargetFind" : false,
      "scaleX" : 1,
      "scaleY" : 1,
      "selectable" : true,
      "stroke" : null,
      "strokeDashArray" : null,
      "strokeWidth" : 1,
      "top" : 50,
      "transparentCorners" : true,
      "type" : "rect",
      "width" : 20
    }
  ]
}
```

Como você pode ver, a saída de toJSON é essencialmente a saída de toObject “stringuificada”.
Agora, a coisa interessante (e útil!) é que essa saída de toObject é esperta e preguiçosa. O que você vê dentro do array de “objects” é o resultado da iteração sobre todo os objetos do canvas e delegando para os seus próprios métodos toObject . fabric.Path tem seu próprio toObject - que como sabemos retorna o array de “pontos” e fabric.Image tem seu próprio toObject - que como sabemos retorna a propriedade “src”. Em um estilo verdadeiramente orientado a objetos, todos os objetos são capazes de serializar a si mesmos.

Isso significa que quando você criar sua própria classe, ou simplesmente precisa customizar a representação serializada do objeto, tudo que você precisa fazer é trabalhar com o método toObject - até mesmo substituí-lo completamente ou extendê-lo. Vamos tentar isso:

```javascript
var rect = new fabric.Rect();
rect.toObject = function() {
  return { name: 'trololo' };
};
canvas.add(rect);
console.log(JSON.stringify(canvas));
```

… e o log de saída é:

```javascript
'{"objects":[{"name":"trololo"}],"background":"rgba(0, 0, 0, 0)"}'
```

Como você pode ver, o array de objetos agora possuí a representação customizada do nosso retângulo. Este tipo de sobrescrita não é provavelmente muito útil - embora traga um ponto de vista - então que tal se nós ao invés disso, extendermos o método toObject com propriedades adicionais.

```javascript
var rect = new fabric.Rect();

rect.toObject = (function(toObject) {
  return function() {
    return fabric.util.object.extend(toObject.call(this), {
      name: this.name
    });
  };
})(rect.toObject);

canvas.add(rect);

rect.name = 'trololo';

console.log(JSON.stringify(canvas));
```

… e o log da saída é:

```javascript
'{"objects":[{"type":"rect","left":0,"top":0,"width":0,"height":0,"fill":"rgb(0,0,0)","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0,"name":"trololo"}],"background":"rgba(0, 0, 0, 0)"}'
```

Nós extendemos o método toObject existente no objeto com a propriedade - “name”, assim esta propriedade faz parte agora da saída do toObject e como resultado aparece na representação JSON do canvas. Mais uma coisa importante a se mencionar é que se você extende um objeto como esse, você também vai ter que ter certeza que a classe do objeto (fabric.Rect neste caso) tenha esta propriedade no array “stateProperties”, assim, ao carregar o canvas a partir de uma representação em string será feito o parse e ela será adicionada ao objeto corretamente.
toSVG

Another efficient text-based canvas representation is in SVG format. Since Fabric specializes in SVG parsing and rendering on canvas, it only makes sense to make this a two-way process and provide canvas-to-SVG conversion. Let's add the same rectangle to canvas, and see what kind of representation is returned from toSVG method:

```javascript
canvas.add(new fabric.Rect({
  left: 50,
  top: 50,
  height: 20,
  width: 20,
  fill: 'green'
}));
console.log(canvas.toSVG());
```

… e o log de saída é:

```javascript
'<?xml version="1.0" standalone="no" ?><!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 20010904//EN" "http://www.w3.org/TR/2001/REC-SVG-20010904/DTD/svg10.dtd"><svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" width="800" height="700" xml:space="preserve"><desc>Created with Fabric.js 0.9.21</desc><rect x="-10" y="-10" rx="0" ry="0" width="20" height="20" style="stroke: none; stroke-width: 1; stroke-dasharray: ; fill: green; opacity: 1;" transform="translate(50 50)" /></svg>'
```

Assim como toJSON e toObject, toSVG - quando chamado no canvas - delega sua lógica para cada objeto individualmente, e cada objeto possui seu próprio método toSVG que específico para o tipo de objeto. Se você precisar de modificar ou extender a representação SVG do objeto, você pode fazer a mesma coisa com toSVG como nós fizemos com toObject.

O benefício da representação SVG comparado com a propriedade toObject/toJSON da Fabric é que você pode abrí-lo em qualquer dispositivo capaz de renderizar SVG (browser, aplicativos, impressoras, câmeras, etc) e ele deverá funcionar. Com toObject/toJSON, entretando, você precisa primeiro de carregá-lo no canvas. Falando em carregar coisas no canvas, agora que podemos serializar o canvas em um eficiente bloco de texto, como iremos carregá-lo de volta no canvas?

### Deserialização, SVG parser

Similarmente com a serialização, há duas formas de carregar o canvas de uma string: a partir da representação JSON ou a partir de um SVG. Quando usamos a representação JSON, há os métodos fabric.Canvas#loadFromJSON e fabric.Canvas#loadFromDatalessJSON. Quando usamos SVG, há os métodos fabric.loadSVGFromURL e fabric.loadSVGFromString.

Note que os 2 primeiros métodos são únicos na instância e são chamados no canvas diretamente, enquanto os 2 últimos métodos são estáticos e são chamados nos objetos "fabric" ao invés de no canvas.

Não há muito o que dizer sobre estes métodos. Eles funcionam exatamente como você esperaria que funcionasse. Vamos pegar, por exemplo, o JSON da saída anterior do canvas e carregá-lo em um canvas vazia:

```javascript
var canvas = new fabric.Canvas();

canvas.loadFromJSON('{"objects":

[{"type":"rect","left":50,"top":50,"width":20,"height":20,"fill":"green","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"rx":0,"ry":0},{"type":"circle","left":100,"top":100,"width":100,"height":100,"fill":"red","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":0,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"radius":50}],"background":"rgba(0, 0, 0, 0)"}');
```

... e "magicamente" ambos os objetos aparecem no canvas:

Então carregar um canvas a partir de uma string é muito fácil. Mas e o método de aparência estranha loadFromDatalessJSON? Como exatamente ele é diferente de loadFromJSON que nós usamos anteriormente? Para enteder o porque precisamos deste método, nós precisamos olhar um canvas serializado que tem um objeto path mais ou menos complexo. Como este aqui:

... e a saída de JSON.stringify(canvas) para este shape é:

```javascript
{"objects":[{"type":"path","left":184,"top":177,"width":175,"height":151,"fill":"#231F20","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":-19,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"path":[["M",39.502,61.823],["c",-1.235,-0.902,-3.038,-3.605,-3.038,-3.605],["s",0.702,0.4,3.907,1.203],["c",3.205,0.8,7.444,-0.668,10.114,-1.97],["c",2.671,-1.302,7.11,-1.436,9.448,-1.336],["c",2.336,0.101,4.707,0.602,4.373,2.036],["c",-0.334,1.437,-5.742,3.94,-5.742,3.94],["s",0.4,0.334,1.236,0.334],["c",0.833,0,6.075,-1.403,6.542,-4.173],["s",-1.802,-8.377,-3.272,-9.013],["c",-1.468,-0.633,-4.172,0,-4.172,0],["c",4.039,1.438,4.941,6.176,4.941,6.176],["c",-2.604,-1.504,-9.279,-1.234,-12.619,0.501],["c",-3.337,1.736,-8.379,2.67,-10.083,2.503],["c",-1.701,-0.167,-3.571,-1.036,-3.571,-1.036],["c",1.837,0.034,3.239,-2.669,3.239,-2.669],["s",-2.068,2.269,-5.542,0.434],["c",-3.47,-1.837,-1.704,-8.18,-1.704,-8.18],["s",-2.937,5.909,-1,9.816],["C",34.496,60.688,39.502,61.823,39.502,61.823],["z"],["M",77.002,40.772],["c",0,0,-1.78,-5.03,-2.804,-8.546],["l",-1.557,8.411],["l",1.646,1.602],["c",0,0,0,-0.622,-0.668,-1.691],["C",72.952,39.48,76.513,40.371,77.002,40.772],["z"],["M",102.989,86.943],["M",102.396,86.424],["c",0.25,0.22,0.447,0.391,0.594,0.519],["C",102.796,86.774,102.571,86.578,102.396,86.424],["z"],["M",169.407,119.374],["c",-0.09,-5.429,-3.917,-3.914,-3.917,-2.402],["c",0,0,-11.396,1.603,-13.086,-6.677],["c",0,0,3.56,-5.43,1.69,-12.461],["c",-0.575,-2.163,-1.691,-5.337,-3.637,-8.605],["c",11.104,2.121,21.701,-5.08,19.038,-15.519],["c",-3.34,-13.087,-19.63,-9.481,-24.437,-9.349],["c",-4.809,0.135,-13.486,-2.002,-8.011,-11.618],["c",5.473,-9.613,18.024,-5.874,18.024,-5.874],["c",-2.136,0.668,-4.674,4.807,-4.674,4.807],["c",9.748,-6.811,22.301,4.541,22.301,4.541],["c",-3.097,-13.678,-23.153,-14.636,-30.041,-12.635],["c",-4.286,-0.377,-5.241,-3.391,-3.073,-6.637],["c",2.314,-3.473,10.503,-13.976,10.503,-13.976],["s",-2.048,2.046,-6.231,4.005],["c",-4.184,1.96,-6.321,-2.227,-4.362,-6.854],["c",1.96,-4.627,8.191,-16.559,8.191,-16.559],["c",-1.96,3.207,-24.571,31.247,-21.723,26.707],["c",2.85,-4.541,5.253,-11.93,5.253,-11.93],["c",-2.849,6.943,-22.434,25.283,-30.713,34.274],["s",-5.786,19.583,-4.005,21.987],["c",0.43,0.58,0.601,0.972,0.62,1.232],["c",-4.868,-3.052,-3.884,-13.936,-0.264,-19.66],["c",3.829,-6.053,18.427,-20.207,18.427,-20.207],["v",-1.336],["c",0,0,0.444,-1.513,-0.089,-0.444],["c",-0.535,1.068,-3.65,1.245,-3.384,-0.889],["c",0.268,-2.137,-0.356,-8.549,-0.356,-8.549],["s",-1.157,5.789,-2.758,5.61],["c",-1.603,-0.179,-2.493,-2.672,-2.405,-5.432],["c",0.089,-2.758,-1.157,-9.702,-1.157,-9.702],["c",-0.8,11.75,-8.277,8.011,-8.277,3.74],["c",0,-4.274,-4.541,-12.82,-4.541,-12.82],["s",2.403,14.421,-1.336,14.421],["c",-3.737,0,-6.944,-5.074,-9.879,-9.882],["C",78.161,5.874,68.279,0,68.279,0],["c",13.428,16.088,17.656,32.111,18.397,44.512],["c",-1.793,0.422,-2.908,2.224,-2.908,2.224],["c",0.356,-2.847,-0.624,-7.745,-1.245,-9.882],["c",-0.624,-2.137,-1.159,-9.168,-1.159,-9.168],["c",0,2.67,-0.979,5.253,-2.048,9.079],["c",-1.068,3.828,-0.801,6.054,-0.801,6.054],["c",-1.068,-2.227,-4.271,-2.137,-4.271,-2.137],["c",1.336,1.783,0.177,2.493,0.177,2.493],["s",0,0,-1.424,-1.601],["c",-1.424,-1.603,-3.473,-0.981,-3.384,0.265],["c",0.089,1.247,0,1.959,-2.849,1.959],["c",-2.846,0,-5.874,-3.47,-9.078,-3.116],["c",-3.206,0.356,-5.521,2.137,-5.698,6.678],["c",-0.179,4.541,1.869,5.251,1.869,5.251],["c",-0.801,-0.443,-0.891,-1.067,-0.891,-3.473],...
```
... e este é somente a quinta parte de todo a saída!

O que está acontecendo aqui? Bem, isso demonstra que este instância de fabric.Path - este shape - consiste de literalmente centenas de linha bezier ditando como exatamente ela deve ser renderizado. Todos estes blocos ["c",0,2.67,-0.979,5.253,-2.048,9.079] na representação JSON correspondem a cada uma destas curvas. E quando há dezenas (ou até mesmo milhares) destes, a representação do canvas acaba ficando enorme.

O que fazer?

É nesta hora que fabric.Canvas#toDatalessJSON vem ajudar. Vamos  tentar isso:

```javascript
canvas.item(0).sourcePath = '/assets/dragon.svg';
console.log(JSON.stringify(canvas.toDatalessJSON()));
```

.. e o log da saída é:

```javascript
{"objects":[{"type":"path","left":143,"top":143,"width":175,"height":151,"fill":"#231F20","overlayFill":null,"stroke":null,"strokeWidth":1,"strokeDashArray":null,"scaleX":1,"scaleY":1,"angle":-19,"flipX":false,"flipY":false,"opacity":1,"selectable":true,"hasControls":true,"hasBorders":true,"hasRotatingPoint":false,"transparentCorners":true,"perPixelTargetFind":false,"path":"/assets/dragon.svg"}],"background":"rgba(0, 0, 0, 0)"}
```
Bem, isso é certamente menor! Então o que aconteceu? Note como antes de chamar toDatalessJSON, nós definimos a propriedade "sourcePath" do objeto path (a forma do dragão) o valor "/assets/dragon.svg". Assim, quando chamamos toDatalessJSON a enorme string do path previamente visto (aqueles dezenas de comandos de path) foi subistituído com uma única string "dragon.svg". Você pode ver isso destacado abaixo.

Quando trabalhamos com muitos shapes complexos, toDatalessJSON nos permite reduzir a representação do canvas \even further\ e subistituir os enormes dados de representação do canvas com um simples link para o SVG.

E agora voltando ao método loadFromDatalessJSON... você pode provavelmente achar que isso simplesmente permite carregar o canvas de uma versão representação dataless. loadFromDatalessJSON sabe como pegar essa string de "path" (como "/assets/dragon.svg") carregá-lo e então usar como dados para os objetos correspondentes.

Agora, vamos ver os métodos de carregamentos SVG. Nós podemos usar a string ou URL:

```javascript
fabric.loadSVGFromString('...', function(objects, options) {
  var obj = fabric.util.groupSVGElements(objects, options);
  canvas.add(obj).renderAll();
});
```

O primeiro argumento é a string SVG e o segundo é uma função de retorno (callback). A função de retorno é invocada quando é feito o parse do SVG é carregado e ela recebe 2 argumentos - objects e options. objects contém um array de objetos parsed a partir do SVG - paths, path groups (para objetos complexos), imagens, texto, etc. Em ordem de agrupar todos estes objetos em uma coleção "coesiva" e para fazê-los para parece da mesma forma que eles são no documento SVG, nós estamos usando fabric.util.groupSVGElements passando ambos objects e options. No retorno, nós termos uma instância de fabric.Path ou fabric.pathGroup, o qual nós podemos então adicioná-los no canvas.

fabric.loadSVGFromURL funciona da mesma forma, exceto que você passa a string contendo a URL ao invés do conteúdo SVG. Note que Fabric irá tentar buscar essa URL via XMLHTTPRequest, assim o SVG precisa está de acordo com as regras SOP usuais.

### Subclasse

Uma vez que a Fabric é construida ao estilo verdadeiramente orientado a objetos, ela foi planejada para fazer subclasses e extenções de um forma simples e natural. Como você viu na primeira parte desta série, há uma hierarquia dos objetos na Fabric. Todos os objetos 2D (paths, images, text, etc) herdam de fabric.Object e algumas "classes" - como fabric.PathGroup - formam uma herânça em terceira nível.

Então como nós criamos subclasses de uma classe existente na Fabric? Ou talvez até mesmo criarmos nossa própria?

Para a primeira pergunta, nós iremos precisar do método utilitário fabric.util.createClass. createClass não nada mais do que uma simples abstração sobre o protótipo de herança do Javascript. Vamos criar uma "classe" Ponto simples:

```javascript
var Point = fabric.util.createClass({
  initialize: function(x, y) {
    this.x = x || 0;
    this.y = y || 0;
  },
  toString: function() {
    return this.x + '/' + this.y;
  }
});
```

createClass pega um objeto e usa as propriedades do objeto para criar a "classes" com as propriedades de nível de instância. A única propriedade especial é "initialize", a qual é usada como construtor. Assim, quando inicializarmos o Point, nós criaremos uma instância com as propriedades "x" e "y" e o método "toString": 

```javascript
var point = new Point(10, 20);

point.x; // 10
point.y; // 20

point.toString(); // "10/20"
```

Se nós quizermos criar um filho da class "Point" - digamos um ponto colorido, nós poderiamos usar createClass dessa forma:

```javascript
var ColoredPoint = fabric.util.createClass(Point, {
  initialize: function(x, y, color) {
    this.callSuper('initialize', x, y);
    this.color = color || '#000';
  },
  toString: function() {
    return this.callSuper('toString') + ' (color: ' + this.color + ')';
  }
});
```

Note como o objeto com as propriedades de nível de instância é agora passada como argumento. E o primeiro argumento recebe a "classe" Point, a qual diz para createClass para usar a "classe" pai dele. Em ordem de esquecer a duplicação, nós estamos usando o método callSuper, o qual chama o método da "classe" pai. 

Isso significa que se nós mudarmos a classe Point, essas mudanças também irã se propagar na classe ColoredPoint. Vejamos a ColoredPoint em ação:

```javascript
var redPoint = new ColoredPoint(15, 33, '#f55');

redPoint.x; // 15
redPoint.y; // 33
redPoint.color; // "#f55"

redPoint.toString(); "15/35 (color: #f55)"
```

Agora que nós já sabemos como criar nossas próprias "classes" e "subclasses", vamos ver como trabalhar com as classes já existentes da Fabric. Por exemplo, vamos criar uma "classe" LabeledRect a qual essencialmente é um retângulo com um tipo de rótulo associado a ele. Quando renderizamos no canvas, o rótulo será representado como um texto dentro do retângulo. Algo similar ao exemplo anterior do grupo com círculo com texto. Como você está trabalhando com Fabric, você notará que as abstrações combinadas como essa podem ser conseguidas por meio de grupos ou usando classes customizadas.

```javascript
var LabeledRect = fabric.util.createClass(fabric.Rect, {

  type: 'labeledRect',

  initialize: function(options) {
    options || (options = { });

    this.callSuper('initialize', options);
    this.set('label', options.label || '');
  },

  toObject: function() {
    return fabric.util.object.extend(this.callSuper('toObject'), {
      label: this.get('label')
    });
  },

  _render: function(ctx) {
    this.callSuper('_render', ctx);

    ctx.font = '20px Helvetica';
    ctx.fillStyle = '#333';
    ctx.fillText(this.label, -this.width/2, -this.height/2 + 20);
  }
});
```

Parece que muita coisa está acontecendo aqui, mas na verdade é muito simples.

Primeiro, nós estamos especificando a "classe" pai como fabric.Rect, para utilizar suas habilidade de renderização. Em seguida, nós definimos a propriedade "type", com o valor "labeledRect". Isso é só para consistência, uma vez que todos os objetos Fabric tem a propriedade type (rect, circle, path, text, etc.). 

Então há um construtor já familiar (initialize) com o qual nós estamos utilizando callSuper de novo. Adicionalmente, nós definimos um rótulo do objeto qulaquer valor passado via options. Finalmente, nós temos dois métodos - toObject e _render. toObject, como nós já sabemos do capítulo de serialização, é responsável pela representação do object (e JSON) na instância. Uma vez que LabeledRect tem as mesmas propriedades de um retângulo regular, mas também um rótulo, nós estamos extendendo o método toobject do pai e simplesmente adicionando o rótulo nele. Por último mas não menos importante o método _render é o responsável por desenhar a instância. Há outra chamada a callSuper nela, o qual renderiza o retângulo e adicionalmente 3 linhas para a lógica de renderização do text.

Agora, se nós renderizarmos tal objeto:

```javascript
var labeledRect = new LabeledRect({
  width: 100,
  height: 50,
  left: 100,
  top: 100,
  label: 'test',
  fill: '#faa'
});
canvas.add(labeledRect);
```

... nós iremos ter isso:

Mudando o valor do rótulo ou qualquer outra propriedade usual do retângulo, irá funcionar obviamente como esperado:

```javascript
labeledRect.set({
  label: 'trololo',
  fill: '#aaf',
  rx: 10,
  ry: 10
});
```

Claro que neste ponto, você é livre para modificar o comportamento desta "classe" da maneira que você quer. Por exemplo, definir alguns valores padrão, para não precisar de passá-los toda vez para o construtor. Ou fazer certas propriedades configuráveis disponíveis na instância. Se você quiser fazer as propriedades adicionais configuráveis, você talvez queira adicioná-los em toObject e initialize:

```javascript
...
initialize: function(options) {
  options || (options = { });

  this.callSuper('initialize', options);

  // give all labeled rectangles fixed width/heigh of 100/50
  this.set({ width: 100, height: 50 });

  this.set('label', options.label || '');
}
...
_render: function(ctx) {

  // make font and fill values of labels configurable
  ctx.font = this.labelFont;
  ctx.fillStyle = this.labelFill;

  ctx.fillText(this.label, -this.width/2, -this.height/2 + 20);
}
...
```

Uma nota, eu estou empacotando esta terceira parte desta séria, na qual nós dividimos em alguns dos aspectos mais avançados da Fabric.
Com a ajuda dos grupos, classes e serialialização e desserialização você pode levar a sua aplicação a um novo nível.

#### Sobre o autor

<img src="https://raw.github.com/rodrigopandini/articles-fabric.js/master/assets/img/part_1/author.png">

Juriy Zaytsev é um apaixonado desenvolvedor JavaScript morando em New York. Ele é ex membro do núcleo Prototype.js, blogueiro em [perfectionkills.com](http://perfectionkills.com/), e criador da biblioteca para canvas [Fabric.js](http://fabricjs.com). No momento, Juriy trabalha em sua startup [Printio.ru](http://printio.ru) e faz a Fabric ainda mais divertida para se usar.

***Encontre Juriy em:***
- Twitter - [@kangax](http://twitter.com/kangax/)
- [Juriy's Blog](http://perfectionkills.com/)
