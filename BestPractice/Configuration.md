## TYPO3 на базе Fluid: лучшие решения по настройкам

Перед прочтением этой главы, необходимо кое-что знать. Расширения этого семейства (по крайней мере, поставщики функционала:
fluidcontent, fluidpages, fluidbackend), все используют настройки режимов Fluid через TypoScript для чтения путей к шаблонам,
которые должны быть просканированы.

Затем сканируются файлы шаблонов (путём простой фильтрации файлов определенных форматов - html,
xml и т. д.) и анализируются на предмет параметров настроек пошаблонно (вроде человекочитаемых меток или значков для
обозначения этого шаблона).

Существует несколько способов задания путей для шаблонов, но лишь один строго следует соглашениям относительно Extbase/Fluid.
Все методы описаны в этой главе, наряду со следствиями из них вытекающими, так, чтобы легко можно было бы сопоставить с 
реальной ситуацией и выбрать правильное решение.

### Соглашение о настройках TypoScript

> Замечание: это соглашение применимо лишь тогда, когда для хранения шаблонов и настроек используется расширение, что крайне
> рекомендуется и официально поддерживается.

Для базовой настройки TypoScript (вроде обязательных путей для режимов - View) необходимо использовать соглашение TYPO3/Extbase:

```php
// in ext_tables.php
t3lib_extMgm::addStaticFile($_EXTKEY, 'Configuration/TypoScript', 'Description of configuration');
```

При этом ожидается, что `setup.txt` и не обязательно, но рекомендуемо, `constants.txt` будут помещены в папку
`EXT:myext/Configuration/TypoScript`. Рекомендуется использовать константы (constants), они всегда крайне полезны при работе с
TypoScript для настроек (в отличие от, например, создания объектов COA). Но так как практически весь TypoScript в любых
расширениях подобного типа отвечает исключитеьно за настройки, необходимо взять в привычку всегда использовать константы для
шаблонов любого типа: страниц, содержимого и модулей внутреннего интерфейса.

Настройки должны использовать следующие области именования, обозначенные соглашением:

```txt
# for frontend-related extensions
plugin.tx_myext.view {
	templateRootPath = ...
	...
}
plugin.tx_myext.settings {
	foo = bar
}

# for backend (EXT:fluidbackend currently) oriented extensions
module.tx_myext.view {
	...
}
module.tx_myext.settings {
	foo = bar
}
```

Следуя указанным соглашениям, структура кода становится понятной для всех, даже если расширение не является публичным,
это поможет в дальнейшем вам же при ревизии кода.

### Регистрация ключа расширения

> Это рекомендуемый способ интеграции с fluidcontent/fluidpages/fluidbackend и тому подобным,
> что обеспечивает правильные связи с расширениями и использует все известные соглашения из Extbase относительно ожидаемого
> местоположения файлов, ресурсов и тому подобных вещей.
> Соглашения относительно конкретных типов интеграции указаны в соответствующей главе этого файла.

Этот подход предполагает использование расширения. Оно уже может содержать файлы лишь для отдельного сайта,
либо можно создать новое (смотрите главу в "Рекомендации по программированию"). В файле ext_tables.php добавьте следующий код
для регистрации:

```php
// $_EXTKEY = 'myext';
Tx_Flux_Core::registerProviderExtensionKey($_EXTKEY, 'Content'); // to register content templates
Tx_Flux_Core::registerProviderExtensionKey($_EXTKEY, 'Page'); // to register page templates
Tx_Flux_Core::registerProviderExtensionKey($_EXTKEY, 'Backend'); // to register backend module templates
```

#### Последствия

Второй параметр указывает на название контроллера (Controller) и имеет следующие последствия:

* Ожидаемое название класса контроллера, если он имеется, то это будет Tx_Myext_Controller_ContentController. И этот класс
используется для формирования шаблона из файлов. Обратите внимание, что это подход, обратный обычному для логики дополнений на
Extbase, где необходимо сначала им самим вызвать контроллер, который, в свою очередь пытается найти соответствующий шаблон. В
этом случае, обнаруживается действие из шаблона, подключенного к контроллеру, и, если такое действие в шаблоне имеется,
то оно и используется вместо уже встроенного для формирования шаблона.
* Ожидается, что в TypoScript имеется хотябы определение для `plugin.tx_myext.view.templateRootPath`. А если шаблон использует
шаблонные части (Partials) и макеты (Layouts), то добавьте пути и для них.
* Ожидается, что шаблоны располагаются в папке `Resources/Private/Templates` внутри расширения, либо другой,
указанной в настройке TypoScript расширения. Соглашение рекомендует указанный путь, и это крайне рекомендуется делать для
 упрощения работы с расширением. Конечно, ничто не запрещает выбрать и другое местоположение.
  Замечание: чтобы поместить шаблоны в папке `fileadmin´ (что не рекомендуется, но все же возможно) обратитесь к главе о путях
  вне расширений.
* Если в шаблоне используется TypoScript, его необходимо разместить в `plugin.tx_myext.settings`, откуда он будет доступен во
Fluid в виде "волшебной" переменной шаблона {settings}, которую можно не передавать в Partials и т. п. для восприятия её там.

Это предпочтительный метод интеграции. Он требует хранить файлы шаблонов и ресурсов в расширении, что немного непривычно,
если вы привыкли хранить все это в папке fileadmin (как для TemplaVoila), но предпочтительнее с точки зрения последовательности
 в создании шаблонов. Пути настроек согласуются со всеми правилами для Extbase и Fluid,
 что дает возможность добавления языковых файлов без необходимости указания каждый раз длинных путей к файлу. Конечно,
 выгда здесь значительно большая, чем возможно здесь вкратце описать - _рассмотрите возможность использования указанного
 подхода, даже если создаете простейший шаблон._

### Простой способ регистрации пути к шаблону (Template)

> Этот метод не рекомендуется, так как при этом файлы шаблона отделены от облсти расширения. Лучше хранить файлы и настройки в
> расширении, таким образо их легче переносить и используется большее число правил для минификации кода,
> а практичность (для интеграторов) работы становится значительно выше.

Для использования альтернативного пути для шаблонов, например в папке `fileadmin` просто добавьте следующий TypoScript (в
существующую запись шаблона TypoScript):

```txt
# for fluidpages template paths
plugin.tx_fluidpages.collections.mycollectionname.templateRootPath = fileadmin/templates/page/
# for fluidcontent template paths
plugin.tx_fluidcontent.collections.mycollectionname.templateRootPath = fileadmin/templates/content/
```

> Замечание: EXT:fluidbackend __не поддерживает такой способ указания путей в TypoScript, требуется использование расширения,
об этом сказано выше__.

#### Последствия

При регистрации путей к шаблону указанным способом, без ассоциации с ним расширения, теряются следующие возможности:

* Невозможно использование `<f:translate>` и других проекторов (ViewHelpers), использующих контекст расширения для определения
используемых ими файлов; если же они используются, необходимо использовать полный путь LLL:... в атрибуте `key`.
* Невозможно использовать настройки TypoScript, если только не воспользоваться `<v:var.typoscript>` для их чтения,
что добавляет сложности.
* Невозможно переназначить метки LLL через TypoScript, что было бы возможно, если бы метки LLL были бы в расширении.
* Шаблон будет формироватся в области расширения, выводящего его, то есть - fluidcontent, fluidpages, fluidbackend.

Это может стать проблемой, а может и нет, но даже если потеря указанных вожможностей не важна,
подумайте вместо этого об использовании расширения. Это в любом случае выигрышней с долгосрочной перспективы и менее затратно.

### Наследие

Расширения fluidpages и fluidcontent все еще, по соображениям совместимости, поддерживают следующие пути:

```txt
plugin.tx_fed.fce.mycollectionname.templateRootPath = ...
plugin.tx_fed.page.mycollectionname.templateRootPath = ...
```
Они рассматриваются как устаревшие, но на данный момент все еще нет решения по их удалению.

Но если они попадутся, измените их, либо используя "Простой способ регистрации пути к шаблону" (например,
`plugin.tx_fluidpages.collections.mycollectionname.templateRootPath` и т. п.), либо, если это вообще возможно,
переместите их в расширение и используйте рекомендуемую регистрацию на основе ключа расширения.