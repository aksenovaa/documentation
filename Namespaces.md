## GIT Master & 6.1 6.0

Используете 6.2-dev: все отлично, не стоит беспокоиться.


Если обновлять flux до текущего git master в typo3 6.0 или 6.1, работа TYPO3 будет сломана из-за ошибки в самом ядре typo3:

http://forge.typo3.org/issues/54115

## Для исправления этого имеется 2 способа:
Лучший и рекомендуемый способ исправления - заменить код в файлах шаблонов. Для этого имеется специальный сценарий. 

Второй способ исправления - уловка (hack) в ядре TYPO3. Внизу страницы указана ссылка на него (смотрите Исправление ядра). 

### Изменение шаблонов (рекомендуется):

Измените все вхождения следующих элементов (смотрите сценарий, нет необходимости проделывать это вручную)

* ``{namespace flux=Tx_Flux_ViewHelpers}`` на ``{namespace flux=FluidTYPO3\Flux\ViewHelpers}``
* ``flux:flexform`` на ``flux:form``
* ``flux:flexform.grid`` на ``flux:grid``
* ``flux:flexform.grid.column`` на ``flux:grid.column``
* ``flux:flexform.grid.row`` на ``flux:grid.row``
* ``flux:flexform.container`` на ``flux:form.container``
* ``flux:flexform.data`` на ``flux:form.data``
* ``flux:flexform.object`` на ``flux:form.object``
* ``flux:flexform.section`` на ``flux:form.section``
* ``flux:flexform.sheet`` на ``flux:form.sheet``
* ``flux:flexform.field.wizard.add`` на ``flux:wizard.add``
* ``flux:flexform.field.wizard.colorPicker`` на ``flux:wizard.colorPicker``
* ``flux:flexform.field.wizard.edit`` на ``flux:wizard.edit``
* ``flux:flexform.field.wizard.link`` на ``flux:wizard.link``
* ``flux:flexform.field.wizard.list`` на ``flux:wizard.list``
* ``flux:flexform.field.wizard.select`` на ``flux:wizard.select``
* ``flux:flexform.field.wizard.slider`` на ``flux:wizard.slider``
* ``flux:flexform.field.wizard.suggest`` на ``flux:wizard.suggest``
* ``flux:flexform.field.checkbox`` на ``flux:field.checkbox``
* ``flux:flexform.field.controllerActions`` на ``flux:field.controllerActions``
* ``flux:flexform.field.custom`` на ``flux:field.custom``
* ``flux:flexform.field.file`` на ``flux:field.file``
* ``flux:flexform.field.inline`` на ``flux:field.inline``
* ``flux:flexform.field.inline.fal`` на ``flux:field.inline.fal``
* ``flux:flexform.field.input`` на ``flux:field.input``
* ``flux:flexform.field.relation`` на ``flux:field.relation``
* ``flux:flexform.field.select`` на ``flux:field.select``
* ``flux:flexform.field.text`` на ``flux:field.text``
* ``flux:flexform.field.tree`` на ``flux:field.tree``
* ``flux:flexform.field.userFunc`` на ``flux:field.userFunc``
* ``flux:flexform.content`` на ``flux:form.content``

Все это автоматически выполняет следующий сценарий:

**Используете на свой страх и риск, измените $directory**

```php
<?php

$directory = '/Users/danilo/Sites/hmspl/typo3conf/ext/';

$replaceNamespaces = array(
	'{namespace flux=Tx_Flux_ViewHelpers}' => '{namespace flux=FluidTYPO3\Flux\ViewHelpers}',
);

$replaceViewHelpers = array(
	'flux:flexform' => 'flux:form',
	'flux:flexform.grid' => 'flux:grid',
	'flux:flexform.grid.column' => 'flux:grid.column',
	'flux:flexform.grid.row' => 'flux:grid.row',
	'flux:flexform.container' => 'flux:form.container',
	'flux:flexform.data' => 'flux:form.data',
	'flux:flexform.object' => 'flux:form.object',
	'flux:flexform.section' => 'flux:form.section',
	'flux:flexform.sheet' => 'flux:form.sheet',
	'flux:flexform.field.wizard.add' => 'flux:wizard.add',
	'flux:flexform.field.wizard.colorPicker' => 'flux:wizard.colorPicker',
	'flux:flexform.field.wizard.edit' => 'flux:wizard.edit',
	'flux:flexform.field.wizard.link' => 'flux:wizard.link',
	'flux:flexform.field.wizard.list' => 'flux:wizard.list',
	'flux:flexform.field.wizard.select' => 'flux:wizard.select',
	'flux:flexform.field.wizard.slider' => 'flux:wizard.slider',
	'flux:flexform.field.wizard.suggest' => 'flux:wizard.suggest',
	'flux:flexform.field.checkbox' => 'flux:field.checkbox',
	'flux:flexform.field.controllerActions' => 'flux:field.controllerActions',
	'flux:flexform.field.custom' => 'flux:field.custom',
	'flux:flexform.field.file' => 'flux:field.file',
	'flux:flexform.field.inline' => 'flux:field.inline',
	'flux:flexform.field.inline.fal' => 'flux:field.inline.fal',
	'flux:flexform.field.input' => 'flux:field.input',
	'flux:flexform.field.relation' => 'flux:field.relation',
	'flux:flexform.field.select' => 'flux:field.select',
	'flux:flexform.field.text' => 'flux:field.text',
	'flux:flexform.field.tree' => 'flux:field.tree',
	'flux:flexform.field.userFunc' => 'flux:field.userFunc',
	'flux:flexform.content' => 'flux:form.content',
);

die('MAKE A DAMN BACKUP, YOU COWBOY!' . PHP_EOL);

$dir = new RecursiveDirectoryIterator($directory);
$ite = new RecursiveIteratorIterator($dir);
foreach($ite as $file) {
	if ('html' !== $file->getExtension()) {
		continue;
	}

	$content = file_get_contents($file->getPathname());

	$hasNamespace = FALSE;
	foreach ($replaceNamespaces as $oldNamespace => $newNamespace) {
		if (FALSE === strpos($content, $oldNamespace)) {
			continue;
		}

		$hasNamespace = TRUE;
		$content = str_replace($oldNamespace, $newNamespace, $content);
	}

	if (FALSE === $hasNamespace) {
		continue;
	}

	foreach ($replaceViewHelpers as $oldViewHelper => $newViewHelper) {
		$newViewHelper = str_replace('$', '\\$', $newViewHelper);
		$content = preg_replace('/<(\/?)' . preg_quote($oldViewHelper) . '([\s\/>])/', '<$1' . $newViewHelper . '$2', $content);
	}

	file_put_contents($file->getPathname(), $content);

	print_r('Modified ' . $file->getFilename() . PHP_EOL);
}

?>
```


### Исправление ядра:

Измените одну строку кода:

https://review.typo3.org/#/c/25814/9/typo3/sysext/fluid/Classes/Core/Parser/TemplateParser.php

Можно спокойно сделать это, так как эти изменения уже будут внесены в следующие выпуски версий ядра 6.0 и 6.1.
