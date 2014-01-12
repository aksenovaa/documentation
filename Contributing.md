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

You have heard all this before, but just in case:

* Before submitting bugs, make sure the bug you are reporting...
  - Is not already fixed (if in doubt: the Git master branch is always up-to-date)
  - Is not currently reported and/or under review
* When submitting bugs, include information about what you wanted to achieve and which errors
  or messages (reminder: error log messages are worth a thousand words) you encountered.

In short: We want to trust that your report is valid and contains helpful information. This
allows us to be most efficient when assisting or fixing the problem; everything runs smoother.

## Making Pull requests

Please make sure, you read the entire [Contribution Guide][contributionGuide] in advance.

We need to stress how important it actually is to contribute in feature-branches. This allows
you to keep your copy of flux in the master branch clean and to be the same as in our repository.
Start of by issueing ``git checkout -b myCoolFeature``.

Make one commit per change and one change per commit only. Example: if you are going to create
a particular feature and this feature requires a few changes to existing code to prepare for
the feature, first make individual commits with the required changes and then create your
"real" work on top of this.

So, you have prepared a nice bugfix to the latest version of flux and want that to be available
to anybody? Great! As we assume, you consumed the [Contribution Guide][contributionGuide]
to make your contribution follow best practices, everything should be fine.

In reality, we tend to be very picky concerning commit messages and our CGL. - Sir Travis
is also. He checks every commit message and wants to make sure, you applied the CGL to your
code. So what if he-or we-complain about findings in your code? Let us try to explain by
example:

### Example: The wrong commit message

So you hit the "wrong commit message" issue and we "force" you to change your commit
message(s). What now? GIT is magic but not that easy to learn. Lets say you have 2 commits.
This is what it would look like in the Pull-Request:

```
Author - Message (commit hash)
------------------------------
Cedric Ziel - a lead-haxor addition to flUx! (aaaaa)
Cedric Ziel - Fixing a docs-issue (bbbbb)
```

What's wrong? You know it-because you read the [guide][contributionGuide]! The commit
(I reference them by hash) aaaaa is lacking a prefix and an uppercase letter at the
beginning.

The optimal structure would be:

```
Author - Message (commit hash)
------------------------------
Cedric Ziel - [FEATURE] New property x added
Cedric Ziel - [DOC] Documenting feature y
```

So you need to ``reword`` (git slang) your last two commits.

To do this, you need to issue a ``rebase``:
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

### Example: Refreshing your GitHub fork

As time goes by, you will certainly need to refresh your repo by pulling in remote changes.

For the sake of simplicity, we call the original FluidTYPO3 repositories our ``upstream`` remote.

To be able to actually refresh your fork, you could either delete it, or pull changes directly via
git. We recommend to do the following:

```
# Add the upstream repo as git remote
git remote add upstream https://github.com/FluidTYPO3/flux.git
# Go to your master branch
git checkout master
# Now, pull in changes from upstream. Rebase prevents merge commits and conflicts
git pull --rebase upstream master
# Up to date, yeah-unicorns everywhere!
```

We recommend to do this everytime you are about to start a new feature branch.

The full guide is available at http://fedext.net/overview/contributing/contribution-guide.html

Last words: welcome to the growing list of contributors! :)

[contributionGuide]: https://github.com/FluidTYPO3/flux.git "FluidTYPO3 contribution guide"
[pomodoro]: http://www.pomodorotechnique.com/ "The Pomodory Technique"
