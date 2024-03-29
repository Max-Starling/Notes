## О линтерах

**Линтер** (англ. `linter`) в программировании — это программа (инструмент), которая анализирует код приложения с целью выявления потенциальных ошибок, проблем, уязвимостей, нарушений код стайла и так далее до реального выполнения кода.

Линтеры позволяют улучшить качество кода вашего приложения.

Настраивая линтер в своём проекте, вы сами задаёте для него правила с учётом уже встроенных рекомендаций, после чего каждый разработчик на проекте автоматически подстаивается под них.

Все мы разные, у каждого из нас свой опыт, своя точка зрения. Кому-то нравятся пробелы, кому-то - табы. Кто-то любит точки с запятой, закрывающие запятые, фигурные скобки, а кто-то топит за минимализм символов. Линтер позволяет установить соглашение о едином стиле между разработчиками.


### Как использовать линтер
Чаще всего линтеры встраивают в качестве расширения в IDE (например, [расширение Eslint в VSCode](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)), в этом случае ошибки будут подсвечиваться прямо у вас в коде.

Если же вы приверженец терминала, то для Eslint также предлагает CLI решение https://eslint.org/docs/latest/use/command-line-interface
```py
# запуск линтинга одного файла
eslint app.js
# запуск линтинга в папке
eslint src/*
```

### Алгоритм линтера

* Производится статический анализ кода (то есть код анализируется без реального выполнения).
* Проанализированный код парсится, затем строится абстракное синтаксическое дерево (англ. `abstract syntax tree`, `AST`).
* Для каждой вершины построенного дерева прогоняются все сконфигурированные на проекте правила линтера.
* Результат отображается на экране (например, в консоли или в IDE) 

### Забавная ассоциация для наилучшего запоминания

[Англ.](https://www.merriam-webster.com/dictionary/linters) `lint`, `linters` переводится как ворс - пушок, который удаляют с семян хлопка во время его обработки. 

[Англ.](https://www.merriam-webster.com/dictionary/linters) `linter` - это машина, которая удаляет ворсинки с хлопка во время его обработки.

То есть хлопковый линтер "причёсывает хлопок", избавляясь от ворсинок, в то время, как линтер в программировании "причёсывает" наш код, устраняя его неровности (недостатки).

## О форматтерах
**Форматтер** (англ. `formatter`) в программировании — это программа (инструмент), которая форматирует код. 

Так же, как и линтер, форматтер анализирует код и строит абстрактное синтаксическое дерево (англ. `AST`)

## Сравнение линтера и форматера 

Линтеры анализируют

Из гугла:
> Linting is distinct from Formatting because linting analyzes how the code runs and detects errors whereas formatting only restructures how code appears.

С сайта Prettier:
> Linters usually contain not only code quality rules, but also stylistic rules. Most stylistic rules are unnecessary when using Prettier, but worse – they might conflict with Prettier! Use Prettier for code formatting concerns, and linters for code-quality concerns, as outlined in Prettier vs. Linters.

## Зачем нужны линтеры и форматеры

Линтеры и форматеры позволяют задать общий стиль всему проекту, а поэтому когда он уже настроен
* Не нужно тратить время и энергию на извечные споры в комментариях Pull Request-ам, комментариев в целом становится меньше, а значит Pull Requuest-ы мержатся быстрее и чаще. Более того, если постоянно не отвлекаться на такие мелочи как стиль кода при ревью, можно сфокусироваться на действительно важной составляющей кода - бизнес-логике и потенциальных ошибках.
* Новым людям намного проще влиться в проект и команду, когда правила явно заданы и хорошо описаны. Так они чувствуют себя увереннее и комфортнее, а большинство вопросов отпадают сами, что экономит время каждого члена проекта.
* Когда линтинг и форматинг происходят автоматически, можно тратить меньше времени на красивое оформление, а значит и писать код быстрее (и качествее, потому что все правила в голове не удержишь).

## Мои предпочтения в настройках линтера с пояснением
