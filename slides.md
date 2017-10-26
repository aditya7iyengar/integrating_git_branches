---
title: Integrating Git Branches
author: Adi Iyengar
patat:
    incrementalLists: true
    theme:
        emph: [vividWhite, onVividBlack, bold]
        imageTarget: [onDullWhite, vividRed]
...

# I GIT IT!
```
                         :':''''
                        '''''''''
                       '''''''''''
                      '''''''''''''
                     '''''''''''''''
                    :''''''''''''''''
                     :''''''''''''''''         MERGE vs REBASE     >>>
                      :''''''''''''''''
                 ',    :''''''''''''''''
                ''',    :''''''''''''''''
               ''''',    `,:''''''''''''''
              ''''''',       :'''''''''''''
             ''''''''',       ''''''''''''''
            '''''''''''       :''''''''''''''
           ''''''''''''       .'''''''''''''''
          '''''''''''''       .''''''''''''''''
         ''''''''''''''.       :''''''''''''''''
        ''''''''''''''''.       :''''''''''''''''
       ''''''''''''''''''   `    :''''''''''''''''
      '''''''''''''''''''   .,    :''''''''''''''''
     ''''''''''''''''''''   .',    ,''''''''''''''''
    '''''''''''''''''''''   .'',       :'''''''''''''
   ''''''''''''''''''''''   .''',       ''''''''''''''
  ,''''''''''''''''''''''   .''''       .''''''''''''':
  :''''''''''''''''''''''   .''''        '''''''''''''
   ''''''''''''''''''''''   .''''       .'''''''''`''
   .'''''''''''''''''''''   ,''''.      ''''''''':''
    ,''''''''''''''''''''   ,'''''`    :'''''''':''
     ,'''''''''''''''''''   ,'''''''''''''''''':''
      ,''''''''''''''''''   ,''''''''''''''''':''
       ,'''''''''''''''''   ,'''''''''''''''':''
        ,'''''''''''''''`    ''''''''''''''':''
         ,'''''''''''''`      ''''''''''''':''
          ,''''''''''''       :''''''''''':''
           ,'''''''''''       .'''''''''':''
            ,''''''''''       :''''''''':''
             ,'''''''''       ''''''''':''
              ,'''''''''     :'''''''':''
               ,''''''''':,:''''''''':''
                ,''''''''''''''''''':''
                 ,''''''''''''''''':''
                  ,''''''''''''''':''
                   ,''''''''''''':''
                    ,''''''''''':''
                     ,''''''''':''
                      ,''''''':''
                       ,''''':''
                        .''':''

```

# RESOURCES

- Books:
  > Git for Teams by Emma Westby
  > Git in Practice by Mike McQuaid
  > Learn Git in a Month of Lunches by Rick Umali

- Other:
  > https://git-scm.com/
  > https://gist.github.com/pandeiro/1552496


# TOPICS

- Basics of Merging and Rebasing
- Differences between them
- Problems with Merging & Rebasing
- Things to keep in mind while rebasing
- When to use `git merge` vs `git rebase` (basics)
- When to use `git merge` vs `git rebase` (using Adi's rules for rebasing)
- Advanced rebasing


# WHAT ARE THESE COMMANDS ABOUT?

`git merge` and `git rebase` can both be used for the same purpose:

_Integrating changes from one branch to another_


# MERGE

Merge adds a new connection to a Git Graph.

- A Feature ready to be merged:

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)
                   \
feature ->         (f1)--(f2)
```


- Checkout feature, merge master into feature
    `$ git checkout feature`
    `$ git merge master`

    or

    `$ git merge master feature`


- Git history after merging master into feature

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)
                   \                 \
feature ->         (f1)--(f2)--------(f3 Merge branch master)
```


- Merge feature branch (through PR) -- __3-way Merge__

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)----(m6 Merge pull request # from feature)
                   \                 \    /
                   (f1)--(f2)---------(f3 Merge branch master)
```


# REBASE

Rebase brings feature branches changes to the base branch by creating new
commits that contain those changes, but start from base branch's current state.

- A Feature ready to be merged:

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)
                   \
feature ->         (f1)--(f2)
```


- Checkout feature and rebase master
`$ git checkout feature`
`$ git rebase master`


- Git history after rebasing

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)
                                     \
feature ->                           (f3)--(f4)
```


- Merge feature branch (through PR) -- __Fast-Forward Merge__

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)--(f3)--(f4)
```

- _Rebased_ the commits `f1` and `f2` from `m2` to `m5`


# COMPARE GIT HISTORIES

- Git history after Merge:

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)----(m6 Merge pull request #1 from f)--(m7)---(m8 Merge pull request #2 from g)
                 \  \                 \    /                                   \                /
                  \ (f1)--(f2)---------(f3 Merge branch master)                 \              /
                   \                                                             \            /
                   (g1)------(g2)--(g3)---(g4)-----------------------------------(g5 Merge branch master)
```

- Git history after Rebase:

```
master -> (m1)--(m2)--(m3)--(m4)--(m5)--(f3)--(f4)--(m6)--(g5)--(g6)--(g7)--(g8)
```


# MERGE vs REBASE

| Merge                                      | Rebase                                |
|:-------------------------------------------|:--------------------------------------|
| Preserves Git History                      | Doesn't preserve git history          |
| Has extra commits for merging              | Doesn't have extra commits            |
| Increases connectivity of Git Graph by one | Keeps the Graph linear                |
| Non-Destructive Operation                  | Destructive Operation                 |


# PROBLEMS WITH MERGING

- Complicated Git Graph, which makes it extremely hard for developers to
follow if the `develop` or `master` branch is very active.

- Hard to debug with the complicated history.

- To see when a feature's commits were applicable, you have to go through the
history and find the merge commit associated with the feature branch.

- Checking out a commit might not give us the actual state of the `develop`
branch.

- Sometimes commit history is messy and could use a re-write.


# PROBLEMS WITH REBASING

- Less complicated git graph at the expense of safety and traceability.

- Rebase modifies git history, which is only recommended as long as this history
is local (or unique to the user rebasing). So don't rebase published commits.

- Rebasing loses the context provided by a merge commit, which is when upstream
changes were explicitly incorporated into the feature.

- Problems with deploy-tag approach while deploying a feature branch. Tags could
be misleading


# THINGS TO KEEP IN MIND WHILE REBASING

- Golden Rule: Unpublished (or local) commits are rebasable.
__I don't completely agree with it.__ (We will get back to this)
```
master -> (m1)--(m2)--(m3)--(m4)--(m5)
                   \
feature ->         (f1)--(f2)
```

    - Checkout master and rebase feature
        `$ git checkout feature`
        `$ git rebase master`


    - Git history after rebasing

    ```
    master -> (m1)--(m2)       (m6)--(m7)--(m8)
                       \       /
    feature ->         (f1)--(f2)
    ```

    - Difference in master forks:

    ```
    Everybody else's master             Our master

    (m1)--(m2)--(m3)--(m4)--(m5)    (m1)--(m2)--(f1)--(f2)--(m6)--(m7)--(m8)

    ```


- Commits that are eligible for rebasing can be displayed by:
`$ git log origin/master..master`

- Even though rebased commits may introduce same textual difference as original
commits, the git-state it represents is completely new. So there's no guarantee
that it will behave the same as the original commits.

- If you try to push a rebased `published` branch, git will prevent you from doing so.
`git push --force`
__Use this only for backup purposes for rare cleanups__


# WHEN TO USE `git rebase` vs `git merge` (The easy stuff)

_Rebases are how changes should pass from the top of hierarchy downwards and merges are how they flow back upwards_

- When pulling changes from `origin/base` onto your local `base` use rebase.
`$ git checkout master`
`$ git pull --rebase`
or
`git rebase origin/master master`

- When finishing a feature branch merge the changes back to `base`,
through `git merge`.


# WHEN TO USE `git rebase` vs `git merge` (The not so easy stuff)

- Adi's First Rule for rebasing:
__Rebase commits only when the commits are leaves.__
Rebasing parent commits to new commits, means those commit hashes don't exist.
Thankfully git works in a way that preserves parent information, but it rebasing
non-leaf commits really have a bad effect of the git history (whether they are
local or published)

- Adi's Second Rule for rebasing:
__Don't Rebase commits to current state of an integration if the integration has
two conflicting big features.__
Merge Conflicts Resolution do not quite work the same as Resolutions are recorded
explicitly for `git pull` and `git merge` vs `git pull --rebase` and `git rebase`.
So, if you find yourself rebasing again and again into an integration branch, it
might be worthwhile to use merge for heavily conflicted work.

- Adi's Third Rule for rebasing:
__NEVER rebase two features that have different parents__
This is quite obvious.. You shouldn't be integrating features anyway.

- Adi's Fourth Rule for rebasing:
__Don't rebase two features with same parents _directly___
This one isn't that obvious, but if two features, published or not, depend on a
parent's state, rebasing either of them to a new commit seems no different than
merging, besides when you run into a problem where one feature introduces a bug
into another, and you're trying to revert commits that might have cause the bug.


# INTERACTIVE REBASING

- To use interactive rebasing, use `-i` option.
`$ git checkout feature`
`$ git rebase -i master`

- Interative Rebasing allows us to alter commits as they are applied to the
feature branch

- This offers the user, complete control over branch's commit history. This
can be used to clean up messy history.

# SUMMARY

- Keep Adi's rules in mind and you __should__ be fine (maybe). ;)

- If you're not entirely confident how the git history will look like after
a rebase, you can always mimic it in a temporary branch. In that way, if you
accidentaly mess up something, you can checkout the original branch:
```
git checkout feature
git checkout -b temporary-branch
git rebase -i master
# [Clean up the history]
git checkout master
git merge temporary-branch
```

- Whenever you're too scared to `rebase` for whatever reason, don't rebase! :D

# QUESTIONS?
