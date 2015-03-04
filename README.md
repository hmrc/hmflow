# hmflow

A simple git branching model for peer reviewed code.

```
feature B     *-----*
feature A  *-/---*   \
          / /     \   \   <=========== Peer review
master >-*-*--+----*---*--------*-------->
              |\               /
         release\ 1.0         / <===== Peer review
                 \           /
hotfix 1.0        *---------+
                            |
                       release 1.1 (hotfix for 1.0)
```

We required a branching model that allowed for releases, hotfixes to releases and peer reviews (currently via github pull requests but really any central repository would do).

git-flow was close but has too many branches for our taste and no peer review built into the model and so we came up with git-hmflow.

* Master is the development branch and the only permanent branch.
* Feature branches are branched off master to feature/\<feature name\>, worked on and peer reviewed before being merged back into master.
* Releases are tagged as release/\<release name\> on master.
* Hotfixes are
  * branched off release tags to hotfix/\<release name\> and worked on
  * peer reviewed before being merged back into master
  * a release is then taged as release/\<new release name\> on hotfix/\<release name\>

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

A hotfixe MUST be branched from the release tag being hotfixed.

Code merged into master branch MUST be peer reviewed.

The hotfix MUST be merged to master before the release is taged and any fixed merge conflicts MUST be peer reviewed.

Branch hotfix/\<release name\> from release/\<release name\>:
```
git checkout -b hotfix/<release name> release/<release name>
git push -u origin hotfix/<release name>
```

Once your hotfix is commited push to the remote repository and create a pull request from hotfix/<release name> into master

Once merged, tag the new release:
```
git tag -a -m "release <new release name>" release/<new release name>
git push --tags
```
