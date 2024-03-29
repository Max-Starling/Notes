- [HTTP](#http)
  - [О протоколе](#о-протоколе)
  - [Связь между URI, URL и URN](#связь-между-uri-url-и-urn)
  - [Формат сообщений](#формат-сообщений)
  - [HTTP-методы](#http-методы)
  - [Идемпотентность и безопасность HTTP-методов](#идемпотентность-и-безопасность-http-методов)
  - [Разница между HTTP и HTTP/2](#разница-между-http-и-http2)
  - [REST](#rest)

# HTTP

## О протоколе
**HyperText Transfer Protocol** (HTTP, протокол передачи гипертекста) — *протокол прикладного уровня передачи данных*.
В *основу* данного протокола входит технология **Клиент-Сервер**.  
*Клиенты* и *серверы обмениваются* *сообщениями* по схеме **запрос-ответ** (request–response): *клиент отправляет запрос*, *сервер возвращает ответ*.

*HTTP* является *общим языком* и *набором правил* для *клиента* и *сервера*.

*HTTP* определяет
* *синтаксис* (формат данных и кодирование),
* *семантику* (смысловое значение, связанное с синтаксисом),
* *время* (скорость и последовательность).  

*Каждый HTTP-запрос и ответ* считается отдельной **HTTP-транзакцией**.

*HTTP* основан на **текстовых данных** — **сообщениях**, состоящих из *битов текста*.  
*Каждое сообщение* состоит из *заголовка* (header) и *тела* (body).

*HTTP* — протокол *прикладного* уровня: *уровень абстракции*, стандартизирующий взаимодействие хостов.  
Сам *HTTP не передаёт данные*, а обращается за помощью к протоколу TCP / IP, чтобы получить запрос и ответ.  

Основным объектом манипуляции в HTTP является ресурс, на который указывает URI в запросе клиента.  

Одной из важных особенностей HTTP является то, что есть возможность задавать параметры в запросах и ответах: формат, кодировку и прочее.  
Несмотря на то, что HTTP является текстовым протоколом, он может обмениваться двоичными данными благодаря возможности кодировки.

Для идентификации ресурсов HTTP использует глобальные URI.  

*HTTP не хранит своё состояние* в отличие от многих других протоколов.  
Это означает, что сохранение промежуточного состояния между парами запрос-ответ отсутствует и HTTP не осведомлён о предыдущих запросах и ответах.  

Клиент, сервер и браузер могут самостоятельно хранить состояние, опираясь на данные последних запросов и ответов.  
Например, на стороне клиента могут храниться токены или куки.  
Сервер может хранить IP-адреса клиентов.  
Браузер может отслеживать задержки ответов.  

Обычные *HTTP-запрос* и *HTTP-ответ* *не зашифрованы и уязвимы* для различных типов атак.  
Эту проблему решает HTTPS (HTTP Secure) — безопастная версия протокола, использующая протол TLS или SSL для шифрования.

**SSL** - это **протокол безопасности**, который позволяет клиенту и серверу безопасно  
обмениваться данными по сети, предотвращая подслушивание и фальсификацию, в то  
время как сообщение передается по сети.

Другие протоколы прикладного уровня: FTP, SMTP.

## Связь между URI, URL и URN

**Uniform Resource Identifier** (URI) — символьная строка, позволяющая идентифицировать какой-либо ресурс: документ, изображение, электронную почту и прочее.   
Прежде всего, речь идёт о ресурсах сети Интернет и Всемирной паутины.    
URI предоставляет простой и расширяемый способ идентификации ресурсов.  

**Uniform Resource Locator** (URL) — URI, предоставляющий информацию о местонахождении ресурса.  

**Uniform Resource Name** (URN) — URI, который только идентифицирует ресурс в определённом пространстве имён (в определённом контексте), но не указывает его местонахождение.  
Пример: urn:ISBN:0-395-36341-1 — URI, указывающий на ресурс (книгу) 0-395-36341-1 в пространстве имён ISBN.

Поскольку URI не всегда указывает на то, как получить ресурс, в отличие от URL, а только идентифицирует его, это даёт возможность описывать с помощью RDF (Resource Description Framework) ресурсы, которые не могут быть получены через Интернет (например, личность, автомобиль, город и проч.).

## Формат сообщений

Как отмечалось ранее, клиент и сервер общаются сообщениями. 

Каждое сообщение содержит заголовки с метаданными и иногда тело с данными.

### Заголовки сообщения

**HTTP-заголовки** (HTTP Headers) обычно содержат **метаданные** (metadata), то есть *данные о данных*.  

```http
GET /url 
Header-Name: header-value
Another-Header-Name: header-value
```

*Метаданные* включают в себя:
- Тип запроса (request type): `GET`, `POST`, `PUT`, `DELETE` и другие.
- Путь запроса (URL).
- Код статуса запроса (status code): `100` - `5xx`.
- Тип контента в запросе (content-type).
- Данные с клиента (User-agent): браузер, ОС и прочее.
- Куки (Cookie): текущая сессия.

### Тело сообщения

**Тело сообщения** (Message body) содержит *данные*. 

Оно отделяется от заголовков пустой строкой.
```http
POST /url 
Content-Type: application/json

Body data
```
В зависимости от HTTP-метода тело может отсутствовать.

## HTTP-методы

**HTTP-методы**, **HTTP-глаголы** (verbs) сообщают серверу, что требуется сделать с данными, хранящимися по указанному URL.  


Когда *клиент делает запрос*, он *указывает тип запроса*, используя один из следующим методов:
- [GET](#get)
- [HEAD](#head)
- [POST](#post)
- [PUT](#put)
- [DELETE](#delete) 
- [OPTIONS](#options)
- [PATCH](#patch)

### GET

**Метод GET** используется для *чтения информации с сервера по данному URL*.

*Метод GET* доступен *только для чтения*. По запросу `GET` данные на сервере не должны изменяться, сервер должен отправлять данные без изменений. 
```http
GET /user/friends
```

*Метод GET* не имеет тела запроса* (Request Body). Все данные запроса GET содержатся в URL и видны всем, поэтому не стоит передавать секретную информацию в методе GET. 

*Статус-коды метода GET*
* **200** (OK) — *запрашиваемый ресурс найден*.
* **404** (Not found) — *запрашиваемый ресурс не найден*.

### HEAD

**Метод HEAD** идентичен *методу GET*, поскольку запрашивает у сервера такой же ответ, но без тела ответа (Response Body). 
```http
HEAD /user/friends
```
При помощи *метода HEAD* можно получить мета-данные, хранящиеся в заголовке ответа (Reponse Headers), и при этом напрасно не передавать данные с сервера.

Как и *метод GET*, *метод HEAD* является *безопасным* и *идемпотентным*.

### POST

**Метод POST** обычно используется для *создания нового ресурса*.  
```http
POST /users/create

username=max&sex=male
```

При поздании нового ресурса подразумевается создание нового подчинённого ресурса (subordinate) для ресурса, заданного по URL. Например, в случае примера выше это создание нового пользователя для ресурса `users`, содержащего информацию о пользователях.

*Статус-коды GET-запроса*
* **201** (Created) — *подчинённый ресурс создан успешно для указанного ресурса*.
* **404** (Not found) — *запрашиваемый ресурс не найден*.

### PUT 

**Метод PUT** используется для обновления ресурса, идентифицируемого URL-адресом, с использованием информации в теле запроса. 
```http
PUT /users/17

username=dana&sex=female
```

PUT также может быть использован для создания нового ресурса.  

PUT запросы отвечают статусом 200 если запрос успешно обновлён и 404 если ресурс не найден.

### DELETE

**Метод DELETE** используется для удаления ресурса, указанного в URL.  

На запросы DELETE в ответ удаляется код состояния 200 (ОК), если он был успешно  
удален, или 404 (НЕ НАЙДЕН), если удаляемый ресурс не может быть найден.

https://medium.freecodecamp.org/how-the-web-works-part-iii-http-rest-e61bc50fa0a

### OPTIONS

## Идемпотентность и безопасность HTTP-методов

**Идемпотентность** (Idempotence) — свойство объекта (или операции) при повторном применении операции к объекту (или при повторном вызове операции) давать тот же результат, что и при первом. 

Таким образом, запросы GET считаются безопасными операциями, потому что вызов его один  
раз или вызов 20 раз будет иметь тот же эффект.

Кроме того, **запросы GET являются идемпотентными**.  
Это означает, что отправка нескольких запросов GET на один и тот же URL-адрес должна  
вызывать тот же эффект, что и один запрос GET, поскольку запрос GET просто запрашивает  
данные у сервера и фактически не изменяет какие-либо данные на сервере.

**POST не является ни безопасным, ни идемпотентным**. Это связано с тем, что выполнение  
двух или более одинаковых запросов POST может привести к созданию двух новых идентичных  
ресурсов.

Запросы PUT не считаются безопасными операциями, поскольку они изменяют состояние на сервере.  
Однако PUT идемпотентен, потому что несколько идентичных запросов PUT на обновление ресурса  
должны иметь тот же эффект, что и первый.

Запросы DELETE являются идемпотентными, потому что если вы удаляете ресурс,  
он удаляется, и даже если вы делаете несколько идентичных запросов DELETE,  
результат остается тем же: удаленный ресурс.

Скорее всего, вы просто получите сообщение об ошибке 404, если отправите запрос  
DELETE более одного раза для одного и того же ресурса, потому что сервер не сможет  
найти его после его удаления.

## Разница между HTTP и HTTP/2

<!--
https://stackoverflow.com/questions/58498116/why-is-it-said-that-http2-is-a-binary-protocol

Binary is probably a confusing term - everything is ultimately binary at some point in computers!

HTTP/2 has a highly structured format where HTTP messages are formatted into packets (called frames) and where each frame is assigned to a stream. HTTP/2 frames have a specific format, including a length which is declared at the beginning of each frame and various other fields in the frame header. In many ways it’s like a TCP packet. Reading an HTTP/2 frame can follow a defined process (the first 24 bits are the length of this packet, followed by 8 bits which define the frame type... etc.). After the frame header comes the payload (e.g. HTTP Headers, or the Body payload) and these will also be in a specific format that is known in advance. An HTTP/2 message can be sent in one or more frames.

By contrast HTTP/1.1 is an unstructured format made up of lines of text in ASCII encoding - so yes this is transmitted as binary ultimately, but it’s basically a stream of characters rather than being specifically broken into separate pieces/frames (other than lines). HTTP/1.1 messages (or at least the first HTTP Request/Response line and HTTP Headers) are parsed by reading in characters one at a time, until a new line character is reached. This is kind of messy as you don’t know in advance how long each line is so you must process it character by character. In HTTP/1.1 the HTTP Body’s length is handled slightly different as typically is known in advance as a content-length HTTP header will define this. An HTTP/1.1 message must be sent in its entirety as one continuous stream of data and the connection can not be used for anything else but transmitting that message until it is completed.

The advantage that HTTP/2 brings is that, by packaging messages into specific frames we can intermingle the messages: here’s a bit of request 1, here’s a bit of request 2, here’s some more of request 1... etc. In HTTP/1.1 this is not possible as the HTTP message is not wrapped into packets/frames tagged with an id as to which request this belongs to.

I’ve a diagram here and an animated version here that help conceptualise this better. -->

## REST

**REST** (Representational State Transfer) — архитектурный стиль для разработки  
приложений.

Его автор, Рой Филдинг, выступал за использование стандартных HTTP методов так,  
чтобы придать запросам определённый смысл. А также за то, что между кодами ответов
и самими ответами должен быть определённый смысл.

Основная идея заключается в том, что вы используете протокол «без сохранения  
состояния», «клиент-сервер», «кэшируемый» для выполнения вызовов между компьютерами  
- и чаще всего этот протокол является HTTP. 

Это просто причудливый способ сказать, что REST дает вам набор ограничений для  
разработки приложения. Эти ограничения помогают сделать систему более производительной,  
масштабируемой, прозрачной и надежной.

**Список ограничений**:
* **Uniform interface (единый интерфейс)**.
* **Stateless (отсутствие состояния)**. То есть все данные о состоянии, необходимые  
для обработки клиентского запроса, должны содержаться в самом запросе, и сервер должен  
отправлять все необходимые данные состояния обратно клиенту через сам ответ. 

Наличие такой системы без сохранения состояния делает приложения намного более  
масштабируемыми, потому что ни одному серверу не нужно беспокоиться о том, чтобы  
поддерживать одно и то же состояние сеанса в течение нескольких запросов.  
Все необходимое для получения данных о состоянии доступно в самом запросе и ответе.
