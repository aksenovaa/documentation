## Использование locallang.xlf в своих Flux/VHS расширениях поставщиках

### Напутствие

Чтобы подготовить свое расширение-проводник к использованию на различных языках, необходимо переместить все выводимые метки и
описания из файлов Template, Partials и Layout в файл locallang. После чего о загрузке локализуемых меток,
как для внутреннего интерфейса, так и страниц сайта позаботиться TYPO3.

Если в шаблонах страниц и содержимого не используются метки и описания, Flux просмотрит папку ``Resources/Private/Language``
и определит по ключам меток доступные метки/описания (или переводы). И если таковые присутствуют,
Flux добавит и выведет их во внутреннем интерфейсе для этих элементов. Поэтому нет необходимости заносить эти атрибуты в файлы
шаблонов.

### Замечание о языках по умолчанию (default)

**Начинать всегда следует с английского, а изменить язык никогда не поздно, настроив нужный в TYPO3.** Поступая таким образом,
становиться гораздо проще делиться своими примерами, получать помощь, избежать путаницы с названиями меток и, конечно,
сделать расширение подходящим для работы с программами перевода, вроде Pootle (смотрите http://translation.typo3.org). Помните,
официальный язык для TYPO3 - английский.

### Работа с locallang.xlf

Сначала взгляните на эту статью:

> http://docs.typo3.org/TYPO3/CoreApiReference/Internationalization/Introduction/Index.html

Если нужно работать с ``locallang.xlf``, необходимо создать его в следующей папке:
``typo3conf/ext/**YOUREXT**/Resources/Private/Language/``

Файл ``locallang.xlf`` используется для языка по умолчанию, и его содержимое должно быть на английском.

Для других языков просто создайте новые файлы с приставкой в виде их ISO кода, вроде:

- для русского ``ru.locallang.xlf``
- для немецкого ``de.locallang.xlf``
- для французского ``fr.locallang.xlf``
- ...

Список ISO кодов для языков можно посмотреть на странице http://www.loc.gov/standards/iso639-2/php/code_list.php

Во всех языковых файлах должна быть хотя бы минимальная структура:

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<xliff version="1.0">
        <file source-language="en" datatype="plaintext" original="messages" date="2012-10-17T17:55:17Z" product-name="**YOUREXT**">
                <header/>
                <body>
                </body>
        </file>
</xliff>
```

## Перевод меток и описаний внутреннего интерфейса ##

В этом примере мы переведем следующий элемент формы Flux:
(Но помните, что это - раздел (section) Configuration в шаблоне (template) страницы. Шаблонам содержимого (content) и модуля
(plugin) требуется дополнительный тег ``flux:form.content`` внутри ``flux:grid.column``)

```
<f:section name="Configuration">
    <flux:form id="settings.defaultpage" >
        <flux:form.sheet name="settings.basics">
            <flux:form.field.checkbox name="settings.includenavi" />
            <flux:grid>
                <flux:grid.row>
                    <flux:grid.column colPos="0" name="content"/>
                </flux:grid.row>
            </flux:grid>
        </flux:form.sheet>
    </flux:form>
</f:section>
```

Так, нам нужно добавить метки и/или описания для

- самой формы (form);
- вкладки (sheet) с названием "basic";
- флажка (checkbox) с названием "includenavi";
- столбца сетки (grid.column) с названием "content".

Для этого поместим следующий код в ``locallang.xlf`` внутри тега ``body`` каждого элемента:
```xml
<trans-unit id="ID.and.path.of.the.element">
      <source>Label or description to be shown</source>
</trans-unit>
```
И основой всегда будет ``flux.formID.type.name-of-the-element``

В нашем примере, в файл xlf (``locallang.xlf``) добавим следующее:

```xml
<trans-unit id="flux.defaultpage">
<source>Default Pagelayout</source>
</trans-unit>
<trans-unit id="flux.defaultpage.sheets.basics">
<source>Basic Configuration</source>
</trans-unit>
<trans-unit id="flux.defaultpage.fields.includenavi">
<source>Include navigation</source>
</trans-unit>
<trans-unit id="flux.defaultpage.columns.content">
<source>Content-Area</source>
</trans-unit>
```

Как понятно из ``typo3conf/ext/flux/Classes/Form/AbstractFormComponent.php`` --> ``public function getLabel()``,
доступны следующие типы:

- sheets (flux.sheets.id)
- sections
- grids
- columns
- objects
- areas
- containers
- fields

### Файл стал таким: ###

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<xliff version="1.0">
    <file source-language="en" datatype="plaintext" original="messages"
          date="2012-10-17T17:55:17Z" product-name="**YOUREXT**">
        <header/>
        <body>
            <trans-unit id="flux.defaultpage">
				<source>Default Pagelayout</source>
			</trans-unit>
			<trans-unit id="flux.defaultpage.sheets.basics">
				<source>Basic Configuration</source>
			</trans-unit>
			<trans-unit id="flux.defaultpage.fields.includenavi">
				<source>Include navigation</source>
			</trans-unit>
			<trans-unit id="flux.defaultpage.columns.content">
				<source>Content-Area</source>
			</trans-unit>
        </body>
    </file>
</xliff>
```

## Работа с другими (дополнительными) языками ##

Для добавления переводов для других языков, необходимо создать файлы ``locallang.xlf`` для каждого из них,
именуя их следующим образом:
``iso.locallang.xlf`` (например, ``ru.locallang.xlf``). И файл будет иметь такую структуру:

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<xliff version="1.0">
    <file source-language="en" datatype="plaintext" original="messages"
          date="2012-10-17T17:55:17Z" product-name="**YOUREXT**">
        <header/>
        <body>
        </body>
    </file>
</xliff>
```

Чтобы сообщить TYPO3 язык этого файла, недостаточно одного его названия, также необходимо добавить в тег ``<file>`` атрибут
``target-language`` ISO код языка:

```xml
<file source-language="en" datatype="plaintext" original="messages"
date="2012-10-17T17:55:17Z" product-name="ext-name" target-language="ru">
```

Как видно, мы добавили целевой язык "ru", изменив **target-language="ru"**

Далее, нужно добавить перевод, как уже делалось для файла на языке по умолчанию, но с одной разницей - вместо
```xml
<source>my translation</source>
```

нужно использовать

```xml
<target>my translation</target>
```

Теперь русский файл выглядит так (``ru.locallang.xlf``):

```xml
<?xml version="1.0" encoding="utf-8" standalone="yes" ?>
<xliff version="1.0">
	<file source-language="en" datatype="plaintext" original="messages"
	      date="2012-10-17T17:55:17Z" product-name="**YOUREXT**" target-language="ru">
		<header/>
		<body>
			<trans-unit id="flux.defaultpage">
				<target>Шаблон страницы по умолчанию</target>
			</trans-unit>
			<trans-unit id="flux.defaultpage.sheets.basics">
				<target>Основная настройка</target>
			</trans-unit>
			<trans-unit id="flux.defaultpage.fields.includenavi">
				<target>Включение навигации</target>
			</trans-unit>
			<trans-unit id="flux.defaultpage.columns.content">
				<target>Содержимое</target>
			</trans-unit>
		</body>
	</file>
</xliff>
```

## Использование языкового файла для внешнего интерфейса (страниц сайта) ##

Если нужно использовать метки или описания при выводе на страницы сайта, то их также необходимо поместить в файл
``locallang.xlf`` целевого языка. Для вывода метки ``contactperson``, можно добавить следующий фрагмент в файл
Template, Layout или Partial:

```
<f:translate key="contactperson" extensionName="YOUREXT"/>
```

То же самое можно сделать при помощи встроенного синтаксиса:

```
{f:translate(key: 'contactperson', extensionName: 'YOUREXT')}
```

А в ``locallang.xlf`` разместите следующий код внутри тега body:

```xml
<trans-unit id="contactperson">
	<source>Contactperson</source>
</trans-unit>
```

Опять же, для перевода на другой язык, поместите следующий фрагмент в свой файл ``iso.locallang.xlf`` (в нашем примере это
``ru.locallang.xlf``):

```xml
<trans-unit id="contactperson">
	<target>Контактное лицо</target>
</trans-unit>
```
