---
title: Integrating Git Branches
author: Adi Iyengar
patat:
    incrementalLists: true
    theme:
        emph: [vividWhite, onVividBlack, bold]
        imageTarget: [onDullWhite, vividRed]
...

# WE LOVE GIT!
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
- Things to keep in mind while rebasing
- When to use `git merge` vs `git rebase`
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


- Commits that are eligible for rebasing can be displayed by:
`$ git log origin/master..master`

- Even though rebased commits may introduce same textual difference as original
commits, the git-state it represents is completely new. So there's no guarantee
that it will behave the same as the original commits.


# WHEN TO USE `git rebase` vs `git merge`

_Rebases are how changes should pass from the top of hierarchy downwards and merges are how they flow back upwards_

- When pulling changes from `origin/base` onto your local `base` use rebase.
`$ git checkout master`
`$ git pull --rebase`

- When finishing a feature branch merge the changes back to `base`,
through fast-forward merge.


# INTERACTIVE REBASING

- To use interactive rebasing, use `-i` option.
`$ git checkout feature`
`$ git rebase -i master`

- Interative Rebasing allows us to alter commits as they are applied to the
feature branch

- This offers the user, complete control over branch's commit history. This
can be used to clean up messy history.

