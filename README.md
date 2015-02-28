# hmflow

A simple git branching model for peer reviewed code.

```
feature B     *-----*
feature A  *-/---*   \
          / /     \   \   <=========== Peer review
master >-*-*--+----*---*--------*-------->
              |\               /
         release\ 1.0         /
                 \           /
hotfix-master     *---------+
                   \       /|   <===== Peer review
                    \ rele/ase 1.1 (hotfix for 1.0)
hotfix               *---*
```

We required a branching model that allowed for releases, hotfixes to releases and peer reviews (currently via github pull requests but really any central repository would do).

git-flow was close but has too many branches for our taste and no peer review built into the model and so we came up with git-hmflow.

* Master is the development branch and the only permanent branch.
* Feature branches are branched off master to feature/\<feature name\>, worked on and peer reviewed before being merged back into master.
* Releases are tagged as release/\<release name\> on master.
* Hotfixes are
  * branched from release tags to hotfix-master/\<release name\>
  * branched from hotfix-master/\<release name\> to hotfix/\<release name\>
  * worked on and peer reviewed before being merged back into hotfix-master/\<release name\> where a hotfix release is tagged as release/\<new release name\>
  * hotfix-master/\<release name\> is merged back into master. Note this code has already been peer reviewed and so an additional peer review is only necessary if there is a merge conflict.


# `git hmflow feature <command> [options]`

* `git hmrcflow feature [list]`
* `git hmrcflow feature start <feature-name>`
* `git hmrcflow feature push`
* `git hmrcflow feature checkout <feature-name>`
* `git hmrcflow feature cancel`
* `git hmrcflow feature finish`

# `git hmflow release <command> [options]`

* `git hmrcflow release [list]`
* `git hmrcflow release create <release-name>`
* `git hmrcflow release push [<release-name>]`
* `git hmrcflow release create-push <release-name>`

# `git hmflow hotfix <command> [options]`

* `git hmrcflow hotfix [list]`
* `git hmrcflow hotfix start <release>`
* `git hmrcflow hotfix push`
* `git hmrcflow hotfix checkout <hotfix-name>`
* `git hmrcflow hotfix cancel`
* `git hmrcflow hotfix pull`
* `git hmrcflow hotfix release <new-release>`
* `git hmrcflow hotfix finish`
