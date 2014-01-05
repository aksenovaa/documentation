TYPO3 на базе Fluid: документация
==================================

> Рады видеть Вас на страницах документации по набору расширений Fluid TYPO3.

Документация состоит из следующих основных глав:

* [Введение](Introduction.md)
  Краткая характеристика расширений Fluid TYPO3 в форме списка.
* [Сетевые службы](OnlineServices.md)
  Ссылки на справку по проекторам - ViewHelpers, ссылки на чат поддержки, ссылки для скачивания пакетов и расширений.
* [Руководство для начинающих](BeginnersGuide.md)
  Руководство в форме книги, которую можно прочитать от корки до корки. Здесь говориться о базовых концепциях, справедливых для всех расширений.
* [Передовой опыт](BestPractice/)
  Передовой опыт во всех областях, связанных с созданием сайтов на основе расширений Fluid TYPO3. А именно:
  * [Советы и рекомендации по Настройкам](BestPractice/Configuration.md)
  * [Советы и рекомендации по программированию](BestPractice/CodeBuilding.md)
  * [Советы и рекомендации по миграции ресурсов и настроек (Setups)](BestPractice/Migration.md)
  * [Советы и рекомендации по созданию шаблонов содержимого (Content Template)](BestPractice/Content.md)
  * [Советы и рекомендации по созданию шаблонов страниц (Page Template)](BestPractice/Pages.md)
  * [Советы и рекомендации по интеграции дополнений (Plugin)](BestPractice/Plugins.md)
* [Руководства](Guides/)
  Набор руководств по использованию Flux API. А именно:
  * [Руководство по использованию компонентов форм - FormComponent](Guides/FormComponent.md)
  * [Руководство по использованию языковых файлов и переводу](Guides/WorkingWithLocallang.md)
* [Расширения](Extensions.md)
  Набор подробной документации по функциям, требованиям, опыту в использовании каждого из расширений.
* [Основные понятия](Concepts/)
  Развернутая информация по уникальной концепции работы TYPO3 на базе Fluid.
* [Рецепты](Cookbook/)
  Набор примеров кода.
* [Содействие](Contributing/)
  Для тех из вас, кто хочет внести свой вклад в код. Здесь описана технология и требования к оформлению кода.

## Внесение вклада в коллекцию документации

> Желаете помочь? Хотите исправить ошибки? Прекрасно! :)

Для внесения вклада в документацию необходимо лишь соблюдать несколько простых правил:

1. В текущей документации допустим лишь английский язык, включая и набор примеров кода (рецепты).
2. Соблюдайте структуру папок, не добавляйте новых, за одним исключением: в папке Cookbook примеры логически разделяются на
группы; но если приводимый пример не соответствует ни одной из существующих групп, можно добавить еще одну. При этом хорошо бы
добавить в эту новую группу сразу несколько примеров (а то как-то неудобно хранить в группе лишь один пример). Если же пример не подходит ни под одну группу, используйте для него группу `Miscellaneous` наряду с описательны названием для кода.
3. Старайтесь использовать те же слова и структуры, как и в другой документации. Всегда используйте то же форматирование!
Строки ограничены 130 символами, а документация должна использовать моноширинный шрифт.
4. Официальный формат Markdown. Не используйте другие форматы!
5. За раз делайте лишь одно изменение. Если нужно, делайте несколько изменений (затем можно объединить их в один pull request).

Скорее всего вы будете добавлять лишь примеры в Рецепты, либо исправлять опечатки, ошибки в примерах или плохие формулировки в
документации. Руководство по написанию примеров в Рецептах находится в [README.md раздела Рецепты](Cookbook/)

### Инструкции по содействию

Изменить что-либо или добавить новый файл просто - можно (как и в других репозиториях) клонировать репозиторий в свою учётную
запись, внести изменения в виде commits, а затем создать pull requests.

Проще всего для этого воспользоваться кнопкой `Edit` в правом верхнем углу экрана при просмотре какого-либо файла. По щелчку,
репозиторий будет отщеплен в Вашу учётную запись, если он уже не был отщеплен. Затем внесите изменения и добавьте
сопровождающее сообщение; если подтвердить изменения (submit) будет создан так называемый pull request, а изменения будут выверены перед внесением (merged).

При написании сопровождающего сообщения, соблюдайте одно простое правило:

> Поле subject (в Github это шаблон со словами `Update <filename>`) - заполните это поле текстом, вроде
> `[DOC] Recipe for doing foobar in a baz template`. Другими словами, всегда описывайте тему, начиная с метки `[DOC]` и начинайте
> последующую фразу с заглавной буквы. Приставка `[DOC]` обязательно для этого репозитория, но помните, что другие репозитории
> могут использовать другую приставку, здесь не описываемую. Тема должна быть понятной, наихудшая из возможных может быть
> `Updated <insert filename>`, и ничего не говорящая о том, что реально было сделано и обновлено.
