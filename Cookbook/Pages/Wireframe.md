## Рецепты для TYPO3 на базе Fluid: каркас шаблона страниц

> Автор: Claus Due, команда TYPO3 на базе Fluid. Лицензия: [GPLv3](http://www.gnu.org/copyleft/gpl.html.)

### Назначение: каркас сайта без необходимости редактирования файлов шаблона

**Не предназначено для рабочего применения** - применяется на страдии разработки; позволяет редакторам содержимого начинать
создавать содержимое сайта на пустом TYPO3 сайте, полностью подготовленному к переключению на использование другого шаблона по
его готовности.

Этот шаблон - простейший каркасный, где можно назначить любой номер столбца содержимого для вставки в него элементов
содержимого, наряду с самым простым меню, начинающимся с `entryLevel="0"`. Для столбцов содержимого можно назначить метки,
используемые как атрибуты `data-name` в выводимых контейнерных элементах. Два дополнительных поля позволяют добавлять
**рудиментарные начальные** стили и сценарии. Для простоты, предусмотрен флажок, при включении которого,
загружается последняя версия jQuery посредством CDN (внешний хостинг сервис, предоставленный со стороны Google).

По выходу сайта из каркасной стадии, этот шаблон необходимо отключить, чтобы предотвратить его использование на сайте!

### Исходные файлы

Разместите, как и любые другие файлы, в расширении поставщике (Provider Extension), например, `Wireframe.html` в
`Resources/Private/Templates/Page/`.

```xml
{namespace flux=FluidTYPO3\Flux\ViewHelpers}
{namespace v=Tx_Vhs_ViewHelpers}
<f:layout name="Page" />
<f:section name="Configuration">
	<flux:form id="wireframe" label="Wireframe template" icon="{f:uri.resource(path: 'Icons/Page.gif')}">
		<flux:field.checkbox name="settings.jQuery" label="Include Google-hosted jQuery library" />
		<flux:field.select name="settings.numberOfColumns" requestUpdate="TRUE" items="1,2,3,4,5,6,7,8,9"
			label="Number of content columns" />
		<v:iterator.loop count="{settings.numberOfColumns}" iteration="iteration">
			<flux:field.input name="content.{iteration.index}.label"
				label="Name, content column {iteration.index}" />
		</v:iterator.loop>
		<flux:field.text name="settings.script" label="Temporary script" cols="120" />
		<flux:field.text name="settings.style" label="Temporary styles" cols="120" />
	</flux:form>
	<flux:grid>
		<v:iterator.loop count="{settings.numberOfColumns}" iteration="iteration">
			<flux:grid.row>
				<flux:grid.column name="{iteration.index}" colPos="{iteration.index}"
					label="{v:var.get(name: 'content.{iteration.index}.label')}" />
			</flux:grid.row>
		</v:iterator.loop>
	</flux:grid>
</f:section>

<f:section name="Main">
	{v:asset.script(name: 'jQeury', path: '//ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js'
		external: 1, standalone: 1) -> f:if(condition: settings.jQuery)}
	{settings.script -> v:asset.script(name: 'temporaryScript') -> f:if(condition: settings.script)}
	{settings.style -> v:asset.style(name: 'temporaryStyle') -> f:if(condition: settings.style)}
	<v:page.menu entryLevel="0" />
	<v:iterator.loop count="{settings.numberOfColumns}" iteration="iteration">
		<div class="content-column content-column-{iteration.index}" id="content-column-{iteration.index}">
			<v:content.render column="{iteration.index}" />
		</div>
	</v:iterator.loop>
</f:section>
```
