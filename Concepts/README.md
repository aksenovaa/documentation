TYPO3 на базе Fluid: Основные понятия
=====================================

## Введение

Этот раздел документации посвящен всем идеям, используемых командой создателей расширений TYPO3 на базе Fluid team.

Детально описаны концепции из приведенного ниже списка, и чем дальше по нему вы спускаетесь,
тем сложнее вопрос раскрывается. Поначалу необходимо знать лишь немногие, но чем дальше в лес,
тем нужнее становится знание и остальных идей.

Эта информация не для новичков, если вы только начали знакомство, то перейдите к [руководству для начинающих]
(../BeginnersGuide.md) и вернитесь к этому разделу при необходимости получения детальной информации по концепциям,
освещаемых в текущем руководстве.

## Содержание

* [Provider Extensions - расширения поставщики](ProviderExtensions.md)
  Здесь можно найти информацию о рекомендуемом хранишище шаблонов: расширениях TYPO3, содержащих шаблоны, применяемые на сайте.
* [Шаблоны](Templates.md)
  Здесь можно понять, как шаблоны Fluid используются в TYPO3 на базе Fluid.
* [Формы Flux](FluxForms.md)
  Все о формах, которые можно сделать при помощи Flux - разные способы их создания, совместная с TYPO3 их работа,
  разъяснение структуры форм и о том, как совместить разные их компоненты.
* [Контроллеры Flux](FluxControllers.md)
  Разъяснение того, чем контроллер Flux Extbase отличается от стандартного контроллера Extbase,
  и чем это полезна Flux API для специфичных целей.
* [Providers - провайдеры](Providers.md)
  Разъяснения о специальных шаблонных классах под названием `Providers`, поставляющих данные и управляющих одним специфичным
  типом записей - fx запуск и последующая обработка записей `tt_content` с `CType` вида `myspecialtype` при из сохранении. И
  многое другое - эта концепция тесно переплетается с концепцией `Форм Flux`, то, что лежит в истоке всего, что делает Flux.
* [Outlets and Pipes - розетки и провода](OutletsAndPipes.md) **Начиная с Flux 7.0.0**.
  Специфичная идея, которая может использоваться в увязке с `Контроллерами Flux`, либо `Провайдерами`. Проще всего представить
  в виде двух специальных типов классов, которые в связке проводят данные сквозь серию входных и выходных `розеток - Pipes` для
   их проверки, преобразования, манипуляции, хранения, отправки, журналирования, публикации (и много другого!) из любого
   ресурса к любому потребителю.

## Слои API

Те немногие концепции, которые вводит Flux, можно разделить на _слои_ в порядке возрастания сложности и гибкости. В
документации этого раздела будет использоваться понятие _слоя API_ функции, и вот, что это значит:

1. Внешний слой, **слой 1**: ViewHelpers, TypoScript, файлы шаблонов. Небольшая сложность.
   По большей части этот слой содержит _настройки полей форм, конфигурацию и вывод_, это простейший слой. Он используется
   интеграторами сайтов в форме, например, указания местоположения файлов шаблонов. Дизайнерами шаблонов и разработчиками для
   определения набора полей форм, доступных при редактировании элементов содержимого, страниц и тому подобного.
2. Внутренний слой, **слой 2**: классы, контроллеры, сервисы - "внутренности". Средняя сложность.
   Более развитые способы интеграции с Flux открываются при использовании PHP, вообще говоря,
   они не предназначены для доступа из TypoScript, либо из шаблонов Fluid. Так как этот слой использует лишь PHP,
   он требует дополнительных знаний о создании классов Extbase (либо отличную способность обучения на примерах,
   так как существует множество примеров расширений TYPO3 на базе Fluid в виде функционала, вроде `fluidpages`).
3. Слой ядра, **нижний слой**: для опытных разработчиков. Высшая степень сложности.
   Обычно этот слой не затрагивается. Почти нет функционала, касающегося этого слоя, но так можно влиять на логику Flux
   сверхтонким образом.

Можно создавать очень сложные сайты, используя возможности только лишь **слоя 1**, вроде `fluidcontent`,
`vhs` и `fluidpages`. Можно сделать и некоторые более сложные расширения, воспользовавшись возможностями **слоя 2**, вроде
[Провайдеров](Providers.md) и [Розеток/проводов](OutletsAndPipes.md). И наконец, возможно управлять практически всем,
интегрированным в **слой 1** и **слой 2** через чёрную магию **нижнего слоя**.

В повседневной работе мы не говорим о _слоях API_ как таковых, просто говорим о контексте интеграции (шаблон, контроллер,
TypoScript, и т. п.). Но это понятие используется в этом разделе документации, чтобы было возможно быстро оценить,
_насколько быстро и сложно можно научиться использовать определенный функционал при определенном уровне знания_.
