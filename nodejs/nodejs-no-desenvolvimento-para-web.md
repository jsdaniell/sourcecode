# Node.js no Desenvolvimento para Web

## Atuação

Existem três principais "roles" onde o Node.js pode atuar no desenvolvimento web, são elas:

* **Executar o servidor**

Diferente do PHP, que precisaria de módulos externos para ouvir requisições, o Node.js faz tanto o "listening" de requisições, como roda o servidor \(enviando respostas\) ao mesmo tempo, basicamente executa o servidor e escuta as requisições, e então ao capturar os "listenings" envia a resposta programada pelo desenvolvedor \(se houver\).

* **Lógica de Negócio**

Pode ser utilizado nesta camada \(podemos dizer assim\) para manipular requisições \(Handle Requests\), validar 'inputs' e se conectar á um banco de dados, é basicamente a parte de manipulação das requisições.

* **Responses**

Retornar Responses \(rendered HTTP, JSON,...\)

É Importante ressaltar que o Node.js nos permite executar Javascript em sistemas operacionais, ou seja,  fora do browser e assim através da linguagem podemos construir e rodar o servidor com métodos POST, GET e etc.

Diversas vezes quando se cria servidores em Node.js se utiliza de módulos que oferecem códigos pré-escritos que aprimoram a experiência de desenvolvimento. Basicamente deixa as implementações mais fáceis inclusive a criação de API's, um exemplo é o Express.js que abstrai bastante complexidade do que faríamos com Node.js puro, deixando o código mais consistente e limpo, além de outras funcionalidades e integrações, isso permite que ao desenvolver uma aplicação, o foco principal será o desenvolvimento da lógica de negócio da aplicação, sem perder tempo reinventando a roda.

## As Responsabilidades do Node.js

![Funcionamento b&#xE1;sico do Servidor/Cliente.](../.gitbook/assets/untitled%20%281%29.png)

### Onde fica o código implementado?

A imagem acima ilustra bem onde vamos implementar códigos escritos em Javascript, ao requisitar uma URL no nosso browser, o navegador manda uma requisição ao servidor do seu provedor de internet em busca do endereço IP do site requisitado \(através do domínio\) então o que é retornado pelo servidor ao navegador pode ser escrito em Node.js. Essa resposta pode conter um arquivo HTML por exemplo, além disso ela também tem "headers" que são informações sobre o que contém dentro do que está sendo retornado ao navegador.

Há inúmeras possibilidades para implementação de código ao lado do servidor, algumas delas são:

* Validação de dados
* Acesso a um banco de dados

### Requisições e Respostas - Protocolo HTTP / HTTPS

Como visto na imagem o browser requisita ao servidor por exemplo a nossa página HTML e o nosso código Node.js manda de volta essa página através do servidor, para acontecer esses envios e respostas existem algumas regras que devemos seguir, regras essas estipuladas pelos protocolos HTTP / HTTPS, os principais protocolos para transmissão de dados entre cliente \(browser\) e servidor.

O protocolo HTTP significa Hyper Text Transfer Protocol, o que controla as regras de transferências entre o browser e o servidor, sua diferença quanto ao HTTPS \(Hyper Text Transfer Protocol Secure\) é que o HTTPS encripta os dados durante a transmissão entre cliente e servidor.

