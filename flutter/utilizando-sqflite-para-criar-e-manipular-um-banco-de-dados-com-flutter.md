# Utilizando SQFlite para criar e manipular um banco de dados com Flutter

Detalhamentos sobre a implementação de banco de dado relacional utilizando o plugin SQFlite para persistência dos dados na aplicação.

### Dependências e projeto da aplicação

Pra facilitar o entendimento disponibilizei o código ao qual vou me referenciar através deste [link](https://gist.github.com/jsdaniell/84d534e5a4ac8b44fb5b51f3f5cf7ac4) do Github Gist.

### Abaixo temos as seguintes depêndencias de plugins utilizadas para o nosso objetivo

```dart
import 'package:path/path.dart';
import 'package:sqflite/sqflite.dart';
```

{% hint style="info" %}
Como utilizamos o código do Flutter para compartilhar entre as plataformas Android e IOS, utilizamos o pacote [path](https://pub.dartlang.org/packages/path) da biblioteca do Dart para localizar de forma mais automatica os locais padrões de cada _device_ para alocar o banco de dados.

E para completar utilizamos o plugin [sqflite](https://pub.dartlang.org/packages/sqflite) para criar e manipular o banco de dados.
{% endhint %}

### Definindo as colunas que vão compor o db com variáveis

```dart
final String contactTable = "contactTable";
final String idCol = "idCol";
final String nameCol = "nameCol";
final String emailCol = "emailCol";
final String phoneCol = "phoneCol";
final String imgCol = "imgCol";
```

{% hint style="info" %}
Lembremos que todas as variáveis foram declaradas globalmente.
{% endhint %}

Aqui é onde começa a primeira definição sobre as colunas que vão compor o banco, cada variável tem o mesmo nome e valor, respectivos a coluna \(o nome e valor das variáveis podem ser alterados\). Lembre-se neste caso, cada variável representa uma **String** com o seu mesmo nome como valor, por quê o que o banco vai utilizar para compor o nome das colunas é seu respectivo valor.

Aqui temos uma variável para armazenar apenas o nome da tabela do banco.

## Iniciando a classe `ContactHelper {}`

### Utilizando o Sigleton Pattern

```dart
ContactHelper.internal();

static final ContactHelper _instance = ContactHelper.internal();

factory ContactHelper() => _instance;
```

_Para um melhor entendimento, mudei a ordem das linhas quanto ao arquivo original._

Aqui temos algo mais interessante, se você já leu algum livro sobre _Design Patterns_ deve ter visto algo sobre o **Singleton Pattern**, onde implementa um padrão bastante conhecido, onde só haverá uma única instância da classe, mesmo se você tentar isso:

```dart
db_one = new ContactHelper();
db_two = new Contact Helper();
```

As duas linhas de comando acima estarão referenciando a mesma classe, o mesmo banco de dados existirá, e nenhuma outra instância será criada.

Na primeira linha um construtor privado é criado, então uma váriavel chamada `_instance` do tipo da classe `ContactHelper` recebe o construtor privado, e então um `factory` da classe ContactHelper executa o bloco de código no qual chama `_instance` que representa o mesmo conteúdo da classe antes instanciada a primeira vez, por isso sempre é referenciada a mesma instância do objeto.

[Outras formas de implementar o padrão singleton em Dart](https://stackoverflow.com/questions/54057958/comparing-ways-to-create-singletons-in-dart).

### Criando o banco de dados

Para criar o banco de dados precisamos vamos guardar um tipo `Database` que pertence ao plugin _sqflite_ em uma variável privada, chamada `_db`.

A partir daqui devemos saber que maior parte senão todas as funções do plugin SQFlite executam de forma assíncrona, não vou entrar a fundo em funções assíncronas, isso é assunto para outra parte dessa wiki. Enfim vamos prosseguir com o código.

```dart
Future<Database> get db async {
    // Now the method to know if the database isn't null

    if (_db != null) {
      return _db; // If isn't null returns a _db registered on Database type
    } else {
      _db = await initDb(); // If null (not created), initiate a new database

      /* The initDb() is a asynchronous function, so have to be a Future object
      * to return */

      return _db;
    }
}
```

Acima declaramos uma condicional para testar se o banco de dados já foi criado anteriormente, como consequência, se houver sido criado então irá retornar o `_db`\(Database\)\`já existente, caso contrário irá executar a função `initDb` função no qual inicializará um novo `_db`\(Database\), nos dois finais o retorno será o `_db`, sendo o já existente ou o novo.

### Inicializando um novo banco de dados

Aqui vai mais uma função assíncrona que irá retornar um tipo `Database` futuro.

```dart
 Future<Database> initDb() async {

   final databasesPath = await getDatabasesPath();
   final path = join("databasesPath", "contacts.db");
}
```

Aqui temos a função raiz do nosso banco de dados, será aqui que o banco vai ser criado a primeira vez, primeiro declaramos o caminho do diretório no qual o banco de dados vai ser armazenado, pra isso é utilizado uma funcionalidade da biblioteca [path](https://pub.dartlang.org/packages/path) do Dart, e atribuímos esse caminho a uma variável:

```dart
final databasesPath = await getDatabasesPath();
```

Vale lembrar que esses processos que demandam um carregamento para obter o caminho de diretórios do aparelho, então essas funções são assíncronas, o `await` então espera que o `getDatabasesPath()` finalize para que atríbua o caminho a variável.

```text
final path = join("databasesPath", "contacts.db");
```

Aqui juntamos o endereço do diretório com o arquivo `contacts.db` e atribuímos a essa variável.

```dart
return await openDatabase(path, version: 1,
        onCreate: (Database db, int newerVersion) async {
      // The SQL command to initiate

      await db.execute(
          "CREATE TABLE $contactTable($idCol INTEGER PRIMARY KEY, $nameCol TEXT, $emailCol TEXT, $phoneCol TEXT, $imgCol TEXT)");
    });
```

Agora que já temos o diretório com o arquivo, podemos manipula-lo com os comandos SQL, e como retorno da função, vamos abrir o banco de dados, utilizamos a função `openDatabase`\(sqflite\) passando o arquivo do banco juntamente com o caminho\(`path`\) e a versão do banco\(parâmetro requerido por openDatabase\) e então mais um parâmetro especificando qual comando será executado no `onCreate`.

Para executar a inicialização do novo banco de dados, referenciamos o `db.execute` e como parâmetro o comando SQL:

`"CREATE TABLE $contactTable($idCol INTEGER PRIMARY KEY, $nameCol TEXT, $emailCol TEXT, $phoneCol TEXT, $imgCol TEXT)");`

E isso tudo retorna como para dentro da variável `_db` declarada no escopo `if` e `else` para verificação da existência do banco.

## O que vai ser armazenado?

Agora que já temos as colunas do banco devidamente prontas, devemos ter as funcionalidades de manipulação do banco.

### A classe Contact

Você deve estar se perguntando o que exatamente iremos salvar no banco de dados? Pra deixar mais claro no final do arquivo temos a declaração da classe contato, com todas as informações que um contato tem. É importante saber que é aqui onde vai ser criado o objeto que será armazenado no banco de dados.

```dart
class Contact {
  int id;
  String name;
  String email;
  String phone;
  String img;

  // Recovering the data from map database to a normal object
  // The name of method "fromMap" is created by user
  // So this is a simple function that's inside contact and receive a Map as parameter

  // Empty constructor

  Contact();

  Contact.fromMap(Map map) {
    // Putting the columns variables inside class variables

    id = map[idCol];
    name = map[nameCol];
    email = map[emailCol];
    phone = map[phoneCol];
    img = map[imgCol];
  }

  // Putting the object values inside a map

  Map toMap() {
    Map<String, dynamic> map = {
      nameCol: name,
      emailCol: email,
      phoneCol: phone,
      imgCol: img,
    };

    // When the object is created, it's don't have an id
    // So the database have to attribute the id
    // If the object isn't null, the id variable is set to idCol

    if (id != null) {
      map[idCol] = id;
    }

    // In the end of all, this function returns a map

    return map;
  }

  // Overriding toString method to show better the information

  @override
  String toString() {
    return "Contact (id: $id, name: $name, email: $email, phone: $phone, img: $img)";
  }
}
```

{% hint style="info" %}
O contato acima se refere a um contato de telefone.
{% endhint %}

### Declaração dos dados do contato

```dart
  int id;
  String name;
  String email;
  String phone;
  String img;
```

Nesta declaração de atributos da classe contato, devemos declarar as variáveis que vão compor as colunas criadas anteriormente no banco de dados.

### Transformando o objeto em um map

```dart
  Map toMap() {
    Map<String, dynamic> map = {
      nameCol: name,
      emailCol: email,
      phoneCol: phone,
      imgCol: img,
    };

    // When the object is created, it's don't have an id
    // So the database have to attribute the id
    // If the object isn't null, the id variable is set to idCol

    if (id != null) {
      map[idCol] = id;
    }

    // In the end of all, this function returns a map

    return map;
}
```

Para o funcionamento do plugin SQFlite qualquer objeto que for adentrar o banco de dados deve ser transformado em um mapa primeiro, faz sentido, pois em um mapa em Dart cada valor tem uma chave, que neste caso irá representar a coluna do banco de dados.

Na linha 2 especificamos os tipos que vão compor o map e declaramos uma variável chamada `map` e dentro dela declaramos as chaves com o nome das colunas que declaramos no banco de dados e os valores das chaves serão os valores das variáveis do contato.

Na linha 13 criaremos um id para o objeto que está sendo transformado em map.

No final é retornado o map em questão, com suas respectivas chaves.

### Recuperando o map para objeto

```dart
  Contact.fromMap(Map map) {
    // Putting the columns variables inside class variables

    id = map[idCol];
    name = map[nameCol];
    email = map[emailCol];
    phone = map[phoneCol];
    img = map[imgCol];
}
```

Para exibirmos o contato na aplicação, devemos transformar o map de volta em objeto, então os respectivos valores do map são atribuídos novamente ás variáveis.

## Fazendo funcionar!

Agora que já temos o banco de dados e os dados que vai o compor, já podemos criar as funcionalidades que irão manipular essas duas coisas. Devemos lembrar que todas as funcionalidades aqui são assíncronas, devido a requisição em diretórios do aparelho não serem instantâneas as funcionalidades e retornos necesitam de um carregamento para se efetivarem.

### Salvando o contato no banco

```dart
  Future<Contact> saveContact(Contact contact) async {
    Database dbContact = await db;

    // Here the method toMap catch the attributes of table and pass toMap

    contact.id = await dbContact.insert(contactTable, contact.toMap());
    return contact;
}
```

O primeiro método que implementamos é o de salvar um contato no banco de dados, recuperamos o banco de dados para uma variável local `dbContact` e então atríbuimos a ela o seguinte:

```dart
dbContact.insert(contactTable, contact.toMap());
```

O método insert vem da API do SQFlite, o que obedece a seguinte regra:

```dart
Future<int> insert(String table, Map<String, dynamic> values,
      {String nullColumnHack, ConflictAlgorithm conflictAlgorithm});
```

Como podem ver essa função retorna um inteiro que é atribuído como id ao contato em questão. E então retorna o contato.

### Deletando o contato

```dart
  Future<int> deleteContact(int id) async {
    Database dbContact = await db;
    return await dbContact
        .delete(contactTable, where: "$idCol = ?", whereArgs: [id]);
}
```

Para deletar o código não muda muito, só o fato do uso da função `delete` do SQFlite que recebe a tabela em questão e a condição dizendo que a coluna tem de ser igual ao id do db.

### Update do contato

```dart
  Future<int> updateContact(Contact contact) async {
    Database dbContact = await db;
    return await dbContact.update(contactTable, contact.toMap(),
        where: "$idCol = ?", whereArgs: [contact.id]);
}
```

Mais um comando do SQFlite `update` passando a tabela, o map e impondo a condição de que os id's devem ser iguais.

```dart
  Future<List> getAllContacts() async {
    Database dbContact = await db;
    List listMap = await dbContact.rawQuery("SELECT * FROM $contactTable");
    List<Contact> listContact = List();
    for (Map m in listMap) {
      listContact.add(Contact.fromMap(m));
    }
    return listContact;
  }

  Future<int> getNumber() async {
    Database dbContact = await db;
    return Sqflite.firstIntValue(
        await dbContact.rawQuery("SELECT COUNT(*) FROM $contactTable"));
}
```

Acima mais algumas funções explicítando como pegar todos os contatos do banco e como pegar uma informação específica.

Lembre-se que toda vez que instânciamos o db, ele faz todo aquele processo de verificação se o banco já existe e o retorna.

### Fechando o banco de dados

```dart
  Future close() async {
    Database dbContact = await db;
    dbContact.close();
}
```

{% hint style="danger" %}
Esta funcionalidade não foi utilizada no projeto.
{% endhint %}

## Utilizando o banco de dados

Agora com toda a nossa classe do banco de dados podemos utiliza-lo em nossa aplicação para inserção e remoção dos dados, para exemplificar o uso, deixei minha interface do aplicativo feita em outro arquivo, toda a aplicação pode ser encontrada [aqui](https://github.com/jsdaniell/contact_list_app).

```dart
  ContactHelper helper = ContactHelper();

  // Declaring the list of contacts, it's empty

  List<Contact> contacts = List();

  @override
  void initState() {
    super.initState();
    _getAllContacts();
  }
```

A primeira coisa a se fazer é adicionar uma instância do banco no seu `StatefulWidget`, então poderá usar os códigos em qualquer lugar, em seguida vamos sobrescrever o método `initState()` que por padrão inicializa a classe, então utilizamos o `_getAllContacts` para conseguir todos os contatos do banco.

### Refenciando os itens do banco

```dart
Text(
  contacts[index].name ?? "",
  style: TextStyle(
  fontSize: 22.0, fontWeight: FontWeight.bold),
),
Text(
  contacts[index].email ?? "",
  style: TextStyle(fontSize: 18.0),
),
Text(
  contacts[index].phone ?? "",
  style: TextStyle(fontSize: 18.0),
),
```

Acimas temos uma estrutura utilizada em um widget avulso chamado \_contactCard, no qual pega um `index` do contacts referindo seu atributo após o `.` e apontando se há uma condição de `""` \(vazio\) nesses atributos.

### Lista dos contatos

```dart
ListView.builder(
        padding: EdgeInsets.all(10.0),
        itemCount: contacts.length,
        itemBuilder: (context, index) {
          return _contactCard(context, index);
        },
      ),
```

Na nossa lista de itens, o widget no qual pega as informações é referenciado no retorno do widget de lista passando o `context` e o `index`.

