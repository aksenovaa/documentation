TYPO3 на базе Fluid: концепции - провайдеры
===========================================

## Введение

Класс провайдера - одна из наиболее сложных концепций, используемых во Flux. Она открыта для "публичного" использования с тех
пор, как `fluidpages` и `fluidcontent` сами используют эту API, как возможность воспользоваться передовыми функциями Flux,
но делают они это через посредством ограниченной API. Если вы сами воспользуетесь повайдером, то откроются способы интеграции с
Flux, схожите с тем, что происходит во `fluidcontent` - на основе конкретных типов записей,
делая некоторые аспекты вывода (например, указание использования файла шаблона) динамическими,
на основе установленных в записях значениях.

### Устранение неоднозначности

Концепцию [расширений поставщиков (Provider Extensions)](ProviderExtensions.md) нельзя путать с концепцией [Провайдеров]
(Providers.md) (этот документ). Концепция раширений поставщиков говорит о структуре файлов, а концепция Провайдеров -
о типе класса.

### Слой API 2

Эта концепция слегка сложнее, чем концепции **слоя 1**; её понимание требует базовых знании PHP и расширений Extbase,
создание классов PHP и использования общего API классов PHP (дополнение - extending классов,
вызов методов и многое другое в том же роде).

## Что такое провайдер (поставщик)?

Провайдер - это класс, который:

1. Всегда ассоциирован с одной таблицей базы данных, например с `tt_content` или `pages`,
   либо любой своей таблицей (один провайдер для таблицы).
2. Все проводиться через Flux при создании, редактировании, перемещении, удалении, сохранении,
   выводе и т. п. записи из этой таблицы
3. Переключатели (как то: обработать записи из связанной с ними таблицы) срабатывают лишь при определенных обстоятельствах,
   например, когда запись `tt_content` имеет (в поле) `list_type` с определенным значением,
   соответствующем определенному дополнению.
4. Содержат методы для получения информации по одному ряду (записи) из таблицы, с которой связан провайдер.
5. Содержат методы для управления записями из связанной с провайдером таблицы БД.
6. Используется в [управляемых Flux контроллерах](FluxControllers.md), когда они выводятся через внешние дополнения, для
   получения важной, связанной с выводом, информации, вроде того - какой использовать контроллер, файл шаблона,
   путь к объекту View и т. п.

Например, провайдер `fluidcontent` с названием `ContentProvider` связан с таблицей `tt_content` и срабатывает только если
значение в поле `CType` записи соответствует `fluidcontent_content` (то есть, это название дополнения для `fluidcontent`). Этот
провайдер используется во внутреннем интерфейсе для обработки экземпляров `FluidTYPO3\Flux\Form` на базе конкретно выбранного
элемента `fluidcontent`. Он читает значения, сохраненные в записи при заполнении и сохранении формы Flux. Он реагирует на
очистку кеша (путём обновления хранимых определений доступных шаблонов `fluidcontent`) и наконец, он интегрируется в
`ContentController` из `fluidcontent`, выводящий каждый элемент, и здесь, в контроллере,
он используется для указания переменных, путей к шаблону, названий желаемого контроллера и расширения контроллера,
и т. д. Затем Flux использует эти переменные и настройки для перевода запроса к нужному контроллеру по названию расширения и т.
п. (`fluidcontent` использует это для возможности добавить свой `ContentControllers` в другие расширения; и, так как элемент
`fluidcontent` идентифицируется по комбинации ключа расширения и названия шаблона, Flux (получая ключ расширения из записи в
`tt_content`, в данном случае - из комбинации индетификатора шаблона `fluidcontent`, например,
`fluidcontent_bootstrap:Alert.html`, а затем определяется название класса контроллера,
которому должен быть делегирован вывод элемента).

Механизм работы провайдера во всей красе можно посмотреть во `fluidcontent`, и здесь же можно понять:

## Зачем и когда использовать провайдер?

Классы провайдеров служат в качестве связи между внутренним и внешним интерфейсами, но ничего не мешает использовать их только
для внешнего или внутреннего интерфейсов. Основная выгода получается от ипользования [управляемого Flux контроллера]
(FluxControllers.md) в дополнении Extbase. При этом провайдер выступает как связь между записями и файлом шаблона, путями,
переменными и т. п., тем, что нужно контроллеру при выводе записи.

Диаграмма пересечения ниже показывает, какие из функций провайдера относятся к внутреннему, внешнему интерфейсам, и к обоим сразу:

![Зоны ответственности провайдера](../Images/ProviderResponsibilityIntersect.png)

Некоторые методы предназначены для управления записями и совершения действий, когда записи меняются; другие предназначены для
возвращения информации о том, какой класс контроллера использовать, из какого расширения и т. п. Эти методы используют ряд
одних и тех же простых методов - получение название файла шаблона. путей к шаблону, переменных формы Flux из записи и т. п.

При этом Flux может автоматически использовать провайдера, если контроллер - [управляемый со стороны Flux](FluxControllers.md).
А если нет - все еще можно использовать провайдер в контроллерах, но организовать вывод придется самостоятельно: самостоятельно
присвоить переменные, возвращенные провайдером для режима (View), вручную перейти к другим контроллерам,
если это применяется в приложении, и все в таком же разрезе. В основном, отказ от [управляемого Flux контроллера]
(FluxControllers.md) значит, что все взаимодействия с провайдером _в контексте внешнего интерфейса_ необходимо создавать
самостоятельно. Flux, однако, по прежнему будет реагировать на все функции класса провайдера,
связанные с внутренним интерфейсом, независимо, есть контроллер, или нет.

Все это вцелом, превращает провайдер в место для динамического определения шаблона, путей, переменных,
влияющих на вывод. Так как провайдер предназначен и для внутреннего, и для внешнего интерфейсов,
возвращаемые значения будут согласованы для обоих мест.

## Что должно быть в провайдере?

`FluidTYPO3\Flux\Provider\ProviderInterface` определяет множество разных функций, каждая из которых доступна при создании
подкласса (subclass) `FluidTYPO3\Flux\Provider\AbstractProvider`. Можно переназначить любой из этих методов при необходимости,
если, например, провайдер должен реагировать на сохранение записи, либо если нужно вернуть определенный файл шаблона или
переменную из него (провайдер делает гораздо больше, чем в приведенных примерах - нужно подробно изучить doc комментарии класса
интерфейса и базового класса; они также выводятся в IDE при создании подкласса/реализации (subclass/implement) упомянутых
класса/интерфейса).

Для определения названия таблицы БД и поля (если нужно), с которыми должен быть связан провайдер, используйте свойства класса из
`AbstractProvider`, а именно, `tableName`, `fieldName` и так далее. Например, `ContentProvider` из Flux связан с таблицей
`tt_content`, где задействовано поле `pi_flexform`, если значение поля `CType` (в классе провайдера называется
`contentObjectType`) соответствует `fluidcontent_content`. Значение `NULL` в любом из этих полей означает,
что ваш провайдер - общего назначения, _но и с более низким приоритетом_  будет подходит для записей из связанной таблицы БД.
И это также означает что если другой класс провайдер, имеет более узкое предназначение (например, если для соответствия ему нужно
еще и `fieldName`), то он сработает сначала, а ваш провайдер - после него.

И наконец, имеется настройка `priority`, которая также повышает или понижает приоритет провайдера для записей из связанной с
ним таблицы. Значение по умолчанию - `50`, которое можно понизить - делая провайдер менее важным, или повысить важность.

В некоторых случаях, вроде вывода предпросмотра в модуле Страница для записей из `tt_content`, первый провайдер,
выводящий предпросмотр, способен и исключить другие провайдеры из очереди вывода; и все это значит,
что если ваш провайдер связан с таблицей `tt_content`, необходимо быть более осторожным, и не переназначать, например,
приоритет ContentProvider `fluidcontent` - иначе можно убрать из отображения вложенную сетку для содержимого (content grids).

## Чего не должно быть в провайдере?

Для _разделения частей_ приложения, основная ответственность провайдера - _делегирование_ другим методам. Например,
провайдер может...

* Вводить сервисы из приложения, управляющего записями.
* Использовать ConfigurationManager для вставки TypoScript в шаблон переменных и нескольких местоположений.
* Динамически убирать и изменять переменные шаблона, перед тем, как они достигнут контроллера.
* Динамически изменять пути к шаблону, которые должен использовать ваш [управляемый Flux контроллер](FluxControllers.md).
* Переключать файл шаблона, который и выводит ваш контроллер, полагаясь на значения из выводимой записи.
* Возвращать пользовательский экземпляр `FluidTYPO3\Flux\Form`, построенный непосредственно на PHP.
* Настраивать некоторые (не все!) аспекты контекста контроллера - ключ расширения, хранилище `$this->settings` и другие.

но он не должен...

* Содержать большие куски бизнесс логики, например, для управления записями по сложному алгоритму.
* Использовать специализированные методы, работающие исключительно только для внутреннего интерфейса или только для страниц
  сайта.

Наряду с лучшей _специализацией_ для провайдера легче писать модульные тесты. И, так как он работает схожим образом и для
внутреннего, и для внешнего интерфейсов, легко гарантировать, что если он должным образом связан в одном контексте,
тоже справедливо и для других. Ввиду того, что он в основном делегирует к другим сервисам или классам,
становиться проще затыкать (to mock) эти так называемые "разветвители". И наконец, так убирается некий,
обычно необходимый код из классов контроллеров в отдельный, более дружелюбный для тестирования контекст. В качестве награды, Flux
имеет базовый класс для модульных тестов, который, при использовании, должен сразу же охватить множество пользовательских
методов по вашему выбору. Он называется `FluidTYPO3\Flux\Tests\Unit\Provider\AbstractProviderTestCase` и для него могут быть
запросто созданы подклассы (subclassed) для собственных модульных тестов.

## Провайдеры с проводами (Outlets) и розетками (Pipes)

Перед чтением этого раздела, хорошо бы взглянуть на [концепцию проводов и розеток](OutletsAndPipes.md), чтобы понять,
о чем идет речь. Вкратце: розетки (Pipes) - тип класса, берущий в одну функцию данные, "что-то" с ними делает,
и возвращает те же или другие данные; провода (Outlets) - тип класса, к которому прикреплено два набора розеток (Pipes):
входные и выходные. При этом провода (Outlets) используют розетки (Pipes) на входе для получения данных (например,
для их проверки и очистки), а розетки (Pipes) на выходе для вывода данных (например, для сохранения в файл, отсылки email,
сохранения записи в БД и т. п.). Как провода (Outlets), так и розетки (Pipes) полностью заменяемы и могут быть созданы
самостоятельно для решения собственных задач. Их крайне просто тестировать, используя модульные тесты.

И как же провайдер использует провода (Outlets) и розетки (Pipes)? Очень просто: если экземпляр формы,
возвращаемый провайдером, содержит провод (Outlet), то он вызывается и вызывает прикрепленные розетки (Pipes) _при сохранении
записи во внутреннем интерфейсе_. Фактически такое поведение может быть изменено или отклчено - вообще говоря,
создание своего класса провайдеа позволяет вам переназначить любое количество методов  для изменения или отключения
определенного поведения, включая все аспекты использования проводов (Outlets) в провайдерах.
