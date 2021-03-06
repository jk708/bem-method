## Представление блоков на файловой системе
БЭМ методология очень гибкая и позволяет вам настраивать её под свои процессы, под свои текущие технологии.

Нужен способ быстро определять, где может находиться код блока, если
  * проект живёт долго и постоянно обрастает новыми сущностями
  * а команда разработки состоит из нескольких специалистов, растёт и изменяется (появляются новые люди)

### Варианты именования файлов
#### Блоки в одном файле
У вас есть проект, в котором вы хотите применить БЭМ для HTML/CSS вёрстки и ни для чего более.

Используйте самую простую схему на файловой системе, когда реализация блоков лежит в файлах с разделением по технологиям: html, css, js.

```
myfacebook/
  myfacebook.css
  myfacebook.js
  myfacebook.html
```

Файлы `myfacebook.css` и `myfacebook.js` содержат весь CSS/JS код проекта и подключаются в HTML как есть.

Нет процесса сборки и оптимизации этих файлов. Это самый простой вариант, чтобы попробовать.

Фактически, БЭМ применяется только для именования CSS-классов.

#### Блоки в файлах
Когда весь CSS код блоков лежит в одном файле, то по этому файлу приходится всё время перемещаться.

Если проект большой, делать это не удобно.
Код блока легко искать, если он расположен в файлах, одноимённых ключевому слову блока.

```
blocks/
  head.css
  menu.css

all.css

index.html
about.html
```

Файл `all.css` может состоять из `@import` соответствующих файлов из директории `blocks`:

``` css
@import url(blocks/head.css);
@import url(blocks/menu.css);
…
```

#### Необязательное в отдельных файлах
Если в ваших блоках есть элементы/модификаторы, которые используются не на всех страницах,
можно вынести их реализацию в отдельные файлы, чтобы подключать только то, что нужно.

При этом можно использовать такую схему расположения на файловой системе:

  * Основной код блока размещён в файлах `имя-блока`
    * Имя файла совпадает с именем блока
    * Техническая реализация блока размещена в файлах с соответствующими расширениями
  * Элементу соответствует файл `имя-блока_~_имя-элемента`
    * Имя файла совпадает с именем элемента
    * Техническая реализация элемента располагается в файлах с соответствующими расширениями
  * Модификатору соответствует файл `имя-блока_имя-модификатора_значение`
    * Имя файла содержит как имя модификатора, так и его значение
    * Техническая реализация модификатора размещается в файлах в этой директории

Вы можете использовать другую схему отделения имени блока от имени элемента и модификатора.

**Пример**

```
blocks/
  head.css
  head__search.css
  head_size_big.css
  menu.css

about.css
about.html

index.css
index.html
```

Файл `index.css` содержит увеличенную в размере шапку, но без поискового поля:

```css
@import url(blocks/head.css);
@import url(blocks/head_size_big.css);
@import url(blocks/menu.css);
…
```

A в файле `about.css` шапка обычного размера и есть поле поиска:

```css
@import url(blocks/head.css);
@import url(blocks/head__search.css);
@import url(blocks/menu.css);
…
```

В этом случае можно начинать использовать [bem-tools](https://github.com/bem/bem-tools) для сборки проекта.

#### Каждый блок в своей директории
Может возникнуть задача использовать несколько блоков предыдущего проекта на новом проекте.

Чтобы легко делать это, реализацию блока (во всех технологиях) нужно уметь доставлять в
другое место. Это может быть сделано при помощи копирования файлов или частичным чекаутом
репозитория проекта-донора.

И в том, и в другом случае удобно, чтобы все файлы, относящиеся к блоку, были объединены
в одну директорию:

```
blocks/
  menu/
    menu.xsl
    menu.js
    menu.css
```

#### Элементы/модификаторы в отдельных директориях
В процессе работы над проектом может возникнуть необходимость изменить какой-нибудь блок.

Менеджер может попросить:
  *  Изменить цвет `активному пункту Меню`, или
  *  Сделать `Меню` реагирующим на наведение курсора

Один разработчик может попросить другого:
  *  Помочь с вёрсткой `Поисковой формы` под IE

Чтобы можно было сразу понять, где лежит код, о котором идёт речь, можно соблюдать в
разработке следующие (или похожие) требования:

  * Код блока размещён в отдельной директории
    * Имя директории совпадает с именем блока
    * Техническая реализация блока размещена в файлах в директории блока
  * Элементу соответствует поддиректория в директории блока
    * Имя директории совпадает с именем элемента
    * Техническая реализация элемента располагается в файлах в этой директории
  * Модификатору соответствует поддиректория в директории блока
   * Имя директории соответствует имени модификатора
   * Техническая реализация модификатора размещается в файлах в этой директории
   * Названия файлов содержат как имя, так и значение модификатора

Этот вариант очень наглядный, при взгляде на файловую систему можно увидеть всю структуру блока.

**Пример**  
Файловая структура блока Меню

```
menu/
  __item/
    _state/
      menu__item_state_current.css
      menu__item_state_current.xsl
    menu__item.css
    menu__item.xsl
  menu.css
  menu.js
  menu.xsl
```

Поддержка такой файловой структуры в ручном режиме, очевидно, неудобна. Чтобы с этим справиться мы
сделали [BEM tools](https://github.com/bem/bem-tools). Этот пакет инструментов помогает создавать
и поддерживать файловую структуру, создавать файлы по шаблонам и т.д.

#### Группировка блоков в папки
На большом портале необходимо реализовывать одни и те же блоки на разных проектах.

Может возникнуть задача:
  * Реализовать одинаковый Подвал `на всех проектах Портала`, или
  * Сделать `новый проект`, используя блоки уже существующего

Разработка сайтов один за другим в web-студии часто подразумевает использование типовых решений для типовых страниц.

Менеджер может попросить:
  * Сделать Страницу заказа товара с формой `как на позапрошлом проекте`

Выполнять эти задачи нужно без копирования блоков из проекта в проект. Должна быть возможность
подключить блоки одного проекта к другому. Для этого блоки нужно объединять в одну директорию.

Мы называем такую директорию `blocks`.

**Пример**

```
blocks/
  foot/
  head/
  menu/
  page/
  search/
```

Такую папку можно подключить к новому проекту прямо из репозитория.

В этом случае код блока будет храниться только в одном месте. Если нужно исправить ошибку или
внести изменения, это нужно будет сделать только там.

### Уровни переопределения
Если группа блоков одного проекта (объединённая в папку) подключена к другому проекту напрямую
(при помощи частичного чекаута или svn:externals), то любое закоммиченное изменение в таких блоках
повлияет на оба проекта.

При разработке одного сайта на основе существующего может потребоваться:
  * Увеличить шрифт в Шапке на всём сайте (не затрагивая предыдущий сайт),
  * Добавить анимацию для раскрытия всех выпадающих меню.

То есть необходима возможность переопределять (или доопределять) блоки в разных технологиях только
для определённого сайта или только для определённых страниц. Это возможно благодаря `уровням переопределения`.

`Уровень переопределения` — это набор реализаций блоков, сгруппированных в одну директорию.

![Набор реализаций блоков](http://img-fotki.yandex.ru/get/9107/221798411.0/0_babc3_6eb2cfbf_XXL.png)

Реализация любого блока библиотеки может быть расширена (или переопределена) на уровне проекта.

![Переопределение на уровне проекта](http://img-fotki.yandex.ru/get/9515/221798411.0/0_babc2_3e8566e4_XXL.png)

С точки зрения сборки страниц:
  * При сборке каждой страницы можно указать список уровней (директорий), блоки которых будут
    представлены на странице.  
    Например, `build-page -l blocks-common -l blocks-my my-page.html`

С точки зрения файловой структуры:
  * Проект может содержать любое количество реализаций блоков. Но на страницу попадут только те,
    которые явно указаны к применению для сборки этой страницы. Возможно делать разные наборы
    уровней переопределения для разных разделов сайтов.

С точки зрения JavaScript
  * Необходима возможность декларативного описания динамического поведения элементов на странице.
    Конечное поведение должно собираться с разных уровней переопределения. Например,

```js
/* blocks-common/dropdown/dropdown.js */
Block('dropdown', {
  init: function() {
    …
  }
});

/* blocks-my/dropdown/dropdown.js */
Block('dropdown', {
  init: function() {
    this.__base();
    …
  }
});
```

С точки зрения шаблонного движка
  * Для того, чтобы можно было не только переопределять, но и "доопределять" шаблон, нужна
    возможность вызвать предыдущую реализацию шаблона.  
    Например, в случае xsl:

```xml
<xsl:template match="b:head">
  <div> <!-- Node for extra design -->
    <xsl:apply-imports/>
  </div>
</xsl:template>
```

С точки зрения архитектуры проекта
  * При разработке портала из нескольких сайтов можно выделить общую библиотеку блоков, которая станет
    одним из уровней переопределения для всех сайтов портала. Блоки конкретного сайта — объединить в
    другой уровень переопределения.
  * В одном и том же репозитории может храниться реализация сайта для десктопных и мобильных браузеров.  
    В таком проекте есть уровень переопределения для общей реализации блоков, для мобильной и для десктопной.
    Различные комбинации уровней дают желаемую реализацию блоков на конкретных страницах.

Примером реализации нескольких уровней переопределения в одном репозитории может служить
[Open Source библиотека блоков bem-bl](https://github.com/bem/bem-bl).

### Сборка страниц
Работа со страницей в терминах блоков подразумевает `предметную абстракцию`. Эта предметная абстракция
обязательна только на уровне взаимодействия человека с кодом.

Когда код попадает в браузер, наличие предметной абстракции не обязательно, важно лишь сохранить требуемый
внешний вид и поведение блоков на странице.

Таким образом, `код для людей` и `код для браузеров` — не одно и то же:
  * Человек пишет код блоков, браузер получает код целой страницы

Для того, чтобы превратить «код для людей» в «код для браузеров» мы используем `сборку страниц`.

`Сборка страницы` — это формирование кода страницы в различных технологиях
(HTML, CSS, JavaScript) на основе декларативного описания страницы (XML, JSON)
путём применения реализаций описанных блоков.

С точки зрения CSS
  * Файлы всех блоков собираются в один файл страницы  
    Несмотря на то, что CSS для каждого блока, элемента и модификатора реализован в отдельном файле,
    для работы страницы нет необходимости подключать эти файлы as is. Всю необходимую CSS-реализацию
    страницы можно собрать в один файл. К тому же это решает проблемы: ограниченного количества импортов
    в IE, количества HTTP-запросов к серверу. Объединение всех CSS в один файл мы делаем при помощи
    утилиты [borschik](https://github.com/bem/borschik).
  * Браузер получает минимизированный код  
    В процессе сборки можно минимизировать и оптимизировать CSS-код. Например, при помощи утилиты
    [CSSO](https://github.com/css/csso)
  * Каждый браузер получает CSS, написанный специально для него  
    Есть возможность разделять CSS-реализации для разных браузеров и поставлять в каждый конкретный
    браузер только тот CSS, который нужен ему. Для этого можно использовать [Сеточку, прототип](https://github.com/afelix/setochka)

С точки зрения JavaScript
  * Много файлов для блоков, один файл для страницы  
  Так же, как и в случае CSS, JavaScript-реализация всех необходимых блоков может быть объединена в один файл.

С точки зрения шаблонных движков
  * Работают только нужные шаблоны  
    Конечный набор шаблонов, используемых для вывода HTML страницы в результате сборки содержит только
    шаблоны нужных блоков. Благодаря этому увеличивается производительность шаблонов и  уменьшается
    вероятность возникновения побочных эффектов.

С точки зрения процесса разработки
  * Роботы на службе у человека (не наоборот)  
    Разработчик пишет код так, как ему удобно. О производительности заботятся роботы, которые в процессе
    сборки превращают код в оптимизированный (и, возможно, нечитаемый).

С точки зрения организации труда
  * Разделение труда  
    Существуют одни разработчики, создающие блоки, и существуют другие разработчики, занимающиеся
    оптимизацией конечного продукта.

Для сборки файлов страниц используются [БЭМ-инструменты](https://github.com/bem/bem-tools).

#### Автоматизация сборки
Использование [БЭМ-инструментов](https://github.com/bem/bem-tools) подразумевает выполнение
нескольких операций для каждой страницы. В результате этих операций на основе bemjson описания
страницы генерируются CSS и JavaScript файлы страницы, шаблоны страницы и (в случае статической разработки)
- HTML страницы.

Чтобы не выполнять эти команды вручную, мы добавили в [bem-tools](https://github.com/bem/bem-tools)
команды `bem make` и `bem server`.

`bem make` — команда для статической сборки проекта. При запуске собирает все файлы, которые требуют пересборки.

`bem server` — команда для запуска HTTP сервера, который при обработке запросов запускает точечную сборку
файлов проекта и после успешного выполнения сборки отдаёт их клиенту.

Чтобы узнать больше, смотрите в [документацию](https://github.com/bem/bem-tools/blob/master/README.ru.md#bem-make).
