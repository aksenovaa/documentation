Добро пожаловать, автор!
========================

От имени всей команды разработчиков TYPO3 на базе Fluid, благодарим Вас за желание внести вклад в наши проекты - путём
публикации вопросов, запосов pull или просто путём решения возникающих проблем со службой поддержки,
что может в будущем помочь другим пользователям.

Вот *ультра компактное* руководство для этого.

На следующей диаграмме приведена вся структура приложений и порядок работы с ними в отношении фиксаций/commits,
объединения/merges и подготовки к публикации/release. Также можно посмотреть, какой тип фиксации/commit (например,
ваша фиксация/commits, добавленная с запросом к нам на объединения/merge) должен производиться и в какой ветви. Вкратце,
исправление ошибок (bug fixes) необходимо помещать в ветвь "staging" версии, для которой применяется это исправление,
а создавать новый функционал нужно в ветви "development". Ветвь "master" можно загрузить/pull и безопасно использовать на
рабочих сайтах - в ней всегда находится последняя версия из TER.

![Диаграмма рабочего процесса FluidTYPO3 Git](Images/GitFlowDiagram.png)

## Отправка сообщени о проблемах (Submitting Issues) - запросы о новом функционале (Feature Requests),
## сообщения об ошибках (Bug Reports), вопросы к поддержке (Support Questions)...

Вы слышали все это раньше, но на всякий случай:

* Передо созданием сообщения об ошибке, убедитесь, что ошибка, о которой вы сообщаете...
  - еще не исправлена (если сомневаетесь: ветвь Git master всегда актуальна);
  - уже не была замечена и о ней еще не сообщалось.
* Сообщая об ошибках, укажите информацию о ваших целях, и о том, какие ошибки или сообщения (помните: сообщения в журнале
  ошибок красноречивее тысячи слов) вы получали.

Вкратце: нам должно быть понятно, что сообщение актуально и содержит полезную информацию. Это позволит эффективнее решить
возникшую проблему - все просто

## Оформление запросов Pull

Убедитесь, что полностью прочитали и усвоили [руководство по содействию][contributionGuide].

Необходимо подчеркнуть, как на самом деле важно содействие в ветвях по функционалу. Это позволяет сохранять чистоту в своей
копии flux в ветви master, также, как и в нашем репозитории. Начинайте сообщение ``git checkout -b myCoolFeature``.

Делайте лишь один commit для каждого изменения и лишь одно изменение в каждом commit. Пример: если вы собираетесь создать
частичную функцию, которая требует нескольких изменений в существующем коде для подготовки грядущих функций,
сначала проведите несколько индивидуальных commits с требуемыми изменениями, а затем проведите "настоящую" работу поверх этого.

Итак, вы подготовили отличное исправление ошибки в последней версии flux, и хотите, чтобы оно стало доступным для всех?
Превосходно! Мы думаем, что вы уже прочитали [руководство по содействию][contributionGuide],
чтобы ваш вклад соответствовал передовому опыту, поэтому все должно пройти отлично.

На самом деле, мы очень ответственно относимся к сообщениям commit, также, как и наш CGL - сэр Travis. Мы проверяем каждое
сообщение commit, чтобы убедиться, что в вашем коде учитывалось CGL. Что, если у него или у нас возникают сомнения в коде?
Попытаемся объяснить на примере.

### Пример: сообщение об ошибках при commit

Итак, вы получили "wrong commit message" и мы "заставляем" вас изменить свое сообщение(я) о commit. Что теперь? GIT - это
волшебство, но непростое. Представьте, что имеется 2 commits. И вот как это будет выглядеть в Pull-Request:

```
Author - Message (commit hash)
------------------------------
Cedric Ziel - a lead-haxor addition to flUx! (aaaaa)
Cedric Ziel - Fixing a docs-issue (bbbbb)
```

Что не так? Все понятно, ведь вы читали [руководство][contributionGuide]! Commit (я ссылаюсь на него по hash) aaaaa не имеет
префикса и в начале есть заглавная буква.

Оптимальна структура будет такой:

```
Author - Message (commit hash)
------------------------------
Cedric Ziel - [FEATURE] New property x added
Cedric Ziel - [DOC] Documenting feature y
```

Вам необходим ``reword`` (изменить формулировку - сленг git) двух последних commits.

Для этого, необходимо сообщить о ``rebase``:
```
git rebase -i HEAD~2 # this will issue an interactive rebase of the last 2 commits off from HEAD
# an editor will come up, showing your last 2 commits, change the first words from pick to reword
# save & exit
# another editor will come up for both commits, make the topic changes there, save & exit
# A message should be shown the rebase is complete
git push --force $GITHUBREMOTE $FEATUREBRANCHNAME
# $GITHUBREMOTE is most likely "origin" in your case if you didnt change anything
# $FEATUREBRANCHNAME is the name of your branch you made the changes on
# this will overwrite the remote's history entirely and the force is absolutely neccessary there
# The changes will shop up here immediately and Travis will try to build again
```

### Пример: обновление своей GitHub fork

Время от времени, безусловно, необходимо обновлять свой репозиторий, вытягивая (pulling) изменения.

Для упрощения, мы называем оригинальные репозитории FluidTYPO3 нашим ``upstream - истоком``.

Для фактического обновления своей вилки/fork, необходимо либо удалить ее, либо вытянуть/pull изменения непосредственно через git.
Мы рекомендуем следующее:

```
# Add the upstream repo as git remote
git remote add upstream https://github.com/FluidTYPO3/flux.git
# Go to your master branch
git checkout master
# Now, pull in changes from upstream. Rebase prevents merge commits and conflicts
git pull --rebase upstream master
# Up to date, yeah-unicorns everywhere!
```

Мы рекомендуем делать это всякий раз, при создании ветви с новым функционалом.

Полное руководство доступно на http://fedext.net/overview/contributing/contribution-guide.html

Напутствие: добро пожаловать в растущую гвардию участников! :)

[contributionGuide]: https://github.com/FluidTYPO3/flux.git "FluidTYPO3 contribution guide"
[pomodoro]: http://www.pomodorotechnique.com/ "The Pomodory Technique"
