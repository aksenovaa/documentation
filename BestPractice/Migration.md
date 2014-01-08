## TYPO3 на базе Fluid: лучшие решения по миграции

Если вы ранее начинали с EXT:fed, сейчас необходимы некоторые приготовления для переноса существующих элементов содержимого и
макетов страниц во EXT:fluidcontent и EXT:fluidpages.

This chapter should give you an advice on how such migrations would work in general.

### Перво-наперво

FED/Flux в первой своей реинкарнации давали возможность размещения своих шаблонов где-либо в файловой системе и ссылаться на
них через TypoScript. Эта концепция МОЖЕТ использоваться до сих пор, но лучше воспользоваться "расширением поставщиком" для
представления своей работы в виде законченной вещи.

В этом документе подразумевается использование именно такого расширения поставщика. Ознакомьтесь с главой
[лучшие решения по программированию](CodeBuilding.md), чтобы уяснить, как автоматизировать и упростить свою работу.

Расширение поставщик должно включать по крайней мере следующие файлы:
```plain
Configuration
 -> TypoScript
    -> setup.txt
    -> constants.txt
ext_emconf.php
ext_tables.php
ext_localconf.php
```

Для простоты и в шутку мы назовем расширение ``fluidtypo3_migration``.

#### TypoScript перед миграцией

В старой настройке (setup), элементы регистрировались в FED/Flux как-то так:
```
plugin.tx_fed.fce.mycollectionname.templateRootPath = ...
plugin.tx_fed.page.mycollectionname.templateRootPath = ...
```

#### TypoScript после миграции

Нам нужно следовать стандартам. Ранее для ``flux`` пришлось найти свой способ, но на данный момент мы используем стандартную
регистрацию режимов (view) каждого расширения FluidTYPO3. Как вы уже могли догадаться,
TypoScript превратиться во что-то подобное:

```plain
# View Registration
plugin.tx_fluidtypo3migration.view {
  label = FluidTYPO3 Migration Elements
  extensionKey = fluidtypo3_migration # notice this entry. Comes in handy when using extension names with underscores
  templateRootPath = EXT:fluidtypo3_migration/Resources/Private/Templates/
  partialRootPath = EXT:flux/Resources/Private/Partials/
  layoutRootPath = EXT:fluidtypo3_migration/Resources/Private/Layouts/
}
```

#### Регистрация расширения поставщика

Если расширение поставщик не создавалось автоматически, а миграция проводится вручную, необходимо зарегистрировать его во flux:
Согласно [главе о настройках](Configuration.md#extension-key-registration), необходимо добавить код в
``ext_localconf.php`` *и* ``ext_tables.php``.

```php
// If your extension contains fluidpages page templates/layouts
Tx_Flux_Core::registerProviderExtensionKey('fluidtypo3_migration', 'Page');
// If your extension contains fluidcontent FCE templates:
Tx_Flux_Core::registerProviderExtensionKey('fluidtypo3_migration', 'Content');
// If your extension containts Backend modules:
Tx_Flux_Core::registerProviderExtensionKey('fluidtypo3_migration', 'Backend');
```

### Fluid шаблоны страниц

> STUB: how to change oldschool TS setup to new location, recommendation for using an extension as provider,
> recommendation for using native view TS locations and the ext_tables.php code to register an extension.

> STUB: how to ensure page templates are in the right template location. Previously, you were a bit more free to use
> custom paths, now, you should always add page templates as if they belong to a "Page" controller and only add the
> templateRootPath up to where the "Page" folder is located.

> STUB: description of the connection between DB field values for selected page templates, the provider collection to
> which they belong and the location of the template file that is selected. This will allow more experienced users to
> figure out manual migrations or how to create those EXT update scripts for their own providers if they rename files
> fx.

### Fluid шаблоны содержимого

> STUB: old location to new pragma - same as for pages.

> STUB: page template locations - same as for pages, except enforcing the "Content" controller - note: this is quite different
> from earlier practices where an "Elements" or even "FCE" controller had been used in examples; it should be described how to
> rename these template folders and adjust the TS (which before, might have contained the "/Elements/" or "/FCE/" segments).

> STUB: connection between DB fields and provider collection and files.

### Общая характеристика дополнений на основе Flux

> STUB: explanation about EXT:builder's CLI commands which can validate a template, ensuring it is fully checked for bad VH class
> names, missing/changed/now-required arguments and such.

> STUB: explanation about using EXT:builder to quickly generate a new skeleton extension which can receive template files from
> an older version extension which can sometimes be easier than renaming everything.

### От FED к ?

> STUB: migrating ViewHelpers used in templates from FED to VHS counterparts - to be extended as we collect information, but
> should at least contain basic information about ViewHelpers like `fed:data.var` becoming `v:var.set` and `v:var.get` with
> each their own responsibility, and especially a section about ANY asset-type ViewHelper (fed's PageRenderer collection
> or script/style ViewHelpers for example) which should be converted to VHS assets.

> STUB: migrating other FED classes - this one may be a bit too exotic and/or related to antique code, but if you can, describe
> that most services and utilities from FED are now available as EXT:tool and most of them are drop-in replaceable by changing
> the class name that's injected or called statically. Not many changes have been made in EXT:tool since splitting it from FED.
