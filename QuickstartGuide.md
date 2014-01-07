## Краткое руководство по TYPO3 на базе Fluid

Fed с TemplaVoilà (находящаяся в конце своего пути)? Это - краткое руководство по началу создания шаблонов страниц и элементов
содержимого, используя семейство расширений [FluidTYPO3](http://github.com/FluidTYPO3).

Можно писать код по мере чтения этого документа, либо скопировать его с [@cedricziel's](http://twitter.com/cedricziel)
[ft3_empty](http://github.com/FluidTYPO3/ft3_empty), это базовая структура и необходимые файлы.

## Шаг 1: Установка необходимых расширений

Нам понадобитятся: ``flux``, ``fluidpages``, ``fluidcontent`` и ``vhs``. Установите их как обычно через модуль управления
расширениями, установите во Flux debug mode (режим отладки) в ``1`` и отметьте rewriting of LLL files (перезапись LLL файлов),
если это нужно.

## Шаг 2: Создайте расширение поставщик (provider extension)

То есть маленькое расширение, содержащее все необходимые шаблоны, typoscript и ресурсы в одном пакете. Для этого придумайте
короткое название для расширения и создайте папку в ``typo3conf/ext/`` (проще и лучше использовать все буквы в нижнем регистре).
Мы очень находчивы и для примера взяли название ``quickstart``. Внутри этой папки мы создали еще несколько и добавили
необходимые файы:

```
typo3conf/ext/quickstart
__ Configuration
____ TypoScript
______ setup.txt
__ Resources
____ Private
______ Language
________ locallang.xml
______ Layouts
______ Partials
______ Templates
________ Content
________ Page
____ Public
__ ext_emconf.php
__ ext_icon.gif
__ ext_tables.php
```

``setup.txt`` содержит typoscript для настройки режимов (view) по умолчанию - пути к файлам шаблонов (как и в любых других
расширениях Extbase):

```
plugin.tx_quickstart.view {
    templateRootPath = EXT:quickstart/Resources/Private/Templates/
    partialRootPath = EXT:quickstart/Resources/Private/Partials/
    layoutRootPath = EXT:quickstart/Resources/Private/Layouts/
}
```

> Обратите внимание, что _ключ расширения_ имеет приставку ``tx_``, указан в нижнем регистре. Далее будут определены другие
> настройки, которые затем будут доступны внутри шаблонов.

``ext_tables.php`` включает небольшой typoscript и настраивает Flux, регистрируя наше расширение в качестве поставщика шаблонов
 для страниц и содержимого:

```php
t3lib_extMgm::addStaticFile($_EXTKEY, 'Configuration/TypoScript', 'Quickstart: a sample extension');

Tx_Flux_Core::registerProviderExtensionKey($_EXTKEY, 'Page');
Tx_Flux_Core::registerProviderExtensionKey($_EXTKEY, 'Content');
```

Можно скопировать ``ext_emconf.php`` из любого другого расширения (вроде [ft3_empty](http://github.com/FluidTYPO3/ft3_empty)) и
 изменить его под своё расширение, либо воспользоваться расширением builder для создания с нуля. Вот пример того,
 как он должен выглядеть:

```php
$EM_CONF[$_EXTKEY] = array(
    'title' => 'Quickstart',
    'description' => 'This extension provides templates and custom content elements',
    'category' => 'misc',
    'author' => 'John Doe',
    'author_email' => 'mail@some.tld',
    'author_company' => 'ACME',
    'shy' => '',
    'dependencies' => 'cms,extbase,fluidflux,fluidpages,fluidcontent,vhs',
    'conflicts' => '',
    'priority' => '',
    'module' => '',
    'state' => 'experimental',
    'internal' => '',
    'uploadfolder' => 1,
    'createDirs' => '',
    'modify_tables' => '',
    'clearCacheOnLoad' => 1,
    'lockType' => '',
    'version' => '0.1',
    'constraints' => array(
        'depends' => array(
            'typo3' => '4.5-6.1.99',
            'cms' => '',
            'extbase' => '',
            'fluid' => '',
            'flux' => '',
            'fluidpages' => '',
            'fluidcontent' => '',
            'vhs' => '',
        ),
        'conflicts' => array(
        ),
        'suggests' => array(
        ),
    ),
    'suggests' => array(
    ),
);
```

``ext_icon.gif`` это значок, размером 16x16 пикселей, который будет выводиться в модуле управления расширением,
его можно скопировать с этого, или любого другого подобного расширения.

``locallang.xml`` содержит основу XML структуры для переводимых меток, позже он будет автоматически заполняться из Flux:

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes"?>
<T3locallang>
  <meta type="array">
    <type>module</type>
    <description>Language labels for extension 'quickstart'</description>
  </meta>
  <data type="array">
    <languageKey index="default" type="array">
    </languageKey>
  </data>
 </T3locallang>
```

Теперь новое расширение проводник возможно найти в модуле управления расширениями: перейдите в него, установите расширение,
подключите статический typoscript из расширения в _корневой TS шаблон_ и удалите все другие объекты PAGE из корневого шаблона.

## Шаг 3: Создание шаблона страницы

Шаблон страницы состоит из двух частей: файлов макета (layout) и шаблона (template). _Макеты (Layouts)_ строят HTML 'скелет'
страницы (часть между тегами ``body``) и определяют динамические части при помощи разделов - 'sections'. А для внутреннего
интерфейса (backend) эти разделы (sections) и любые другие дополнительные поля определяются в шаблонах - _templates_. Чтобы
лучше понять это, воспользуйтесь приведенным ниже примером:

Файл макета (Layout) ``typo3conf/ext/quickstart/Resources/Private/Layouts/Foo.html``

```html
<f:layout name="Foo"/>

<div id="page" class="{settings.pageClass}">
    <div id="sidebar">
        <f:render section="Sidebar"/>
    </div>
    <div id="content">
        <f:render section="Content"/>
    </div>
</div>
```

Файл шаблона (Template) ``typo3conf/ext/quickstart/Resources/Private/Templates/Page/Foo.html``

```html
{namespace flux=Tx_Flux_ViewHelpers}
{namespace v=Tx_Vhs_ViewHelpers}

<div xmlns="http://www.w3.org/1999/xhtml" lang="en"
      xmlns:f="http://typo3.org/ns/fluid/ViewHelpers"
      xmlns:flux="http://fedext.net/ns/flux/ViewHelpers"
      xmlns:v="http://fedext.net/ns/vhs/ViewHelpers">

<f:layout name="Foo"/>

<f:section name="Configuration">

    <flux:flexform id="foo">

        <!-- Input field for Fluid variable 'pageClass' -->
        <flux:flexform.field.input name="settings.pageClass" default="some-css-class"/>

        <!-- Backend layout grid (TYPO3 6.x and greater only) -->
        <flux:flexform.grid>
            <flux:flexform.grid.row>
                <flux:flexform.grid.column colPos="0" name="Sidebar" style="width: 25%"/>
                <flux:flexform.grid.column colPos="1" name="Content" style="width: 75%"/>
            </flux:flexform.grid.row>
        </flux:flexform.grid>

    </flux:flexform>

</f:section>

<f:section name="Content">
    <!-- Render colPos=0 in this section -->
    <v:content.render column="0"/>
</f:section>

<f:section name="Sidebar">
    <!-- Render colPos=1 in this section -->
    <v:content.render column="1"/>
</f:section>

</div>
```

### Посмотрим, что мы здесь имеем

Мы создали шаблон страницы с названием _Foo_. Чтобы связать шаблон (template) с макетом (layout) они оба названы ``Foo.html`` и
 в обоих указано ``<f:layout name="Foo"/>``.

> Предназначение контейнеров div лишь
[включение автозавершения кода в любимой вами IDE]
(http://buzz.typo3.org/teams/extbase/article/howto-autocompletion-for-fluid-in-phpstorm)
и они не будут выводиться на сайте.

Макет (layout) содержит лишь простейшую структуру HTML с двумя областями для содержимого и внешним контейнером div с классом,
определяемым переменной Fluid ``{settings.pageClass}``. В названии переменной используется приставка ``settings.``,
что не требуется для настройки, но очень полезно, почему, станет понятно позже.

> По правилам, в макетах (layouts) имеется ``<f:section name="Main"/>``, именно она и выводиться в итоге на страницы сайта.

Шаблон (template) определяет представление макета (layout) посредством форм настроек - flexform и сетки внутреннего макета -
backend layout grid (доступны, начиная с TYPO3 6.x). Форма настроек (flexform) определяется через проекторы (viewhelpers)
``flux``, которые сильно упрощают их создание.

> Необходимый минимум для файла макета страницы (layout) - определить раздел (section) с названием _Configuration_,
содержащей форму настроек - flexform, с указанием, по крайней мере,  _id_, чтобы сделать возможным выбор её во внутреннем
интерфейсе (backend).

В нашем примере мы добавили поле ввода (input) для класса CSS, который затем становиться доступен в макете (layout) в виде
переменной Fluid с тем же названием:

```html
<flux:flexform id="foo">

    <!-- Input field for Fluid variable 'pageClass' -->
    <flux:flexform.field.input name="settings.pageClass" default="some-css-class"/>

    [...]

</flux:flexform>
```
и сетку (grid), котрая будет использоваться в макете внутреннего интерфейса (backend layout):

```html
<flux:flexform id="foo">

    [...]

    <!-- Backend layout grid (TYPO3 6.x and greater only) -->
    <flux:flexform.grid>
        <flux:flexform.grid.row>
            <flux:flexform.grid.column colPos="0" name="Sidebar" style="width: 25%"/>
            <flux:flexform.grid.column colPos="1" name="Content" style="width: 75%"/>
        </flux:flexform.grid.row>
    </flux:flexform.grid>

</flux:flexform>
```

Разделы _Content_ и _Sidebar_ используют проектор (viewhelper) ``vhs`` для вывода содержимого в эти столбцы (column).

> Все возможные проекторы (viewhelpers) и их аргументы можно найти в справочнике на страницах
[fedext.net](http://fedext.net/viewhelpers.html)

Теперь должно быть возможным выбрать макет страницы (layout) из внутреннего интерфейса,
отредактировав свойства страници и очистив все кеши. ``fluidpages`` имеет некоторые превосходные возможности наследования,
которые делают возможным выбор шаблона не только для текущей страницы, но и для ее потомков. Такая цепочка наследования
может быть разорвана на любом уровне.

### Что такое эти странные метки (labels)?

Если включить возможность перезаписи LLL (LLL rewrite) во ``flux``, то уникальные идентификаторы меток (label) автоматически
будут создаваться в ``typo3conf/ext/quickstart/Resources/Private/Language/locallang.xml``,
и в то же время они будут использоваться в качестве переводов по умолчанию.
По окончанию разработки своих шаблонов, можно пойти дальше, и заменить переводы по умолчанию на настоящие.
Не беспокойтесь, уже сделанные переводы не будут затронуты.

### Поговорим о ресурсах (assets)

Подключение к шаблону страницы глобальных CSS и JS настраиваются посредством typoscript в ``setup.txt``,
который уже был добавлен ранее. Как и во всех других расширениях на основе extbase, файлы ресурсов (asset) расположены в
``typo3conf/ext/quickstart/Resources/Public``, и могут быть упорядочены по подпапкам. Давайте добавим к нашему шаблону
``style.css`` и ``script.js``, добавив лишь несколько строк в указанный выше файл:

```
[...]
plugin.tx_vhs.settings.asset {
    styles {
        name = styles
        path = EXT:quickstart/Resources/Public/style.css
    }
    script {
        name = script
        path = EXT:quickstart/Resources/Public/script.js
    }
}
```

Как видно, ``plugin.tx_vhs.settings.asset`` - это массив файлов ресурсов, подключаемых к странице сайта. _Существует множество
параметров для их настройки, но нам они пока не нужны. О них поговорим позже_. Пока же нам нужны лишь наиболее важные параметры:
``name`` - название, и ``path`` - путь. Массив индексов не определяется никакими правилами,
но общеупотребимо использование для них name. Теперь, убедитесь, что эти файлы существуют,
очищенны кеши и взгляните на HTML код вашей страницы (с текущим шаблоном) - к ней должны быть подключены ресурсы - assets
(JS внизу станицы).

## Шаг 4: Свои элементы содержимого

Элементы содержимого определяются подобно шаблонам страниц, и также состоят из файлов макета (layout) и шаблона (template).
Обычно макет (layout) просто выводит раздел (section) Main шаблона (template), которая общая для нескольких элементов содержимого:

```html
<f:layout name="Content"/>

<f:render section="Main"/>
```

Все волшебство происходит в файле шаблона (template) элемента содержимого, основная структура которого похожа на следующую:

```html
{namespace flux=Tx_Flux_ViewHelpers}
{namespace v=Tx_Vhs_ViewHelpers}

<div xmlns="http://www.w3.org/1999/xhtml" lang="en"
      xmlns:f="http://typo3.org/ns/fluid/ViewHelpers"
      xmlns:flux="http://fedext.net/ns/flux/ViewHelpers"
      xmlns:v="http://fedext.net/ns/vhs/ViewHelpers">

<f:layout name="Content"/>

<f:section name="Configuration"/>

<f:section name="Preview"/>

<f:section name="Main"/>

</div>
```

Как уже говорилось, шаблон (template) с приведенным выше макетом (layout) соединяется через ``<f:layout name="Content"/>``,
добавим разделы (section) ``Configuration``, в котором будут наши формы настроек (flexform), ``Preview``,
отвечающий за вид во внутреннем интерфейсе и раздел ``Main`` где, как видно из макета (layout) будет выводиться содержимое на
страницы сайта.

Теперь сделаем типовой элемент содержимого: анонс, состящий из изображения, заголовка, некоторого текста и необязательной ссылки
``typo3conf/ext/quickstart/Resources/Private/Templates/Content/Teaser.html``:

```html
{namespace flux=Tx_Flux_ViewHelpers}
{namespace v=Tx_Vhs_ViewHelpers}

<div xmlns="http://www.w3.org/1999/xhtml" lang="en"
      xmlns:f="http://typo3.org/ns/fluid/ViewHelpers"
      xmlns:flux="http://fedext.net/ns/flux/ViewHelpers"
      xmlns:v="http://fedext.net/ns/vhs/ViewHelpers">

<f:layout name="Content"/>

<f:section name="Configuration">
    <flux:flexform id="teaser">
        <flux:flexform.field.file name="image" allowed="jpg" uploadFolder="uploads/tx_quickstart" minItems="1" maxItems="1"
        size="1"/>
        <flux:flexform.field.input name="headline"/>
        <flux:flexform.field.text name="teasertext" rows="5" cols="30" required="TRUE"/>
        <flux:flexform.field.input name="link">
            <flux:flexform.field.wizard.link activeTab="page"/>
        </flux:flexform.field.input>
    </flux:flexform>
</f:section>

<f:section name="Preview">
<table width="100%">
    <tr>
        <td width="50%"><v:media.image src="uploads/tx_quickstart/{image}" alt="{headline}" width="100"/></td>
        <td width="50%">
            <f:format.crop maxCharacters="50">{teasertext}</f:format.crop>
            <f:if condition="{link}">
                <strong>Link:</strong> {link}
            </f:if>
        </td>
    </tr>
</table>
</f:section>

<f:section name="Main">
<div class="quickstart-teaser">
    <article>
    <h1>{headline}</h1>
    <div class="image-wrapper">
        <v:media.image src="uploads/tx_quickstart/{image}" alt="{headline}" width="200"/>
    </div>
    <div class="text-wrapper">
        <f:format.nl2br>{teasertext}</f:format.nl2br>
        <f:if condition="{link}">
            <f:link.page pageUid="{link}" title="{headline}" class="readmore">read more</f:link.page>
        </f:if>
    </div>
    </article>
</div>
</f:section>

</div>
```

## Взглянем на три раздела (section):

``Configuration`` содержит форму настроек (flexform), определяющую поля элементов содержимого внутри проектора (viewhelper)
``<flux:flexform/>`` . ``id`` - обязательный элемент для этого проектора (viewhelper) и используется для формирования ключа
перевода для меток элемента (в этом случае flux.teaser) в _Мастере нового элемента содержимого (New Content Element
Wizard)_ TYPO3. По умолчанию создается новая вкладка с меткой _FCE_ - она может быть переназначена при помощи аргумента
``wizardTab``.

Ничего дополнительного о полях формы в этом примере сказать нельзя, ознакомьтесь со справочным руководством на
[fedext.net](http://fedext.net/viewhelpers/flux.html), где имеется вся необходимая информация о функциях и аргументах проекторов.
Внимательнее следует взглянуть на мастер ссылок (link wizard), добавляемого в качестве дочернего элемента к соответствующему
полю ввода (input).

> В разделах (section) ``Preview`` и ``Main`` доступ к значениям полей осуществляется посредством переменных с эквивалентными
названиями.

``Preview`` - это некий старомодный табличный макет для вывода во внутреннем интерфейсе, который может быть знаком по работе с
Templavoilà. Обратите внимание на использование ``<v:media.image />`` во избежание проблем с отображением изображений во
внутреннем интерфейсе, ввиду использования относительных путей.

``Main`` содержит все, выводимое на страницы сайта посредством использования проекторов (viewhelpers) fluid.

После очистки всех кешей, в _Мастере нового элемента содержимого (New Content Element Wizard)_ должна появиться новая вкладка
_FCE_, где возможно выбрать новые элементы содержимого. Как уже говорилось, функция LLL rewrite из Flux, при включении,
позаботиться о формировании переводимых метов в ``typo3conf/ext/quickstart/Resources/Private/Language/locallang.xml``.

Продолжение следует...
