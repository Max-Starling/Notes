 ## MVC и его модификации

### MVC (1979)
В 1970-ых обязанности не разделялись: смешивание HTML, CSS и работы с базой данных считалось нормальной практикой. С ростом таких приложений становилось понятно, что они очень запутанны и их невозможно поддерживать (тратится слишком много ресурсов), поэтому нередко разросшиеся приложения приходилось переписывать с нуля.

В 1979 появился шаблон проектирования **MVC** (Model-View-Controller), попытавшийся разрешить проблему, продвигая идею **разделения ответственности** (separation of concerns, SoC) между бэкендом и фронтендом, UI и бизнес-логикой.

**Бизнес-логика** (business logic, domain logic) - часть приложения, задающая бизнес-правила, определяющие, как данные (состояние, state) предметной области (domain) приложения создаются, хранятся и изменяются.

MVC разделяет приложение на 3 концептуальные единицы
* **Модель** (Model) представляет бизнес-логику (данные и правила, методы работы с ними) приложения; не содержит информации, как отобразить данные.
* **Представление** (View) - UI-компонента (кнопка, поле для ввода и прочее); то, что пользователь видит и с чем взаимодействует.
* **Контроллер** (Controller) выступает координатором между View и Model: решает, какие Views показывать и с какими данными, переводит действия пользователя (например, клик по кнопке) в бизнес-логику.

Модель может быть представлена объектом или структурой объектов.

*Особенности MVC*   
1) View использует объекты данных напрямую из Model, чтобы эти данные отобразить.  
2) Когда данные в Model меняются, срабатывает событие, немедленно обновляющее View.  
3) Один View обычно привязан к одному Controller.  
4) Каждый экран может иметь несколько пар View-Controller.  
5) Controller может быть связан с несколькими Views. 

*Проблема MVC*  
Когда MVC прявился, HTTP ещё не было. Сейчас же при попытке описать полноценное приложение (HTTP-клиент + HTTP-сервер) понятно, что подлинный MVC работать не сможет: при обновлении данных в БД клиент не получает данные напрямую, а делает это по запросу к серверу (через Controller). Это можно решить при помощи веб-сокетов, но не всегда есть в этом необходимость

### Иерархический MVC (2000), PAC (1987)

**HMVC** (Hierarchical MVC) увеличивает модульность в контексте виджетизации UI-блоков.

Существует мнение, что авторы HMVC переосмыслили другой паттерн: **PAC** (Presentation-Abstraction-Control).

HMVC разбивает уроверь клиента (client tier) на иерархию из MVC-слоёв. Это называется **проектированием клиентского уровня** (client-tier architecture) и должно увеличивать масштабируемость приложения: каждый MVC-слой независим от других и может работать при отсутствии любого другого.

Controller, обрабатывающий основной запрос, пересылает подзапросы другим Controllers, чтобы получить рендеринг виджетов и включить их в рендеринг основного View.

### MVP (1996)

*MVC* был хорош для приложений своего времени, но приложения выросли и настало время изменений.

**MVP** (Model-View-Presenter) видоизменяет MVC, разделяя View и Model и осуществляя их коммуникацию только через **Presenter**.

*Presenter* также называют **Supervisor Controller**.

*Особенности MVP*  
1) View пассивен (passive) и ничего не знает о Model. 
2) Presenter не содержит бизнес-логики, он просто вызывает методы Model, а затем передаёт необходимые данные во View.  
3) Только один Presenter для каждого View.  
4) Изменение данных в Model не вызывает немедленное обновление View: событие всегда проходит через Presenter, что позволяет в нём перед обновлением View проделывать дополнительную логику, связанную с представлением.


### MVVM (2005)
  
Сложность приложений продолжала расти и снова появилась необходимость изменений.

**MVVM** (Model-View-ViewModel) призван разделить UI-дизайн и бизнес-логику таким образом, чтобы за них могли отвечать разные люди, использующие разные технологии для этих целей.

*Особенности MVVM*  
1) Один ViewModel соответствует только одному View и наоборот.
2) Вся логика из View перемещается во ViewModel, чтобы упростить View и позволить ему выполнять свою задачу (визуализация).
3) Отношение один к одному установлено между данными во View и данными во ViewModel.
4) Изменение данных во ViewModel вызывает немедленное обновление View.


## EBI (1992)

Шаблон проектирования **Entity-Boundary-Interactor** (EBI) был опубликован Иваром Якобсоном в одной из серии книг об объектно-ориентированной разработке ПО.

Изначально автор назвал архитектуру Entity-Interface-Control, но затем переименовал для понимания, что Interface не связан с одноимённой конструкцией в языках программирования, а Control не связан с Controller в MVC.

**Entity-объекты** содержат данные системы и всю логику, напрямую связанную с этими данными (такую логику, которая при внесении изменений в Entity тоже должна изменяться; меняется структура - меняются методы работы с ней).

**Boundary-объекты** содержат в себе весь функционал, касающийся интерфейса системы (системного окружения).
 
Любое взаимодействие с системой происходит через Boundary. Действующее лицо (actor) может быть не только человеком, но и другим устройсвом или сторонним API.

Антипаттерн: Entity содержит лишь данные, всё поведение выносится в Boundary. 

**Interactor-объекты** содержат поведение, не вошедшее в Boundary и Entity (обычно это операции над несколькими Entity, результат  которых возвращается в Boundary; похоже на сервисы).

*Interactors* важны, поскольку они содержат специфическую логику для конкретных *Boundaries*, в то время как *Entity* содержит общую (generic) логику для всех *Boundaries*. В случае, если вся логика будет храниться в *Entity*, некоторые *Boundaries* будут иметь доступ к тому, к чему не должны, что увеличивает сложность и может привести к ошибкам.

Обычные объектно-ориентированные методологии помещают все обязанности в одну сущность, не разделяя их между тремя описанными типами объектов, но автор книги считает, что такое разделение (инкапсуляция) обязанностей позволяет сделать систему более гибкой к изменениям, которые в этом случае становятся локальными: изменяется только один объект системы.

Так же, как в MVC Model представляет весь бэкенд (все сущности, сервисы и их связи), EBI представляет Boundary как одно целостное соединение с окружающим миром. Boundary представляет весь уровень представления, который соответствует Model и Controller в совокупности в MVC. Entities являются физическими сущностями, хранящими данные вместе со своим поведением. Interactors связывают уровень представления и Entities вместе.

MVC и EBI не заменяют, а дополняют друг друга.  
При их совместном использовании получился бы паттен View-Controller-Interactor-Entity.