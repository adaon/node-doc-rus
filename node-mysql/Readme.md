# node-mysql

[![Build Status](https://secure.travis-ci.org/felixge/node-mysql.png)](http://travis-ci.org/felixge/node-mysql)

## Установка

```bash
npm install mysql@2.0.0-alpha7
```

Несмотря на тег `alpha`, это рекомендуемая версия для новых приложений.
За информацией о предыдущих релизах 0.9.x, обращайтесь на [v0.9 branch][].

Иногда я, возможно, буду просить вас установить последнюю версию из Github для проверки исправления ошибок. В этом случае, сделайте следующее:

```
npm install git://github.com/felixge/node-mysql.git
```

[v0.9 branch]: https://github.com/felixge/node-mysql/tree/v0.9

## Введение

Это node.js драйвер для mysql. Он написан на javaScript, не требует компилляции и лицензирован MIT на 100%.

Вот пример использования:

```js
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'localhost',
  user     : 'me',
  password : 'secret',
});

connection.connect();

connection.query('SELECT 1 + 1 AS solution', function(err, rows, fields) {
  if (err) throw err;

  console.log('The solution is: ', rows[0].solution);
});

connection.end();
```

Из этого примера можно узнать следующее:

* Каждый метод, вызываемый соединением, добавляется в очередь и выполняется последовательно.
* Закрытие соединения выполняется с помощью `end()`, которая гарантирует, что все оставшиеся запросы будут выполнены перед завершением соединения.

## Разработчики

Спасибо людям, которые вносили код в этот модуль, см
[GitHub Contributors page][].

[GitHub Contributors page]: https://github.com/felixge/node-mysql/graphs/contributors

Дополнительно я бы хотел поблагодарить следующих людей:

* [Andrey Hristov][] (Oracle) - за помощь в вопросах, связанных с протоколами.
* [Ulf Wendel][] (Oracle) - за помощь в вопросах, связанных с протоколами.

[Ulf Wendel]: http://blog.ulf-wendel.de/
[Andrey Hristov]: http://andrey.hristov.com/

## Спонсоры

Следующие компании финансово спонсировали данный проект, позволяя мне тратить на него больше времени (отсортировано по времени взноса):

* [Transloadit](http://transloadit.com) (мой стартап, мы предлагаем хранение и кодирование видео как услугу, ознакомьтесь)
* [Joyent](http://www.joyent.com/)
* [pinkbike.com](http://pinkbike.com/)
* [Holiday Extras](http://www.holidayextras.co.uk/)
* [Newscope](http://newscope.com/)

Если вы заинтересованы в спонсировании моего дня или больше, пожалуйста
[свяжитесь][].

[get in touch]: http://felixge.de/consulting

## Сообщество

Если вы хотите обсудить данный модуль или задать вопросы о нем, используйте следующие ресурсы:

* **Список рассылки**: https://groups.google.com/forum/#!forum/node-mysql
* **Канал IRC**: #node.js (on freenode.net, I pay attention to any message
  including the term `mysql`)

## Открытие соединений

Рекомендуемый способ открытия соединения:

```js
var mysql      = require('mysql');
var connection = mysql.createConnection({
  host     : 'example.org',
  user     : 'bob',
  password : 'secret',
});

connection.connect(function(err) {
  // connected! (unless `err` is set)
});
```

Однако, соединение также может автоматически открываться при выполнении запроса:

```js
var mysql      = require('mysql');
var connection = mysql.createConnection(...);

connection.query('SELECT 1', function(err, rows) {
  // connected! (unless `err` is set)
});
```

В засисимости от способа, которым вы обрабатываете ошибки, один из методов может быть более подходящим. Все типы ошибок соединения (установка соединения или сеть) рассматриваются как фатальные, смотрите [Обработка ошибок](#error-handling) для более детальной информации.

## Параметры соединения

При установке соединения, вы можете установить следующие параметры:

* `host`: Имя хоста базы данных, с которой вы соединяетесь. (По умолчанию:
  `localhost`)
* `port`: Номер порта для соединения. (По умолчанию: `3306`)
* `socketPath`: Путь к доменному сокету UNIX. Если используется `host`
  и `port` игнорируются.
* `user`: MySQL-пользователь для аутентификации.
* `password`: Пароль для этого MySQL-пользователя.
* `database`: Имя базы данных, используемое при соединении (Опционально).
* `charset`: Кодировка для соединения. (По умолчанию: `'UTF8_GENERAL_CI'`)
* `timezone`: Временная зона, используемая для хранения дат. (По умолчанию: `'local'`)
* `insecureAuth`: Разрешает соединение с MySQL-серверами, которые используют старый (небезопасный) метод аутентификации. (По умолчанию: `false`)
* `typeCast`: Определяет, должны ли значения полей конвертироваться в JavaScript-типы. (по умолчанию: `true`)
* `queryFormat`: Пользовательская функция форматирования запроса. Смотрите [Пользовательское форматирование](#custom-format).
* `supportBigNumbers`: При работе с большими числами в базе данных, вы должны установить данный параметр.
* `debug`: Выводит детали протокола в стандартный поток вывода. (По умолчанию: `false`)
* `multipleStatements`: Разрешает выполнение нескольких MySQL-выражений в запросе. Будьте осторожны, это создает уязвимости для SQL-инъекций. (По умолчанию: `false`)
* `flags`: Список флагов соединения, которые будут использоваться вместо установленных по умолчанию. Также можно внести флаги по умолчанию в черный список. Для подробной информации, см. [Флаги соединения](#connection-flags).

В дополнение к передаче этих параметров в виде объекта, вы также можете использовать строку URL. Например:

```js
var connection = mysql.createConnection('mysql://user:pass@host/db?debug=true&charset=BIG5_CHINESE_CI&timezone=-0700');
```

Примечание: Значения в строке запроса сначала преобразовываются как JSON, и если преобразование завершается неудачей, используются как простой текст.

## Завершение соединений

Есть два пути закрыть соединение. Плавное завершение соединения выполняется вызовом метода `end()`:

```js
connection.end(function(err) {
  // Теперь соединение завершено
});
```

Данный способ гарантирует, что все выполняемые запросы завершатся перед отправкой пакета `COM_QUIT` MySQL-серверу. Если перед отправкой пакета `COM_QUIT` возникает фатальная ошибка, аргумент `err` будет передается функции обратного вызова, но соединение все равно будет закрыто.

Другой путь завершить соединение заключается в вызове метода `destroy()`. Этот метод вызовет немедленное закрытие сокета соединения. В дополнение метод `destroy()` гарантирует, что больше не будет вызвано никаких событий и функций обратного вызова для данного соединения.

```js
connection.destroy();
```

В отличие от `end()`, метод `destroy()` не принимает аргумент функции обратного вызова.

## Объединение соединений

Соединения могут быть объединены для упрощения совместного использования соединения, или управления несколькими соединениями.

```js
var mysql = require('mysql');
var pool  = mysql.createPool({
  host     : 'example.org',
  user     : 'bob',
  password : 'secret'
});

pool.getConnection(function(err, connection) {
  // соединено! (если не задано `err`)
});
```
 
Когда работа с соединением завершена, просто вызовите `connection.end()` и соединение вернется в пул, готовое к повторному использованию.

```js
var mysql = require('mysql');
var pool  = mysql.createPool(...);

pool.getConnection(function(err, connection) {
  // Использование соединения
  connection.query( 'SELECT something FROM sometable', function(err, rows) {
    // Работа с соединением завершена.
    connection.end();

    // Не используйте соединение здесь, оно возвращено в пул.
  });
});
```

Если вы захотите закрыть соединение и удалить его из пула, используйте вместо этого
`connection.destroy()`. В следующий раз, когда понадобится соединение, пул создаст новое.

Connections are lazily created by the pool. Если вы создаете пул на 100 соединений, но только 5 используются одновременно, только 5 соединений будут созданы. Кроме того, соединения упорядочены в циклическом стиле, соединения берутся сверху стопки и возвращаются в низ.

## Параметры пула

Пул принимает те же параметры, что и соединение. При создании нового соединения, параметры просто передаются конструктору соединения. В дополнение к параметрам соединения, пул принимает несколько своих опций:

* `createConnection`: Функция, используемая для создания соединения. (По умолчанию:
  `mysql.createConnection`)
* `waitForConnections`: Определяет действия пула, когда соединения закончились и достигнут лимит. Если `true`, пул поставит запрос в очередь и выполнит его, когда будет доступно соединение. Если `false`, пул немедленно вызовет функцию обратного вызова с ошибкой. (По умолчанию: `true`)
* `connectionLimit`: Максимальное число одновременных соединений.
  (По умолчанию: `10`)

## Переключение пользователей / изменение состояния соединения

MySQL предоставляет команду changeUser, которая позволяет изменить текущего пользователя и другие свойства соединения без закрытия сокета:

```js
connection.changeUser({user : 'john'}, function(err) {
  if (err) throw err;
});
```

Доступные опции для данного метода:

* `user`: Имя нового пользователя (по умолчанию равно предыдущему).
* `password`: Пароль нового пользователя (по умолчанию равно предыдущему).
* `charset`: Новая кодировка (по умолчанию равна предыдушей).
* `database`: Новая база данных (по умолчанию равна предыдущей).

Иногда бывает полезен побочный эффект данной операции: эта функция также сбрасывает любое состояние соединения (переменные, транзакции и т.д.).

Ошибки, возникающие в процессе данной операции, рассматриваются этим модулем как фатальные ошибки соединения.

## Потеря соединения с сервером

Иногда соединение с MySQL-сервером может обрываться из-за проблем с сетью, истечением тайм-аута, или сбоем сервера. Все эти события рассматриваются как фатальные ошибки, и получают err.code = 'PROTOCOL_CONNECTION_LOST'`. См. раздел [Обработка ошибок](#error-handling) для подробной информации.

Наилучший способ обработки таких непредвиденных отсоединений показан ниже:

```js
function handleDisconnect(connection) {
  connection.on('error', function(err) {
    if (!err.fatal) {
      return;
    }

    if (err.code !== 'PROTOCOL_CONNECTION_LOST') {
      throw err;
    }

    console.log('Re-connecting lost connection: ' + err.stack);

    connection = mysql.createConnection(connection.config);
    handleDisconnect(connection);
    connection.connect();
  });
}

handleDisconnect(connection);
```

Как вы видите в примере выше, повторное соединение выполняется путем создания нового соединения. После закрытия, существующий объект соединения не может выполнить повторное соединение.

При использовании пула, отсоединенные соединения будут удаляться из пула, освобождая место для новых соединений, создаваемых при следующем вызове `getConnection`.

## Escaping query values

In order to avoid SQL Injection attacks, you should always escape any user
provided data before using it inside a SQL query. You can do so using the
`connection.escape()` method:

```js
var userId = 'some user provided value';
var sql    = 'SELECT * FROM users WHERE id = ' + connection.escape(userId);
connection.query(sql, function(err, results) {
  // ...
});
```

Alternatively, you can use `?` characters as placeholders for values you would
like to have escaped like this:

```js
connection.query('SELECT * FROM users WHERE id = ?', [userId], function(err, results) {
  // ...
});
```

This looks similar to prepared statements in MySQL, however it really just uses
the same `connection.escape()` method internally.

Different value types are escaped differently, here is how:

* Numbers are left untouched
* Booleans are converted to `true` / `false` strings
* Date objects are converted to `'YYYY-mm-dd HH:ii:ss'` strings
* Buffers are converted to hex strings, e.g. `X'0fa5'`
* Strings are safely escaped
* Arrays are turned into list, e.g. `['a', 'b']` turns into `'a', 'b'`
* Nested arrays are turned into grouped lists (for bulk inserts), e.g. `[['a',
  'b'], ['c', 'd']]` turns into `('a', 'b'), ('c', 'd')`
* Objects are turned into `key = 'val'` pairs. Nested objects are cast to
  strings.
* `undefined` / `null` are converted to `NULL`
* `NaN` / `Infinity` are left as-is. MySQL does not support these, and trying
  to insert them as values will trigger MySQL errors until they implement
  support.

If you paid attention, you may have noticed that this escaping allows you
to do neat things like this:

```js
var post  = {id: 1, title: 'Hello MySQL'};
var query = connection.query('INSERT INTO posts SET ?', post, function(err, result) {
  // Neat!
});
console.log(query.sql); // INSERT INTO posts SET `id` = 1, `title` = 'Hello MySQL'

```

If you feel the need to escape queries by yourself, you can also use the escaping
function directly:

```js
var query = "SELECT * FROM posts WHERE title=" + mysql.escape("Hello MySQL");

console.log(query); // SELECT * FROM posts WHERE title='Hello MySQL'
```

## Escaping query identifiers

If you can't trust an SQL identifier (database / table / column name) because it is
provided by a user, you should escape it with `mysql.escapeId(identifier)` like this:

```js
var sorter = 'date';
var query = 'SELECT * FROM posts ORDER BY ' + mysql.escapeId(sorter);

console.log(query); // SELECT * FROM posts ORDER BY `date`
```

It also supports adding qualified identifiers. It will escape both parts.

```js
var sorter = 'date';
var query = 'SELECT * FROM posts ORDER BY ' + mysql.escapeId('posts.' + sorter);

console.log(query); // SELECT * FROM posts ORDER BY `posts`.`date`
```

When you pass an Object to `.escape()` or `.query()`, `.escapeId()` is used to avoid SQL
injection in object keys.

### Custom format

If you prefer to have another type of query escape format, there's a connection configuration option you can use to define a custom format function. You can access the connection object if you want to use the built-in `.escape()` or any other connection function.

Here's an example of how to implement another format:

```js
connection.config.queryFormat = function (query, values) {
  if (!values) return query;
  return query.replace(/\:(\w+)/g, function (txt, key) {
    if (values.hasOwnProperty(key)) {
      return this.escape(values[key]);
    }
    return txt;
  }.bind(this));
};

connection.query("UPDATE posts SET title = :title", { title: "Hello MySQL" });
```

## Getting the id of an inserted row

If you are inserting a row into a table with an auto increment primary key, you
can retrieve the insert id like this:

```js
connection.query('INSERT INTO posts SET ?', {title: 'test'}, function(err, result) {
  if (err) throw err;

  console.log(result.insertId);
});
```

When dealing with big numbers (above JavaScript Number precision limit), you should
consider enabling `supportBigNumbers` option to be able to read the insert id as a
string, otherwise it will throw.

This option is also required when fetching big numbers from the database, otherwise
you will get values rounded to hundreds or thousands due to the precision limit.

## Executing queries in parallel

The MySQL protocol is sequential, this means that you need multiple connections
to execute queries in parallel. You can use a Pool to manage connections, one
simple approach is to create one connection per incoming http request.

## Streaming query rows

Sometimes you may want to select large quantities of rows and process each of
them as they are received. This can be done like this:

```js
var query = connection.query('SELECT * FROM posts');
query
  .on('error', function(err) {
    // Handle error, an 'end' event will be emitted after this as well
  })
  .on('fields', function(fields) {
    // the field packets for the rows to follow
  })
  .on('result', function(row) {
    // Pausing the connnection is useful if your processing involves I/O
    connection.pause();

    processRow(row, function() {
      connection.resume();
    });
  })
  .on('end', function() {
    // all rows have been received
  });
```

Please note a few things about the example above:

* Usually you will want to receive a certain amount of rows before starting to
  throttle the connection using `pause()`. This number will depend on the
  amount and size of your rows.
* `pause()` / `resume()` operate on the underlying socket and parser. You are
  guaranteed that no more `'result'` events will fire after calling `pause()`.
* You MUST NOT provide a callback to the `query()` method when streaming rows.
* The `'result'` event will fire for both rows as well as OK packets
  confirming the success of a INSERT/UPDATE query.

Additionally you may be interested to know that it is currently not possible to
stream individual row columns, they will always be buffered up entirely. If you
have a good use case for streaming large fields to and from MySQL, I'd love to
get your thoughts and contributions on this.

## Multiple statement queries

Support for multiple statements is disabled for security reasons (it allows for
SQL injection attacks if values are not properly escaped). To use this feature
you have to enable it for your connection:

```js
var connection = mysql.createConnection({multipleStatements: true});
```

Once enabled, you can execute multiple statement queries like any other query:

```js
connection.query('SELECT 1; SELECT 2', function(err, results) {
  if (err) throw err;

  // `results` is an array with one element for every statement in the query:
  console.log(results[0]); // [{1: 1}]
  console.log(results[1]); // [{2: 2}]
});
```

Additionally you can also stream the results of multiple statement queries:

```js
var query = connection.query('SELECT 1; SELECT 2');

query
  .on('fields', function(fields, index) {
    // the fields for the result rows that follow
  })
  .on('result', function(row, index) {
    // index refers to the statement this result belongs to (starts at 0)
  });
```

If one of the statements in your query causes an error, the resulting Error
object contains a `err.index` property which tells you which statement caused
it. MySQL will also stop executing any remaining statements when an error
occurs.

Please note that the interface for streaming multiple statement queries is
experimental and I am looking forward to feedback on it.

## Stored procedures

You can call stored procedures from your queries as with any other mysql driver.
If the stored procedure produces several result sets, they are exposed to you
the same way as the results for multiple statement queries.

## Joins with overlapping column names

When executing joins, you are likely to get result sets with overlapping column
names.

By default, node-mysql will overwrite colliding column names in the
order the columns are received from MySQL, causing some of the received values
to be unavailable.

However, you can also specify that you want your columns to be nested below
the table name like this:

```js
var options = {sql: '...', nestTables: true};
connection.query(options, function(err, results) {
  /* results will be an array like this now:
  [{
    table1: {
      fieldA: '...',
      fieldB: '...',
    },
    table2: {
      fieldA: '...',
      fieldB: '...',
    },
  }, ...]
  */
});
```

Or use a string separator to have your results merged.

```js
var options = {sql: '...', nestTables: '_'};
connection.query(options, function(err, results) {
  /* results will be an array like this now:
  [{
    table1_fieldA: '...',
    table1_fieldB: '...',
    table2_fieldA: '...',
    table2_fieldB: '...'
  }, ...]
  */
});
```

## Error handling

This module comes with a consistent approach to error handling that you should
review carefully in order to write solid applications.

All errors created by this module are instances of the JavaScript [Error][]
object. Additionally they come with two properties:

* `err.code`: Either a [MySQL server error][] (e.g.
  `'ER_ACCESS_DENIED_ERROR'`), a node.js error (e.g. `'ECONNREFUSED'`) or an
  internal error (e.g.  `'PROTOCOL_CONNECTION_LOST'`).
* `err.fatal`: Boolean, indicating if this error is terminal to the connection
  object.

[Error]: https://developer.mozilla.org/en/JavaScript/Reference/Global_Objects/Error
[MySQL server error]: http://dev.mysql.com/doc/refman/5.5/en/error-messages-server.html

Fatal errors are propagated to *all* pending callbacks. In the example below, a
fatal error is triggered by trying to connect to an invalid port. Therefore the
error object is propagated to both pending callbacks:

```js
var connection = require('mysql').createConnection({
  port: 84943, // WRONG PORT
});

connection.connect(function(err) {
  console.log(err.code); // 'ECONNREFUSED'
  console.log(err.fatal); // true
});

connection.query('SELECT 1', function(err) {
  console.log(err.code); // 'ECONNREFUSED'
  console.log(err.fatal); // true
});
```

Normal errors however are only delegated to the callback they belong to.  So in
the example below, only the first callback receives an error, the second query
works as expected:

```js
connection.query('USE name_of_db_that_does_not_exist', function(err, rows) {
  console.log(err.code); // 'ER_BAD_DB_ERROR'
});

connection.query('SELECT 1', function(err, rows) {
  console.log(err); // null
  console.log(rows.length); // 1
});
```

Last but not least: If a fatal errors occurs and there are no pending
callbacks, or a normal error occurs which has no callback belonging to it, the
error is emitted as an `'error'` event on the connection object. This is
demonstrated in the example below:

```js
connection.on('error', function(err) {
  console.log(err.code); // 'ER_BAD_DB_ERROR'
});

connection.query('USE name_of_db_that_does_not_exist');
```

Note: `'error'` are special in node. If they occur without an attached
listener, a stack trace is printed and your process is killed.

**tl;dr:** This module does not want you to deal with silent failures. You
should always provide callbacks to your method calls. If you want to ignore
this advice and suppress unhandled errors, you can do this:

```js
// I am Chuck Norris:
connection.on('error', function() {});
```

## Exception Safety

This module is exception safe. That means you can continue to use it, even if
one of your callback functions throws an error which you're catching using
'uncaughtException' or a domain.

## Type casting

For your convenience, this driver will cast mysql types into native JavaScript
types by default. The following mappings exist:

### Number

* TINYINT
* SMALLINT
* INT
* MEDIUMINT
* YEAR
* FLOAT
* DOUBLE

### Date

* TIMESTAMP
* DATE
* DATETIME

### Buffer

* TINYBLOB
* MEDIUMBLOB
* LONGBLOB
* BLOB
* BINARY
* VARBINARY
* BIT (last byte will be filled with 0 bits as necessary)

### String

* CHAR
* VARCHAR
* TINYTEXT
* MEDIUMTEXT
* LONGTEXT
* TEXT
* ENUM
* SET
* DECIMAL (may exceed float precision)
* BIGINT (may exceed float precision)
* TIME (could be mapped to Date, but what date would be set?)
* GEOMETRY (never used those, get in touch if you do)

It is not recommended (and may go away / change in the future) to disable type
casting, but you can currently do so on either the connection:

```js
var connection = require('mysql').createConnection({typeCast: false});
```

Or on the query level:

```js
var options = {sql: '...', typeCast: false};
var query = connection.query(options, function(err, results) {

}):
```

You can also pass a function and handle type casting yourself. You're given some
column information like database, table and name and also type and length. If you
just want to apply a custom type casting to a specific type you can do it and then
fallback to the default. Here's an example of converting `TINYINT(1)` to boolean:

```js
connection.query({
  sql: '...',
  typeCast: function (field, next) {
    if (field.type == 'TINY' && field.length == 1) {
      return (field.string() == '1'); // 1 = true, 0 = false
    }
    return next();
  }
})
```

If you need a buffer there's also a `.buffer()` function and also a `.geometry()` one
both used by the default type cast that you can use.

## Connection Flags

If, for any reason, you would like to change the default connection flags, you
can use the connection option `flags`. Pass a string with a comma separated list
of items to add to the default flags. If you don't want a default flag to be used
prepend the flag with a minus sign. To add a flag that is not in the default list, don't prepend it with a plus sign, just write the flag name (case insensitive).

**Please note that some available flags that are not default are still not supported
(e.g.: SSL, Compression). Use at your own risk.**

### Example

The next example blacklists FOUND_ROWS flag from default connection flags.

```js
var connection = mysql.createConnection("mysql://localhost/test?flags=-FOUND_ROWS")
```

### Default Flags

- LONG_PASSWORD
- FOUND_ROWS
- LONG_FLAG
- CONNECT_WITH_DB
- ODBC
- LOCAL_FILES
- IGNORE_SPACE
- PROTOCOL_41
- IGNORE_SIGPIPE
- TRANSACTIONS
- RESERVED
- SECURE_CONNECTION
- MULTI_RESULTS
- MULTI_STATEMENTS (used if `multipleStatements` option is activated)

### Other Available Flags

- NO_SCHEMA
- COMPRESS
- INTERACTIVE
- SSL
- PS_MULTI_RESULTS
- PLUGIN_AUTH
- SSL_VERIFY_SERVER_CERT
- REMEMBER_OPTIONS

## Debugging and reporting problems

If you are running into problems, one thing that may help is enabling the
`debug` mode for the connection:

```js
var connection = mysql.createConnection({debug: true});
```

This will print all incoming and outgoing packets on stdout.

If that does not help, feel free to open a GitHub issue. A good GitHub issue
will have:

* The minimal amount of code required to reproduce the problem (if possible)
* As much debugging output and information about your environment (mysql
  version, node version, os, etc.) as you can gather.

## Todo

* Prepared statements
* setTimeout() for Connection / Query
* Support for encodings other than UTF-8 / ASCII
* API support for transactions, similar to [php](http://www.php.net/manual/en/mysqli.quickstart.transactions.php)
