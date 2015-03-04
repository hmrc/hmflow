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
  * worked on and peer reviewed before being merged back into hotfix-master/\<release name\>
  * a release is taged as release/\<new release name\> on hotfix-master/\<release name\> but not pushed
  * hotfix-master/\<release name\> is merged back into master. Note this code has already been peer reviewed and so an additional peer review is only necessary if there is a merge conflict.
  * the updated master and new release tag is pushed to the remote repository

## Working on features

Feature branches SHOULD branch off master and MUST be pull requested back into master.

Feature branches SHOULD be named feature/\<feature name\>. Really they can be named anything but they MUST NOT have a prefix of hotfix/ or release/.

Branch off master to start working on your feature:
```
git checkout -b feature/<feature name> master
git push -u origin feature/<feature name>
```

When your work is done push them to the remote git repository and submit a pull request from your branch into master for peer review.

At this point it's safe to delete your feature branch and pull the latest master.

## Creating a release

Releases MUST be tagged on either master or a hotfix-master branch.

Tag a new release on the HEAD of master and push:
```
git tag release/<release name> master
git push --tags
```

You can also target a certain commit for the release tag:
```
git tag release/<release name> <commit id>
```

## Hotfixing a released bug

Hotfixes MUST be branched from the release tag being hotfixed.

Code merged into a hotfix-master branch MUST be peer reviewed.

hotfix-master MUST be merged to master before the release is taged and any fixed merge conflicts MUST be peer reviewed.

Branch hotfix-master/\<release name\> from release/\<release name\> and branch hotfix/\<release name\> from hotfix-master/\<release name\>:
```
git checkout -b hotfix-master/<release name> release/<release name>
git push -u origin hotfix-master/<release name>
git checkout -b hotfix/<release name> hotfix-master/<release name>
git push -u origin hotfix/<release name>
```

Once your hotfix is commited push to the remote repository and create a pull request from hotfix/<release name> into hotfix-master/<release name>.

Once that pull request is merged pull the updated hotfix-master/<release name>:
```
git checkout hotfix-master/<release name>
git pull
```

Tag the new release and then merge to master, peer reviewing any resolved merge conflicts, then push master and the release tag:
```
git tag -a -m "release <new release name>" release/<new release name>
git checkout master
git merge --no-ff hotfix-master/<release name>
git push --tags origin master
```
