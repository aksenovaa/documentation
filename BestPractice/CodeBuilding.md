## TYPO3 на базе Fluid: лучшие решения по программированию

Этот раздел документации посвящён решениям по автоматическому формированию кода. Существует несколько решений для
автоматического формирования кода расширений TYPO3 на базе Fluid, работающих с учётом всех правил. Первое,
и наиболее важное из таких решений по формированию кода - это EXT:builder, которое можно найти на Github:
[https://github.com/FluidTYPO3/builder](https://github.com/FluidTYPO3/builder).

EXT:builder предлагает несколько методов про проверке и построению кода.

### Формирование расширений поставщиков (Provider Extension)

Запущенная из консоли эта CLI команда, доступная при установленном EXT:builder, создаст каркас расширения с базовым набором 
файлов и настроек, и все - лишь одной простой командой (из корня сайта TYPO3):

```bash
./typo3/cli_dispatch.phpsh extbase builder:providerextension <arguments>
```

Команда поддерживает большой набор аргументов для настройки формируемого расширения относительно его предназначения и создания.

```plain
ОБЯЗАТЕЛЬНЫЕ АРГУМЕНТЫ:
  --extension-key      Ключ формируемого расширения, которого не должно быть в папке typo3conf/ext.
  --author             Автор расширения, в формате "Имя Фамилия <name@example.com>" с необязательным названием
                       компании, при этом форма будет следующей: "Имя Фамилия <name@example.com>, Компания"

ВОЗМОЖНЫЕ АРГУМЕНТЫ:
  --title              Название формируемого расширения, по умолчанию "Provider extension for $enabledFeaturesList"
  --description        Описание формируемого расширения, по умолчанию 
                       "Provider extension for $enabledFeaturesList"
  --use-vhs            Если TRUE, добавляется зависимость от расширения VHS, что 
                       рекомендуется, включено по умолчанию
  --pages              Если TRUE, создаются основные файлы, применительно к
                       шаблонам Fluid Page
  --content            Если TRUE, создаются основные файлы, применительно к
                       шаблонам Fluid Content
  --backend            Если TRUE, создаются основные файлы, применительно к
                       модулям Fluid Backend
  --controllers        Если TRUE, формируются контроллеры для всех указанных
                       функций. При включении $backend, контроллер создается
                       не взирая на этот аргумент.
  --minimum-version    Минимально требуемая для этого расширения версия ядра,
                       по умолчанию - последняя LTS (на данный момент 4.5)
  --dry                Если TRUE, performs a dry run: does not write any files
                       but reports which files would have been written
  --verbose            If FALSE, suppresses a lot of the otherwise output
                       messages (to STDOUT)
  --git                Если TRUE, initialises the newly created extension
                       directory as a Git repository and commits all files. You
                       can then "git add remote origin <URL>" and "git push
                       origin master -u" to push the initial state
  --travis             Если TRUE, generates a Travis-CI build script which uses
                       TYPO3 на базе Fluid coding standards analysis and code
                       inspections to automate testing on Travis-CI
```

Depending on which toggles you use, template files will be built for EXT:fluidcontent, EXT:fluidpages and/or EXT:fluidbackend,
all containing a basic set of configuration enabling the file to be used by each extension. To make a "dry run" specify `--dry 1`
which means no files/folders are written but the intent to create each file/folder is output instead. You can even initialise
and initially commit all files as a git repository (requires the `git` CLI command to be available to the current shell user).

For example, such a build command might look like:

```shell
./typo3/cli_dispatch.phpsh extbase builder:providerextension test "Claus Due <claus@wildside.dk>" \
	--pages 1 --content 1 --controllers 1 --git 1 --travis 1 --use-vhs 1
```

Which would generate the extension key `test` authored by `Claus Due` with email `claus@wildside.dk` and include page and content
templates as well as controller classes for each, a Travis-CI build script to go along with the extension and finally will create
a git repository in the extension folder and initially commit all files.

You can then very easily install the generated extension also from the command line:

```shell
./typo3/cli_dispatch.phpsh extbase builder:install test
```

Which simply installs the extension key "test" (note: this feature only works on 6.0+ TYPO3 sites). All you then need to do is
load the static TypoScript into each root template where you want the content/page templates to be available - and start adding
your own templates or adjusting the pre-built placeholder templates.

### Generating unit and functional tests

When you are "done" (if such a thing is possible) with your templates and classes related to your templates - Controllers,
ViewHelpers etc. - EXT:builder allows you to generate unit and functional tests for automateed testing of your code. There are
commands to generate ViewHelper class unit tests (for when you create custom ViewHelpers) and further test creation is planned
(for example: template validation based tests so phpunit will catch any Fluid syntax errors or incorrect arguments used).

There are even commands in EXT:builder to perform inspections of your templates, reporting any bad arguments or obvious syntax
problems (which you may not detect, for example if you change a custom ViewHelper's name or arguments and forget about a template
which uses the old names) and detecting problems in your PHP code. More analysis features are planned, for example ones to detect
problems in annotations used for example in Controllers, validating them to ensure required class types exist - and more.

### Using Travis-CI

[Travis CI](https://travis-ci.org/) is a service that is free to use for OpenSource projects - what it provides is a way to run
automated "builds" of your code, using scripts you can configure. EXT:builder can create a Travis build configuration which uses
TYPO3 на базе Fluid standards and code inspections to check your code. When built (and activated in Travis-CI by signing up and
switching on your repository in the list of your repositories), the Travis-CI script will automatically install dependencies as
required by your extension. EXT:builder is then used to perform a range of tests and finally, phpunit is called on to run any unit
tests your extension may contain (which includes tests built by using EXT:builder itself).

Whenever you receive pull requests or push to your master branch, Travis will execute the build script and report any problems to
you, the repository owner. [An example of a Travis build; for EXT:builder itself](https://travis-ci.org/FluidTYPO3/builder) and
[the script which configures that build](https://github.com/FluidTYPO3/builder/blob/master/.travis.yml).

Since even the most basic automated Travis-CI script written by EXT:builder will perform many tests on syntax in both PHP and
Fluid you are highly encouraged to use Travis-CI. But you can of course also perform the tests as needed, simply by running the
approproate CLI command from EXT:builder's list of commands.
