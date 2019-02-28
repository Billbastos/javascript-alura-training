Não esqueçam de entrar dentro da pasta `client` e executar o comando `npm install` antes de utilizar o projeto.

Para ativar a compilação em tempo real, dentro de `client`, execute o comando `npm start`. Estamos usando Webpack agora!

------------------------------
CURSO 1
------------------------------

1 - Relembrar é viver:
------------------------------
JS Basics:
- var td = document.querySelector('#someTdId');
- td.textContent = 'Valor entre tags da td';
- e.addEventListener('submit', function() {...});
- var tr = document.createElement('tr');
- tr.appendChild(td);
- event.preventDefault(); // No caso do form submit, nao recarrega a pagina;

Apenas com o JS basico nao ha separacao de responsabilidades, entao aplicaremos o padrao MVC para separar Modelo (camada de negocios) da View.

2 - JS Orientado a Objetos:
------------------------------
- ES6 -> Classes para criar modelos, antes disso Prototypes;
- Estrutura do projeto:
  -> CSS
  -> JS -> app  -> controllers
                -> helpers
                -> models
                -> services
                -> views
        -> lib
- Criar um arquivo de Negociacao.js dentro da pasta models;
  - class Negociacao { ... }
  - constructor( prop ){ this.prop = prop }
  - `this._prop` Propriedade privada por convesao coloca-se o `_`
  - get prop() { return this.prop } // Para usar esse codigo fora da classe, basta usar `instancia.prop`
  - `Object.freeze( this )`; // Shalow freeze. Incluir dentro do construtor para nao alterar a prop interna do obj para checar se esta congelado, basta `Object.isFrozen()` mesmo conceito de objetos imutaveis;
  - `var` ou `let`. Usar sempre let para evitar da variavel vazar o escopo de bloco;
  
3 - Criando o Controller:
------------------------------
  - Local micro jquery: `let $ = document.querySelector.bind(document)` para usar, basta `$('#prop)`;
  - Evitar percorrer o DOM a todo o momento (Boa pratica JS para performance);
  - Percorrer DOM no constructor da classe pois so roda uma unica vez;
  - `typeof( obj )` // Devolve o tipo do objeto;
  - `.split('')` transforma String em array usando um separador;
  - `.join('')` transforma Array em String usando um separador;
  - `.replace(/-/g, '')` replace all '-' por '';
  - ... spread operator ES6 e.g. `new Date(...array.split('-'))`
  - Metodos estaticos. `DataConverter.paraTexto(data)`. 
    class DataConverter { 
      constructor() { `throw new Error('Nao pode ser instanciado')` }
      `static` paraTexto(data) { return "dataTexto" + data }
    }
  - Template strings `Ola Mundo ${new Date()}` usando 'back stick';
  - Regex `/\d{3}-\d{2}/.test( string )` testa se string possue formato: `000-00`
  - Retornando um array imutavel `return [].concat( array )`;
  - Removendo todos os itens do array: `array.length = 0`;
  - `loose equal` == does the implicity coerce "42" is equal to 42, `strict equal` === doesn't do that.
  - In JavaScript, functions are first-class objects, which means they can be: `stored in a variable, object, or array`, `passed as an argument to a function`, `returned from a function`;
  - A higher order function is a function that takes another function as an input, returns a function or does both. Built-in HOF are `filter()`, `map()`, `reduce()`;
  
4 - Criando a View:
------------------------------
  - Por convencao, criar uma classe para view que possue um metodo que retorna o template() { return `<table></table>`}
  - e.innerHTML = `<div></div>` (innerHTML Converte string em elemento do DOM);
  - A view trabalha com tres pilares `template`, `elemento onde aplicar o template`, `dados do modelo`;
  - Template string aninhada com interpolacoes aninhadas `<tbody> ${data.map(n=>`<tr>${n.data}</tr>`).join('')}</tbody>`
  - IIFE - Immediately Invoked Function Expression `(function(){ return 0})()`
  - Usando o reduce para somar o volume. `lista.reduce((volume,item)=>volume+item.valor, 0)` 

5 - Enviando mensagens para o usuario:
------------------------------
  - Criar a classe Mensagem que recebe texto no `constructor(texto = ''){...}` Valor padrao '' na criacao do objeto Mensagem.
  - Criar `get texto(){...}` uso: mensagem.texto;
  - Criar `set texto(texto){...}` uso: mensagem.texto = 'x';
  - Criar a view para a mensagem MensagemView que `recebe o elemento do DOM no construtor(elem)`, `metodo template(model) que recebe o texto e devolve o HTML`, `metodo update(model) que associa o html ao elemento do construtor`;
  - Se `a = '', null, false, undefined ou 0` é avalido como `false` quando a ? true : false;

6 - Criando Heranca em JS:
------------------------------
  - Criar uma classe View que `recebe o elemento do DOM no construtor(elem)`, `metodo template(model) que recebe o texto e devolve throw new Error()`, `metodo update(model) que associa o html ao elemento do construtor`;
  - MensagemView e NegociacoesView devem herdar View e.g. `class NegociacoesView extends View { ... }`
  - Retirar os metodos `construtor(e)` ou aproveitar o `super(e)` e `update(data)` das classes MensagemView e NegociacoesView pois essa logica estara na classe mae View;
  - O metodo template dentro das filhas sobreescreve o template da classe View;
  
------------------------------
CURSO 2
------------------------------

1 - Modelo atuzaliza a View:
------------------------------
  - Arrow function possui escopo lexico `this` enquando que uma function tem escopo dinamico.
  - e.g. fn.roda(`this`, function(){ `this` }); O this tem escopo de quem chama a funcao, tvz seja outro lugar/escopo diferente de fn.roda;
    - Para isso funcionar, deveria haver uma maneira de mandar o contexto e rodar `Reflect.apply(fn, contexto, [])` (ES6);
    - `fn.apply(contexto, [])` mesma coisa com ao (ES5);
  - e.g. fn.roda(()=>`this`); Esse this tem mesmo escopo da fn.roda e nao necessita de contexto ja que mantem o contexto `this` onde foi criado.
  - Reaproveitamento de codigo usando mixins.
    `e.g.`
    voa() {
        // executa o método `voa` de `Avião` usando como contexto a instância de `Passarinho`
        Reflect.apply(`Aviao.prototype.voa`, this, []);
    }
  - Observer é o padrao usado que `observa` (lanca a armadilha) para alguma acao de `adiciona`, `atualiza` e roda a armadilha dentro desses metodos;
  - Proxy: é um padrao de projeto que tira a infraestrutura da classe de modelo e coloca no Proxy que delega o acesso aos metodos do modelo. Resumindo, o app/controller acessa o proxy ao inves da classe do modelo.
    - e.g. `let o = new O('arg')` criando o proxy de O `let proxy = new Proxy(new O('arg'),{ ... };`);
    - definindo os handlers para incluir a infraestrutura `trap` no proxy;
    - , {
      `get(target, prop, receiver)`{
        // target: referencia ao objeto original `new O('arg')`
        // prop: a propriedade acessada
        // receiver: referencia ao proprio Proxy
        `return Reflect.get(target, prop, receiver)` // retorna o valor da prop interceptada
      }

      `set(target, prop, value, receiver)`{
        // target: referencia ao objeto original `new O('arg')`
        // prop: a propriedade alterada
        // value: novo valor da propriedade
        // receiver: referencia ao proprio Proxy
        
        `return Reflect.set(target, prop, value, receiver)` // seta o valor da prop interceptada
      }
    }
  
2 - Bind, Proxy e Factory:
------------------------------
  - O padrao factory é uma classe que contem um metodo estatico para a criacao do objeto em questao `static create(objeto, propriedades, acao){...}`.
  - `Bind` class associa o Model com a View. Recebe a view, model, propriedades para interceptacao do proxy no construtor e devolve o proxy configurado. return new ProxyFactor( model, props, armadilha = `(model) => view.update(model)`)
  -  ... REST parameters ES6 e.g. `function fn(a, b, ...c)`; fn(1,2,`3,4,5`); `...c` sera um array dentro da funcao c.forEach( ... );

3 - Criando Servicos
------------------------------
  - Importando negociacoes atraves de Ajax para o Servidor
  - Usando JS puro para a criacao do `XMLHttpRequest`
    - `let xhr = new XMLHttpRequest();`
    - `xhr.open('GET', 'negociacoes/semana');`
    - `xhr.onreadystatechange() = () => { ... }` //Adicionando as Configuracoes
    - Estados de uma requisicao AJAX (readyState):
      `0: req nao iniciada`, 
      `1: conn com o server estabilizada`, 
      `2: requisicao recebida`, 
      `3: processando`, 
      `4: req concluida e resp pronta`;
    - `if( xhr.readyState == 4 && xhr.status == 200) { ... }`
    - { xhr.responseText }
      - Parseando o texto para Array JS: `JSON.parse( xhr.responseText )`
      - mapeando o array convertido para o Objeto Negociacao:
        - JSON.parse(xhr.responseText)
            .map(o => new Negociacao( new Date(o.data), o.quantidade, o.valor ))
            .forEach( negociacao => this._lista.adiciona( negociacao ))
    - Adicionado HTTP headers: `xhr.setRequestHeader("Content-Type", "application/json");`
    - `xhr.send();`
    - Enviando dados no POST: `xhr.send( JSON.stringify(dado) )`
    - Criando a classe de Servico :

    class NegociacaoService {

      obterNegociacoesDaSemana( callback ) {

        let xhr = new XMLHttpRequest();
        xhr.open();
        xhr.onreadystatechange = () => {
          if(xhr.readyState == 4 && xhr.status == 200 ) {
              
            callback( null, JSON.parse(xhr.responseText)
              .map(o => new Negociacao( new Date(o.data), o.quantidade, o.valor )));

          } else {
            console.log(xhr.responseText);
            callback( 'Nao foi possivel obter as negociacoes' );
          }
        }
        xhr.close();
      }
    }

    - `Chamando o Service atraves de callback no Controller:`
      service.obterNegociacoesDaSemana(`(err, nogociacoes)`=>{
        if(err) {
          mensagem.texto = err;
          return;
        }
        nogociacoes.forEach( n => this.lista.adiciona( n ) );
        mensagem.texto = "Sucesso!!!";
      });

    - Se uma chamada assincrona dependesse de uma ordem teriamos o tal `callback hell` ou `piramide de doom`;

    service.obterNegociacoesDaSemana(`(err, nogociacoes)`=>{
        if(err) {
          mensagem.texto = err;
          return;
        }
        nogociacoes.forEach( n => this.lista.adiciona( n ) );

        service.obterNegociacoesDaSemanaAnterior(`(err, nogociacoes)`=>{
          if(err) {
            mensagem.texto = err;
            return;
          }
          nogociacoes.forEach( n => this.lista.adiciona( n ) );

          service.obterNegociacoesDaSemanaRetrasada(`(err, nogociacoes)`=>{ ... }
      });
    });

4 - Padrao de Projetos Promise
------------------------------
  - Toda chamada da promise devolve a propria promisse entao a chamada ficaria:
    let promise = service.obtemSemana();
    `promise`
      `.then( list => list.forEach( n => list.add(n)))`
      `.catch( err => mensagem.texto = err );`
  - Deveolvendo uma Promise atraves do Service;
  - obtemSemana() { return new Promise(( resolve, reject ) => { 
      // Em caso de sucesso!!!
      `resolve( negociacoes );`
      // Em caso de erro!!!
      `reject( mensagem );` 
    })}
  - Como criar uma ordem de chamada de Promises, a classe Promise possui um metodo estatico all();
    `Promise.all`([
      p1(), p2(), p3()
    ]).then( list => 
        list.reduce((arrayAchatado, array)=>arrayAchatado.concat(array),[])
            .forEach( n => list.add(n))
    ).catch( err => mensagem.texto = err )
    
5 - Criando o HTTPService
------------------------------
  - class HTTPService {
    get(`url`) {
      return new Promise((resolve, reject)=>{
        ...
        xhr.open('GET', `url`);
        ...
        `resolve(JSON.parse(xhr.responseText))`
        // If erro
        `reject(xhr.responseText)`
      })
    }
  }
  - Chamando `HTTPService` no `NegociacaoService`
      constructor(){
        this._http = new HTTPService()
      }

      oberSemana() {
        this._http
        .get(`negoiacoes/semana`)
        .then( resolve( ... ) ) 
        .catch( reject( ... ) );
      }

------------------------------
CURSO 3
------------------------------

1 - Persistencia no Indexed DB
------------------------------
  - Objeto global `indexedDB` retorna Fabrica de IndexDB
  - Pedindo requisicao de abertura de conexao do IndexDB `let request = indexedDB.open(nomebanco, 1)`
    - Triade de eventos qndo tentasse abrir uma conn com o banco:
    1) `request.onupgradeneeded = (e) => { ... } ` // Cria ou altera BD exixtente;
    2) `request.onsuccess = (e) => { ... };` // Obtem a conexao
    3) `request.onerror = (e) => { e.target.error };` // Error na conexao

  - Pegando uma conexao do Banco e criando um objectStore:
    1) dentro do onsuccess = (e) => { connection = `e.target.result` }
    2) dentro do onupgradeneeded = (e) => { let mConnection = `e.target.result;` }
      2.1) ainda dentro do onupgradeneeded, criar o obj store `mConnection.createObjectStore('negociacoes')`
      2.2) Se necessario alterar a ObjectStore criada, pode-se pesquisar se existe:
        - `mConnection.objectStoreNames.contains('negociacoes')`;
        - Deletar a store: `mConnection.deleteObjectStore('negociacoes')`;
        - Criar store com ID autoincrement: `mConnection.createObjectStore('nome', {autoIncrement: true})`;
    3) Adicionando dados no objectStore:
      3.1) criar uma funcao que cria uma transaction e uma store
        function add() { 
          let transacao = connection.transaction(['negociacoes'], 'readwrite');
          let store = transacao.objectStore('negociacoes');
          let negociacao = new Negociacao(new Date(), 1, 200);
          let request = store.add( negociacao );
          request.onsuccess = e => {}
          request.onerror = e => {}
        }
    4) Listando dados no objectStore:
      4.1) Criar uma funcao que cria uma transaction, uma store, e um cursor.
        function listAll() { 
          let transacao = connection.transaction(['negociacoes'], 'readwrite');
          let store = transacao.objectStore('negociacoes');
          let cursor = store.openCursor();
          let negociacoes = [];
          cursor.onsuccess = e => {
            let atual = `e.target.result` // Ponteiro para negociacao no banco
            if(atual) {
              let dado = atual.value;
              list.push(dado.data, dado.quantidade, dado.valor);
              `atual.continue();` // Muda para o proximo registro
            }
          }
          cursor.onerror = e => {}
        }

2 - Criando Connection Factory
------------------------------
  - Chamada da factory:
    ConnectionFactory
      .getConnection()
      .then(connection => {
    });

  - Regras do design da Classe dentro da pasta Services:

      A) O método `getConnection()` será um método estático, ou seja, invocado diretamente na classe.

      B) O `retorno de getConnection será uma promise`, pois a abertura de uma conexão é um processo assíncrono.

      C) Não importa quantas vezes seja chamado o método getConnection(), `a conexão retornada deve ser a mesma`.

      D) Toda conexão possui o método close(), mas o programador não pode chamá-lo, porque a conexão é a mesma para a aplicação inteira. `Só o próprio ConnectionFactory pode fechar a conexão`.
  
  - const stores = ['negociacoes']; 
    let connection = null;
    let close = null;
    // Variaveis fora do escopo da classe pois o objeto nao pode ser instanciado 

    class ConnetionFactory {
        
        constructor() {
          throw new Error('Nao pode ser instanciada');
        }

        static getConnection() {

          return new Promise(( resolve, reject ) => {
            
            let openrequest = windows.indexedDB.open( bdname, version );

            openrequest.onupgradeneeded = e => { //cria as stores }
            openrequest.onsuccess = e => {
              if (!connection) connection = e.target.result;
              resolve(connection);
              
              ...
            }
            openrequest.onerror = e => { ... }

            }
          });
        }
    }

3 - Module pattern
------------------------------
  - Resolve os problemas das viaveis globais no topo do arquivo
  - var `ConnectionFactory` = (function() {
      
      var a = ...
      `return` class ConnectionFactory {
        ...
        `// usa a`
      }
    })();
  - So exporta a classe ConnectionFactory o restante fica escondido dentro do modulo.

4 - Monkey patch
------------------------------
  - Resolvendo o problema de conseguir fechar uma conexao fora da classe
  - .then( `connection => connection.close()` );
  - request.onsucess = e => {
      if(!connection) {
        connection = e.target.result;
        `connection.close` = function() { //Sobreescrevendo o metodo close da API
          throw new Error('...')
        }
      }
    }
  - Criando um metodo especial para fechar a conexao
  - static closeConnection() {
      if(connection) {
        connection.close();
        connection = null;
      }
    }
  - Mas outro erro é gerado porque nao é possivel fechar a conexao
  - Criando uma variavel que guarda a funcao original close()
    
    `var close = null`;
    return class ConnectionFactory {
      static getConnection() {
        ...
        o.onsuccess = e => {
          ...
          `close = connection.close.bind(connection)`;
          connection.close = function() { ... }
        }
      }
      static closeConnection() {
        if(connection) { 
          `close()`; // Usa o novo close pra fechar a conexao
          connection = null
        } 
      }
    }

5 - DAO Pattern (data access object)
------------------------------
  - Chamada da factory:
    ConnectionFactory
      .getConnection()
      .then(connection => {

      `trabalhando com a DAO`
      let dao = `new NegociacaoDao( connection )`;
      let negociacao = new Negociacao(new Date(), 1, 100);
      `dao.adiciona( negociacao ).catch((erro)=>{ ... })`;

    });
  - Criando a negociacaoDao:
    class negociacaoDao {

      constructor(connection){
        this._connection = connection;
        this._store = 'necociacoes';
      }

      adiciona( negociacao ) {
        
        return new Promise((resolve, reject)=>{
          
          // IndexedDB 
          let request = this._connection
            .transaction([this._store], 'readwrite')
            .objectStore(this._store)
            .add( negociacao );
        
          request.onsuccess = e => {
            resolve();
          }

          request.onerror = e => {
            console.log('Erro no cadastro da negociacao.');
            reject(e.target.error);
          }
        })
      }
    }
  - `Dexie` e o `Db.js` sao bibliotecas criadas para lidar com o IndexedDB

5 - Array - Some
------------------------------
  - Dado: var a = {a:1, b:2};
  - var array = [{a:2, b:2},{a:3, b:2},`{a:1, b:2}`,{a:4, b:2},{a:5, b:2}]
  - Procurar por algum obj `a` dentro de `array`
    - `array.some((o)=> JSON.stringify(o) == JSON.stringify(a) );` // true

6 - Boa pratica e o metodo _init
------------------------------
  - Contrutor deve apenas iniciar os atributos
  - Se houver logica no construtor é melhor criar um metodo privado pra isso e chamar o metodo no contrutor. construtor(){ ... `this._init()`}
  - `_init() { logica removida do construtor }`

7 - Transportar a complexidade das acoes de listar, adicionar, etc para o Service
------------------------------
  - Ao inves de chamar o ConnectionFactory que devolve uma conexao, crair um DAO com essa conexao e depois realizar a acao, serai melhor escoder essa complexidade no NegociacaoService e ter algo do tipo:
  - NegociacaoService
      .cadastra(negociacao)
      .then(msg=>log(msg))
      .catch(msg=.log(msg));

8 - Trocando XMLHttpRequest po fetch API
------------------------------
  - class HttpService {
    get(url) {
      ... remove all code from here ...
    }
  }
  - New code:
  - get(url) {
      return `fetch(url)`
        .then( `res` => `this._handleErrors(res)` )
        .then( `res` => `res.json()` ) //Retorna Promise por padrao
    }
  - Para lidar com os erros da fech sera necessario conferir o `res.ok`
  - Criar um metodo privado na classe chamado _handleErrors(res)
  - _handleErrors(res) {
      if(!res.ok) 
        throw new Error('Houve um erro');
      return res;   
    }
  - Novo metodo post
  - post(url, dado) {
      return fetch(url, {
        headers: {'content-type': 'application/json'},
        method: 'post',
        body: JSON.stringfy(dado)
      })
      .then( `res` => `this._handleErrors(res)` )
    }
  - Ja existe um fetch.js polyfill caso navegador nao suporte a fetch API

9 - Transpiler - Babel
------------------------------
  - npm install babel-cli@6.10.1 --save-dev
  - npm install babel-preset-es2015@6.9.0 --save-dev
  - criar e configurar o arquivo .babelrc
  {
    "presets":["es2015"]
  }
  - Criar uma linha dentro do script do package JSON "build": "babel js/app-es6 -d js/app"
  - no terminal rodar `npm run build`
  - Gerando source-map para debug, adicionando o paramentro `--source-maps` dentro do `"build": "babel js/app-es6 -d js/app --source-maps"`
  - Para manter rodando o comando do babel, adicionar o `watch`
  - Criar um script `"watch": "babel js/app-es6 -d js/app --source-maps --watch"`
  - E para rodar basta `npm run watch`

10 - Trabalhando com modulos
------------------------------
  - ES6 Define export e import e que cada arquivo é um modulo
  - export class View { ... }
  - Dentro do MensagemView.js `import { View } from './View'`
  - export class MensagemView() { ... }
  - E assim por diante ...

11 - Carregamento dos modulos
------------------------------
  - Loader de scripts (SystemJS)
  - `npm install systemjs@0.19.31 --save`
  - importar o systemjs dentro do index.html
    - <script src="node_modules/systemjs/dist/system.js"></script>
  - indicando o primeiro modulo que vai ser carregado pelo systemjs
  - <script>
      System.defaultJSExtensions = true;
      System.import('js/app/boot').catch(function(err){
        console.error(err);
      });
    </script>
  - Criar o arquivo boot.js na pasta js/app
  - Esse arquivo deve importar os pollyfills e o controller principal
  - Instalar o plugin do Babel para usar o SystemJS `npm install babel-plugin-transform-es2015-modules-systemjs@6.9.0 --save-dev`
  - No .babelrc, adicionar o plugin instalado "plugins":["transform-es2015-modules-systemjs"]
  - rodar `npm run build`

12 - Ordenando os dados da tabela sem o controller no escopo global
------------------------------
  - No Controller, ao inves de exportar a classe, criar uma variavel que recebe a instancia da classe e export a instancia ao inves da classe;
  - No final do arquivo adicionar `let negociacaoController = new NegociacaoController();`
  - Exportar a instancia:
  export function currentInstance() {
    return negociacaoController;
  }
  - No boot.js, ao inves de importar `NegociacaoController`, importar a `currentInstance`
  - import { `currentInstance` } from '../controllers/NegociacaoController'
  - no construtor da view que recebe o elemento, nós podemos caçar a tag clicada, no caso procurar por `th` e ordenar a lista atraves do `currentInstance.ordena(element.target.textcontent.toLowercase())`

