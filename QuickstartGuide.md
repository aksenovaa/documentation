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

The layout contains some simple HTML structure with two content areas and the outer div container's CSS class is controlled
by a Fluid variable ``{settings.pageClass}``. The variable is prefixed ``settings.`` which is not required by configuration
but very useful. This will become clear at a later stage.

> Per convention layouts have to define a ``<f:section name="Main"/>`` which is the section that will finally get rendered.

The template defines the backend representation of this layout by providing a flexform and a backend layout grid (only
available in TYPO3 6.x and greater). This flexform is defined with ``flux`` viewhelpers which makes that part really
simple.

> The bare minimum for a page layout file is to define a section named _Configuration_ containing a flexform with at least
an _id_ to make it selectable in the backend.

In our example we add an input field for the CSS class which is then available in the layout as a Fluid variable of the
same name:

```html
<flux:flexform id="foo">

    <!-- Input field for Fluid variable 'pageClass' -->
    <flux:flexform.field.input name="settings.pageClass" default="some-css-class"/>

    [...]

</flux:flexform>
```
and a grid that will be used as the backend layout:

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

The sections _Content_ and _Sidebar_ make use of a ``vhs`` viewhelper to render the content of those columns.

> All available viewhelpers and their arguments can be looked up in the reference on
[fedext.net](http://fedext.net/viewhelpers.html)

You should now be able to select the page layout in the backend by editing a page's properties after clearing all caches.
``fluidpages`` includes some fine inheritance feature that enables you to select the page template not only for the current
page but also for its children and the chain of inheritance can be interrupted at any level.

### But those weird labels?

If you enabled the LLL rewrite feature in ``flux`` it will create unique label identifiers in
``typo3conf/ext/quickstart/Resources/Private/Language/locallang.xml`` and use those as default translations at the same time.
Once you are finished developing your templates you can go ahead and exchange the default translations with the real ones.
No worries, existing translations won't be touched.

### Excursion about assets

Inclusion of a page template's global CSS and JS is configured via typoscript in ``setup.txt`` that has been added earlier.
As in any other extbase extension asset files are located in ``typo3conf/ext/quickstart/Resources/Public`` and can of course
be organized in subfolders to your liking. Let's add ``style.css`` and ``script.js`` to our template by adding the following
lines:

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

As you can see ``plugin.tx_vhs.settings.asset`` is an array of asset files to be included into your page. _It provides a
lot more configuration options we don't need here yet but will be discussed at a later stage_. For now we'll stick to the
most important being ``name`` and ``path`` which should be self-explanatory. The array index doesn't follow any special
convention but it's common practice to use the name. Now, make sure those files exist, clear your caches and have a look
at the HTML source of your page to find the assets being included (JS at the bottom of the page).

## Шаг 4: Custom content elements

Content elements are defined similar to page templates and consist of a layout and a template file. Typically the layout
simply renders the main section of the template and is shared with several content elements:

```html
<f:layout name="Content"/>

<f:render section="Main"/>
```

All magic happens in the content element's template file which in its basic structure looks like this:

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

As you can see we connect the template to the above layout via ``<f:layout name="Content"/>``, add a section named
``Configuration`` that will contain our flexform, a Section named ``Preview`` that will get rendered in the backend and
the ``Main`` section as found in the layout that will contain the rendered frontend content.

Now, let's create a content element for a typical use case: a teaserbox consisting of an image, a headline, some teaser
text and an optional link in ``typo3conf/ext/quickstart/Resources/Private/Templates/Content/Teaser.html``:

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

## Let's have a look at the three sections:

``Configuration`` contains the flexform that defines the content element's fields which are wrapped in an outer
``<flux:flexform/>`` viewhelper. ``id`` is a required argument for this viewhelper and used to generate the translation key
for the element's label (flux.teaser in this case) in TYPO3's _New Content Element Wizard_. By default a new tab labelled
_FCE_ is created which can be overridden with the viewhelper argument ``wizardTab``.

There's nothing special about the flexform's fields in this example so to avoid duplicate content please take a look at the
reference guide on [fedext.net](http://fedext.net/viewhelpers/flux.html) where you can find all required information about
their functionality and arguments. One thing to take a closer look at though is the link wizard that is implemented by making
it a child of the according input field.

> The fields' values are accessible as equally named variables in sections ``Preview`` and ``Main``.

``Preview`` contains some real oldschool table layout to be rendered in the backend and which may look familiar if you worked
with Templavoilà before. Note the usage of ``<v:media.image />`` to avoid issues with displaying images in the backend due
to relative paths.

``Main`` finally contains the output to be rendered in the frontend and makes use of some fluid viewhelpers you certainly
know already.

After clearing all caches you should now see a new tab _FCE_ in the _New Content Element Wizard_ that makes our new content
element selectable. As described earlier Flux' LLL rewrite feature will - when enabled - take care of generating translation
keys in ``typo3conf/ext/quickstart/Resources/Private/Language/locallang.xml``.

To be continued...
