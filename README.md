Fluid Powered TYPO3: документация
==================================

### Дорогой читатель,

благодарим за проявленный интерес к проекту Fluid Powered TYPO3!

Эта документация разъясняет всё, что Вы хотели бы узнать и чему хотели бы научиться в области шаблонирования при помощи Fluid Powered TYPO3.

Надеемся, что это поможет найти свой новый и удобный путь создания шаблонов в этом наборе расширений. Со своей стороны мы попытались дать всю возможную помощь для достижения этой цели в кратчайшие сроки и минимальными усилиями. Наши расширения делают всю рутинную работу, тем самым помогая сосредоточиться на самом создании шаблонов, а не на способах их интеграции в TYPO3.

### Введение

> Резюме: во Fluid Powered TYPO3 файлы шаблонов и есть основной интерфейс, API, а настройки TypoScript выступают лишь способом добавления или переназначения файлов шаблонов и других ресурсов, а также определяют на основе констант настройки, которые могут менять редакторы, чтобы, в свою очередь, изменить выводимый на страницы код шаблонов.

Прежде чем начать, необходимо уяснить наиболее важную вещь, которую нужно поминть относительно Fluid Powered: **в основе Fluid Powered TYPO3 лежат правила**, доставшиеся в наследство как от Extbase и Fluid, так и от самой TYPO3 CMS. Целью расширений Fluid Powered TYPO3 является **упрощение интеграции**. Чтобы это стало понятным, можно взглянуть на написанный на Fluid шаблон, без *упрощения интеграции* при помощи `fluidpages`, обработка шаблона Fluid в качестве шаблона страницы требует указания и настройки специфичных параметров TypoScript и соотнесения (просто, как пример) значений полей выбора макетов внутреннего/backend или внешнего/frontend интерфейсов с файлами обрабатываемых шаблонов. Но, воспользовавшись *упрощённой интеграцией* со стороны `fluidpages`, использование, скажем, шаблона страницы упрощается до простого *размещения его в определённой директории и добавления этой директории к набору*.

Главное отличие указанного подхода и принятым (в TYPO3 CMS) подходом, *настройка выше правил/configuration over convention*,  в том, что вместо грамозкой настройки, где, например, регистрируются все новы типы элементов содержимого и параметры шаблона в виде параметров TypoScript, Fluid Powered TYPO3 предлагает минимальную настройку *путей* вместо полных структур, а лишь затем обнаружение и использование шаблонов в указанных местах. Причин такого выбора огромное количество, и здесь — не место для рассмотрения каждой из них, подробнее узанать об этом можно в следующих главах.

Также мы следуем практике *вкючение в (Fluid) XML каждого шаблона параметров, при помощи которых редактор может влиять на выводимый шаблон*. То есть, можно, например, добавить поле, которое включит вывод нижнего колонтитула на страницы сайта. При этом Fluid Powered TYPO3 прочитает эту настройку прямо из шаблона и выведет её в виде полей-настроек во внутреннем интерфейсе/backend TYPO3. Эта стратегия исползуется во всех контекстах Fluid Powered TYPO3 для *избавления от необходимости создания дополнительных полей базы данных для значений параметров настроек, отделённых от самих шаблонов*. Проще говоря, вы просто указываете Fluid Powered TYPO3, где находятся файлы с шаблонами, а затем начинаете создание/использование этих шаблонов без необходимости какой-либо дополнительной настроки.

Однако, есть несколько ключевых аспектов, расходящихся с общепринятой практикой TYPO3 CMS. Держа в голове эти несколько простых вещей можно свести к минимуму всю возможную путаницу.

#### TypoScript декларативен

> Резюме: во Fluid Powered TYPO3 каждая TypoScript настройка рассматривается, как декларативная, то есть здесь такие вещи, как `stdWrap` и option splits не применимы к значениям.

В традиционных шаблонах TYPO3 CMS широго распространены так назваемые *объекты TypoScript*, содержащие псевдо инструкции для определения всего, начиная от значений атрибутов тегов HTML до условий по формированию элементов в зависимости от значений записей. В этом, традиционном подходе интеграторы влияют на вывод элементов, *изменяя инструкции вывода/rendering TypoScript*. Во Fluid Powered TYPO3 требования к интеграторам слегка другие — интеграторы должны подстроить параметры, которые будут доступны редакторам, способ вывода элементов и все условия при которых элемент выводиться нужным способом при помощи *создания новой версии файла шаблона и изменяя содержимое этого файла*. Вместо создания огромного набора переназначающих и дополняющих значений TypoScript, интегратор просто добавляет новый файл шаблона по местоположению их набора (при необходимости, изменяя существующие), а затем занимается самим шаблоном, изменяя нужным образом каждый его аспект в плане формирования и удобства для изменения со стороны редакторов. А затем используются изменённые шаблоны.

Этот подход предполагает, что *TypoScript рассматривается как только лишь декларативный — во Fluid Powered TYPO3 он используется исключительно для настроек типа констант*, за всё остальное отвечает каждый отдельный шаблон/template, частичный шаблон/partial template или макет/layout.

####  Автоматизация опирается на правила

> Резюме: во Fluid Powered TYPO3 почти исключительно используется `нижнеийГорбатыйСинтаксис/lowerCamelCase` и `ВерхнийГорбатыйСинтаксис/UpperCamelCase`, за исключением нечасто используемого традиционного контекста, с использованием `нижнего_регистра_с_подчёркиванием`, а именно — в определении ключей расширений.

Большая часть функций, включенных во Fluid Powered TYPO3 в виде различных расширений TYPO3 *требуют соблюдений правил*. То есть, *вы более ограничены в, например, выборе названий для своих ресурсов*. Например, *ожидается, что названия файлов будут в формате `UpperCamelCase`*, а, *почти всегда, форматирование `snake_case` недопустимо*. Это отличается от традиционных TypoScript- и TCA- настроек, но благодаря этому многое в интеграции Fluid Powered TYPO3 происходит автоматически. Детальное описание правил именования приводится в следующих главах.

Нет правил без исключение, что справедливо и в этом случае: в некоторых обстоятельствах необходимо использовать традиционный формат, но это всегда понятно (например, некоторые проекторы/ViewHelpers принимают параметры `extensionKey`, и эти ключи расширений по прежнему могут содержать подчёркивания). Эти случаи всегда чётко документированы в описаниях параметров, атрибутах проекторов/ViewHelper, комментариях PHP doc и так далее.

### Итог

Подытожим важнейшие пункты:

> правила именования обязательны для файлов и параметров настроек, TypoScript рассматривается только в своей декларативной сути, а настраиваемые параметры производятся путём подмены (через настройку, а не физическую замену) файлов шаблонов по отдельности или всех вместе.
