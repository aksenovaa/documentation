Fluid Powered TYPO3: Documentation
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

## Contributing to the documentation collection

> Wish to contribute? Have a correction? Great! :)

In order to contribute to the documentation you only need to respect a few easily remembered rules:

1. English is the only language allowed in this documentation - including cookbook recipes.
2. Respect the folder structure - don't add new folders, with one exception: in the cookbook folders are used to divide recipes
   into logical groups; if your recipe does not fit into any of the existing groups add one for it. When you do this you are
   encouraged to add multiple recipes for that group (in order to avoid groups with just one recipe). If your recipe fits into no
   group at all, use the `Miscellaneous` group along with a descriptive name for the recipe.
3. Try to use the same wording and sentence structure as the other documentation. Always use the same formatting! Lines are
   manually wrapped at 130 characters and documentation should be edited using a monospace font.
4. The official format is Markdown. Do not use other formats!
5. Make one change at a time. If necessary, create multiple changes (you can then submit them as one pull request).

You will most likely only be adding recipes to the cookbook and correcting the occasional typo, incorrect example or bad wording
in the main docs. A guide for writing good cookbook recipes can be found in [README.md of the Cookbook section](Cookbook/)

### Contribution how-to

Making changes and new files is easy - you can (like any other repository) clone the repository to your own account, make changes
as commits and then create pull requests.

The easiest way by far is to use the `Edit` button in the top right hand corner when viewing each file. When you click this button
the repository is forked to your account - if it has not already bene forked. You then make your changes and add a cover message;
when you submit this a so-called pull request is made and your change will be evaluated before being merged.

When writing your cover message please observe this single rule:

> The subject field (on Github, it has a placeholder saying `Update <filename>`) - please fill in this field with a text like
> `[DOC] Recipe for doing foobar in a baz template`. In other words: always write a subject and always begin it with `[DOC]` and
> begin the following sentence with a Capital letter. The `[DOC]` prefix is mandatory in this repository but note that other
> repositories also use other prefixes not described here. Make your subjects descriptive - the worst possible subject is one
> that just says `Updated <insert filename>` and says nothing about what was actually updated.
