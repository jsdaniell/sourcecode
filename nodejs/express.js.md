# Express.js

## O que é? Por quê usar?

### Codificação de Servidor é Complexa!

Nas minhas anotações anteriores sobre Nodejs \(requests e responses\) pude perceber uma certa redundância de códigos mais especificamente na implementação de rotas, além de uma devida complexidade de execução para capturar dados de requisições e manipulá-los, como o exemplo de capturar o Buffer e após isso tranformá-lo em string, ou seja a manipulação de dados carrega uma certa estrutura sequencial complexa em Node.js puro.

Removendo essas desnecessariedades podemos ter um maior foco na lógica de negócio da nossa aplicação, no funcionamento lógico, regras de negócio. É importante entender o funcionamento do Node para que assim saibamos o que na verdade o Expressjs nos abstrai para uma maior produtividade. Utilizado o framework para fazer o trabalho pesado, se pode pensar no melhor desempenho e lógica da aplicação.

Em toda minha experiência com Frameworks, posso dizer que basicamente pode ser representado como uma série de funções de ajuda \(helpers functions\), ferramentas e regras que te ajudam a construir sua aplicação.

Complementando o assunto, muitas pessoas pensam que apenas sabendo a linguagem de programação ao qual um framework pertence elas poderão implementa-lo da noite pro dia, é importante ressaltar que é necessário conhecer o framework para utiliza-lo saber quais são os seus objetivos para com o desenvolvimento de uma aplicação, como ele funciona.

### Não é o único!

Express.js não é o único framework disponível para cumprir esse objetivo de facilitar o desenvolvimento, segue uma lista de outras alternativas:

* Nodejs puro \(sim, é possível mas bastante trabalhoso escrever as mesmas aplicações complexas ou não, escritas com utilização do Express\)
* [Adonis.js](https://adonisjs.com)
* Koa
* Sails.js
* Entre outras...

### Exemplo de uso

Após adicionar o Express.js ao projeto através do gerenciador de pacotes `npm` podemos requisitar ele como um módulo, como no exemplo abaixo:

```text
const http = require('http');
const express = require('express');

const app = express(); // Basicamente a criação de uma aplicação Express

const server = http.createServer(app); // A instânciação da aplicação no servidor

server.listen(3000);
```

O código acima não traz resultados no browser visto que não criei uma lógica nem adicionei propriedades a aplicação, porém é possível ver que basicamente troquei a função anônima que recebia requisições e mandava resposta, por toda uma aplicação Express, que vou demonstrar mais a frente como maneja-la.

## Middleware

Aqui vai uma característica importante sobre o Express, ele funciona com Middlewares, são execuções que estão no meio do processo de requisição e resposta, tendo como papel na comunicação entre servidor e cliente, de processar, manipular e vigiar essa requisição ou resposta.

![Funcionamento dos Middlewares.](../.gitbook/assets/untitled%20%282%29.png)

Uma requisição recebida por um Middleware no Express.js passa por uma série de funções pré-concebidas pelo framework, então ao invés de ter apenas uma lógica para várias requisições diferentes, se pode ter várias requisições com uma pré-estrutura de funcionalidades dedicada a cada requisição diferente, possivelmente isso é o que permite a facilitação da implementação de rotas diferentes vindas pelas requisições do browser, então se pode enganhar múltiplas funções a uma requisição antes de enviar uma resposta, isso ainda permite que o código seja dividido em várias partes em vez de um código gigante que faz tudo.

Esta é a natureza \(puggable nature\) do Express.js onde se pode adicionar outros pacotes externos que adicionam essas funcionalidades de Middlewares e conectar ao Express.

### Utilizando Middleware Functions

Existe um comando do framework que nos permite facilmente utilizar funções middlewares no nosso código:

```text
const http = require('http');
const express = require('express');

const app = express();

app.use(); // Utilizar middleware

const server = http.createServer(app);

server.listen(3000);
```

A forma mais fácil de se utilizar esse método é da seguinte maneira:

```text
const app.use(() => {});
```

Essa função anônima que adicionei ao `app` , todas as requisições \(incomming requests\) vão passar por ela, e essa função recebe três argumentos:

```text
const app.use((req, res, next) => {});
```

São os parâmetros `req, res, next` , que é basicamente a requisição, a resposta e o next é uma função que permite que essa requisição possa viajar para o próximo middleware. Por exemplo se tivermos dois middlewares:

```text
const http = require('http');
const express = require('express');

const app = express();

const app.use((req, res, next) => {
	console.log("Is a middleware");
});

const app.use((req, res, next) => {
	console.log("Is another middleware");
});

const server = http.createServer(app);

server.listen(3000);

// Console result:

// Is a middleware
// Is a middleware
```

Da maneira como escrevemos acima apenas o primeiro middleware será executado. Para que o próximo middleware execute, deve-se chama a função `next()` no final do middleware anterior.

```text
const http = require('http');
const express = require('express');

const app = express();

const app.use((req, res, next) => {
	console.log("Is a middleware");
	next();
});

const app.use((req, res, next) => {
	console.log("Is another middleware");
	// Response here!
});

const server = http.createServer(app);

server.listen(3000);

// Console result:

// Is a middleware
// Is a middleware
```

O framework não oferece uma resposta padrão, no segundo middleware é necessário escrever a resposta, agora vem mais uma grande utilidade do Express, possibilitando a escrita de código html de forma mais produtiva, utilizando o método `send()` do framework, podemos escrever qualquer tag html, que seria compátivel com o &lt;body&gt;.

```text
const http = require('http');
const express = require('express');

const app = express();

const app.use((req, res, next) => {
	console.log("Is a middleware");
	next();
});

const app.use((req, res, next) => {
	console.log("Is another middleware");
	res.send('<h1>Hello from Express</h1>');
});

const server = http.createServer(app);

server.listen(3000);

// Console result:

// Is a middleware
// Is a middleware
```

Por convenção podemos também alterar o comando `server.listen(3000)` para `app.listen(3000)` , e ainda remover o `require('http')` visto que dentro da biblioteca do Express esse módulo já está implicitamente importado, removeremos também a constante `server` que já não é mais necessária já que o objeto `app` já está criado como um servidor no background do Express.js.

```text
const express = require('express');

const app = express();

const app.use((req, res, next) => {
	console.log("Is a middleware");
	next();
});

const app.use((req, res, next) => {
	console.log("Is another middleware");
	res.send('<h1>Hello from Express</h1>');
});

app.listen(3000);

// Console result:

// Is a middleware
// Is a middleware
```

Agora todo o código está "escrito" em Express.js porém o que está acontecendo é que estou passando de modo mais simples os parâmetros e atributos que me convêm para que o Express faça todo o trabalho de parsing, request ou response em Node.js puro no background.

## Lidando com Rotas no Express.js

Abaixo a exemplificação do quão mais fácil é direcionar e manipular rotas com o uso do Express.js, observando o código abaixo:

```text
const express = require('express')

const app = express();
const port = 3000

app.use('/', (req, res) => {
    
    console.log("It's working!");
    res.send('<h1>Nodemon is awessome!</h1>');

})


app.listen(port);
```

A execução do servidor irá ser efetuada normalmente, assim como a página mostrará `Nodemon is awessome!` porém se adicionarmos algum endereço após a barra como por exemplo: `[<http://localhost:300/add-product>](<http://localhost:3000/add-product>)` a página ainda vai ser a mesma o que significa que caiu no mesmo middleware com a mesma frase anterior, análisando esse comportamento já se percebe que é diferente do uso do Node.js puro, no qual retornaria um erro como resultado, visto que não haveria resposta para tal requisição.

No caso acima o middleware pôde ser executado tanto pelo endereço apenas com `/` quanto pelo endereço com `/add-product` . Isso acontece por que o primeiro parâmetro utilizado `('/')` não significa o caminho por inteiro e sim, está significando apenas que o endereço começará com `/` . Para funcionar corretamente o código tem de ser da seguinte maneira:

```text
const express = require('express')

const app = express();
const port = 3000

app.use('/add-product', (req, res, next) => {
    
    console.log("It's working!");
    res.send('<h1>Now add your product</h1>');
})

app.use('/', (req, res, next) => {
    
    console.log("It's working!");
    res.send('<h1>Nodemon is awessome!</h1>');
})


app.listen(port);
```

É importante perceber aqui que não estamos permitindo a passagem do middleware com `/add-product` por que não há função `next()` , sendo assim o middleware debaixo não tem sequer chance se executar se a condição do middleware anterior for atendida, se invertessemos o resultado não seria diferente da tentativa anterior, visto que o último middleware atende qualquer endereço que começe com `/` . Basicamente estou criando um filtro de condições para serem atendidas acima do endereço padrão de acesso de forma em que o código seja executado em ordem gravitacional.

## Extraindo Dados de Requisições

Anteriormente fiz uma grande anotação sobre como extrair dados de uma requisição POST feita por um input de um formulário em html, porém é de se perceber que é deveras trabalhoso, visto que é necessário fazer o parsing do Buffer \(é como o Node.js funciona\) para só depois utilizar o dado em algum futuro, ainda tive de separar os index do array pra poder mostrar apenas a mensagem, agora vai-se perceber a abstração na quantidade de código que o Express nos permite.

```text
const express = require('express')

const app = express();
const port = 3000

app.use('/add-product', (req, res) => {

    res.send('<form action="/product" method="POST"><input type="text" name="title"><button type="submit">Add Product</button></form>')
})

app.use('/product', (req, res) => {

    console.log(req.body);
    res.redirect('/');
})

app.use('/', (req, res) => {
    res.send('<h1>Here is your response Sir.!</h1>');
})

app.listen(port);
```

No código acima adicionei um formulário semelhante, porém o método de redirecionamento de rotas do Express foi usado, ainda com um novo comando de resposta, o `res.redirect('/')` no qual redireciona a página para outra, nesse caso para a página raiz. E para observar como se comporta o `body` tentei mostra-lo no console, porém sem sucesso, o resultado foi `undefined` , isso acontece por que por padrão a requisição não faz o parsing da incomming message, para que seja possível ver os dados do `body` é preciso fazer o parsing dos dados da requisição, e isso pode ser feito utilizando um módulo pra facilitar esse processo, o "body-parser", que pode ser instalado via npm utilizando o comando `npm install --save body-parser` e importado para o projeto da seguinte forma.

```text
const bodyParser = require('body-parser');
```

Agora, é preciso criar um middleware do body-parser:

```text
const express = require('express');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

app.use(bodyParser.urlencoded({extended: false})); // New line

app.use('/add-product', (req, res) => {

    res.send('<form action="/product" method="POST"><input type="text" name="title"><button type="submit">Add Product</button></form>')
})

app.use('/product', (req, res) => {

    console.log(req.body);
    res.redirect('/');
})

app.use('/', (req, res) => {
    res.send('<h1>Here is your response Sir.!</h1>');
})

app.listen(port);
```

Acima a nova linha está criando um middleware no qual há a utilização de uma função do módulo body-parser `urlencoded()` , tal função não serve como coringa para fazer o parsing de todos os tipos de body porém é útil para fazer o parsing nesses casos de requisições via POST ou GET, dentro da função passei um objeto, esse no qual se pode utilizar de algumas configurações, a utilizada `extended` faz com que o parsing aconteça apenas com requisições não-padrões.

Se executo o código agora, posso receber o dado do formulário em `console.log('req.body')` . O resultado será um objeto em javascript, que terá como chave o nome dado a tag `<input>` e como valor, o que foi inserido no input e enviado via requisição POST como ação do `<form>` .

```text
[Object: null prototype] { title: 'Book' }
```

Algo a se notar até aqui, é o fato de que um envio de requisição vai ser enviado via url, é comum se ver a mudança constante de url's enquanto se navage em qualquer site, mesmo que essa requisição não significa a mudança de página, ainda sim ela poderá significar o envio de algum dado via POST ou GET.

### Limitando a Execução do Middleware para Métodos POST

Aqui vai um conceito importante, já escrevi essa implementação anteriormente utilizando apenas o Node.js porém, mais uma vez o Express fazendo seu papel de facilitar as coisas, no código com node utilizei os operadores lógicos para execução da resposta apenas se a `url === '/exemplo' && method==='POST'` , utilizando o Express podemos filtrar o tipo de requisição que deve ser considerado trocando o a função `app.use()` por `app.get()` ou `app.post()` , assim pode-se filtrar o tipo de requisição aceita para a execução de um middleware específico. Com o exemplo utilizado acima, devemos filtrar uma requisição da url com o método POST, pois foi assim que defini na tag de formulário.

```text
const express = require('express');
const bodyParser = require('body-parser');

const app = express();
const port = 3000;

app.use(bodyParser.urlencoded({extended: false})); // New line

app.use('/add-product', (req, res) => {

    res.send('<form action="/product" method="POST"><input type="text" name="title"><button type="submit">Add Product</button></form>')
})

app.post('/product', (req, res) => { // Troca de 'use' p/ 'post'

    console.log(req.body);
    res.redirect('/');
})

app.use('/', (req, res) => {
    res.send('<h1>Here is your response Sir.!</h1>');
})

app.listen(port);
```

Dessa forma a requisição só irá executar o middleware se houver acontecido derivada de um método POST.

## Organizando Código Usando Express Router

Pra ter uma melhor organização de código poderia usar o sistema de modularização do Node.js, com exports e imports nos códigos do projeto, porém o Express tem uma maneira diferente de fazer esse mesmo papel. Assumindo que o projeto gira em torno de uma loja online, a nova estrutura de pasta e arquivos fica assim:

Nesse caso a pasta `routes` foi criada para que armazene as rotas principais, os blocos de funcionalidades de adição e remoção de produtos que só cabem ao administrador e as funcionalidades de funcionamento geral da loja.

A página inicial terá como front-end o código escrito em "shop.js ".

Todo o código das rotas `/add-product` e `/product` vão pertencer agora ao arquivo "admin.js", e rotas mais gerais como a rota inicial `/` vai fazer parte do arquivo "shop.js".

### Exportando e Importando

Abaixo passo o código que será referente as atividades do administrador para o arquivo referente:

```text
const express = require('express');

const router = express.Router();

router.get('/add-product', (req, res) => {

    res.send('<form action="/product" method="POST"><input type="text" name="title"><button type="submit">Add Product</button></form>')
})

router.post('/product', (req, res) => {

    console.log(req.body);
    res.redirect('/');
})

module.exports(router);
```

Essa é a forma com que o Express facilita a exportação do código, `express.Router()` funciona como um mini-servidor, então como se pode ver nas outras linhas, assim como apliquei anteriormente, os métodos `get` e `post` também funcionam se direcionados a variável do `route` visto que só faziamos isso na variável do servidor.

A forma de importação também é diferente do convencional quando feita com a utilização do framework.

```text
const express = require('express');
const bodyParser = require('body-parser');

const adminRoute = require('./routes/admin'); // Importando o módulo

const app = express();
const port = 3000;

app.use(bodyParser.urlencoded({extended: false}));



app.use('/', (req, res) => {
    res.send('<h1>Here is your response Sir.!</h1>');
})

app.listen(port);
```

Dessa vez é necessário referenciar o arquivo "admin.js" na pasta routes. O que se deve saber agora é que o nosso `router` age como um middleware, sendo possível utilizar seus middlewares da seguinte forma:

```text
const express = require('express');
const bodyParser = require('body-parser');

const adminRoute = require('./routes/admin')

const app = express();
const port = 3000;

app.use(bodyParser.urlencoded({extended: false}));

app.use(adminRoute);

app.use('/', (req, res) => {
    res.send('<h1>Here is your response Sir.!</h1>');
})

app.listen(port);
```

A ordem de uso dos middlewares aqui é importande, vai seguir o mesmo padrão gravitacional feito anteriormente. Com o uso de métodos `get` ou `post` no objeto servidor, filtra-se assim qualquer url não tratada em middlewares, visto que dentro do middleware `adminRoute` temos os filtros de requisição devidamente aplicados.

### Adicionando Página de Erro 404

O famoso erro 404 acontece quando uma requisição de algo que não tem resposta do servidor, podemos tratar o erro de "sem retorno para tal requisição" com redirecionamento para uma página que indique isso, é basicamente um tratamento de erro no qual serve principalmente para não deixar perdido quem está tentando acessar uma página inexistente. Abaixo segue como se faz o redirecionamento de requisições que não tem responses no servidor. Deve ainda ficar claro aqui que o uso do `.use()` aqui captura qualquer requisição feita pelo navegador, por isso lá no arquivo raiz "admin.js" se utiliza o `.get()` ou `.post()` por isso ainda não se consegue a resposta e o browser retorna o erro já reportado.

Sendo assim faz sentido que a captura de uma url qualquer que não tem resposta pré-definida caia na implementação do `app.use()` .

```text
const express = require('express');
const bodyParser = require('body-parser');

const adminRoutes = require('./routes/admin');
const shopRoutes = require('./routes/shop');

const app = express();
const port = 3000;

app.use(bodyParser.urlencoded({extended: false}));

app.use(adminRoutes);
app.use(shopRoutes);

// Capturing any request that not have a defined response

app.use((req, res, next) => {
    res.status(404).send('<h1>Page not Found!</h1>')
});

app.listen(port);
```

Antes da execução do método de envio no código de captura de uma requisição qualquer, utilizei uma função anterior `res.status(404).send('<h1>Page not Found!</h1>')` foi a utilização de `status(404)` o que me permitiu retornar minha resposta juntamente com o código de status, normalmente se utiliza o 404 para requisições sem resposta devida, há outras implementações que podem ser utilizadas antes do `send()` , como por exemplo o `setHeader` porém enfatizo que o `send()` deve estar sempre como última execução para que a response seja enviada de volta ao navegador.

### Filtrando Caminhos

Aprimorando os códigos anteriores, há uma forma de ter dois middlewares com o mesmo caminho de url como requisição porém com dois resultados diferentes, como uma resposta para requisições dessa url feitas com POST e uma resposta para requisições feitas dessa url com método GET, tendo os dois middlewares na mesma página o uso desse middleware no código do servidor principal, muda para que filtre os caminhos e execute apenas os middlewares provindos do arquivo `admin.js` . Abaixo segue o exemplo:

```text
// Código do arquivo admin.js

const express = require('express');

const router = express.Router();

router.get('/add-product', (req, res) => {

    res.send('<form action="/admin/add-product" method="POST"><input type="text" name="title"><button type="submit">Add Product</button></form>')
})

router.post('/add-product', (req, res) => {

    console.log(req.body);
    res.redirect('/');
})

module.exports = router;
```

Pode-se observar que agora há duas respostas para duas requisições da mesma url com métodos diferentes e o `action` da tag "input" agora aponta para o `/add-product` utilizando POST. Se pode perceber também que a requisiçã do "input" procura por um middleware derivado de `/admin/add-product` , sendo assim é mais um filtro adiconado. Agora no arquivo main.js:

```text
const express = require('express');
const bodyParser = require('body-parser');

const adminRoutes = require('./routes/admin');
const shopRoutes = require('./routes/shop');

const app = express();
const port = 3000;

app.use(bodyParser.urlencoded({extended: false}));

app.use('/admin',adminRoutes); // Mais um mecanismo de filtro
app.use(shopRoutes);

app.use((req, res, next) => {
    res.status(404).send('<h1>Page not Found!</h1>')
});

app.listen(port);
```

