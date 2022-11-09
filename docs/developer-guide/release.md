# Version and Release Process

## Project Involved
- Loggie project
- Loggie-installation deployment script project
- Loggie docs document project (Temporarily the latest version of the document. Multiple versions will be supported in the future.)

## Branching strategy
- Version Reference [Semantic](https://semver.org/).
- Use the main branch as development branch, and all PRs are merged into main branch.
- New version uses the release branch. Format should be `release-vA.B`, such as`release-v1.1`. Only the first two digits of the version are used here.
- Tag a specific version based on the release branch, for example `v1.1.0`.
- Bugfix will definitely be merged into the main branch, and additionally cherry-pick-ed to the specific release branch according to the serious situation, and tagged at the same time. Minimum version needs to be upgraded, for example `v1.1.1`.

## Steps of Publishing New Version

#### 1. Create a new release branch

Confirm that all the features required by the version have been merged, and the main branch is the latest commit:

``` bash
git checkout main
git pull upstream main -r
```

Create a new release branch based on the main branch, for example `release-v1.1`:
```bash
git checkout -b release-v${A.B}
```

#### 2. Fill in CHANGELOG
Fill in CHANGELOG based on this branch and submit it to the project:

```bash
git add .
git commit -m'Release: add v${A.B.C} changelog'
git push upstream release-v${A.B.C}
```

#### 3. Release Loggie-installation
The steps of releasing Loggie-installation deployment script project is the same as the above steps 1 and 2.

Note:

- Don't forget to modify the version number in Chart.yaml.

#### 4. Regression test
Perform regression test based on the release branch.
After the release branch is pushed to Github, the action will be triggered to build the image. And the image will be used for regression testing. 
The test here will use the corresponding release branch in Loggie-installation, and regression test will verify the correctness of the deployment script.
If the test finds a bug, submit a PR and merge it into the release branch, and re-run the regression test.  

#### 5. Tag the version

After tests pass, tag the corresponding version based on the release branch, such as `v1.1.0`
```bash
git pull upstream release-v${A.B.C}
git tag v${A.B.C}
git push v${A.B.C}
```
Note that both Loggie and Loggie-installation require to be tagged.

#### 6. Publish the Release on GitHub

- Loggie has used github action to automatically build and publish release artifacts.
- Loggie installation needs to provide `loggie-v${A.B.C}.tgz` helm chart package.
- Based on the Loggie-installation of the corresponding version execute `helm package ./helm-chart`.

#### 7. Update Loggie Docs

- Update Loggie docs and submit a PR.
- Modify the Loggie image and version number, etc.


#### 8. Merge the Release Branch into Main
Submit PR to merge the release branch into the main. You can wait the release for a period of time to be stabilized before merging. Note that both Loggie project and Loggie-installation are required.


## When There Is A BugFix

- If it is an unimportant bug, you can submit the modification based on the main branch. Merge into the release branch is not necessary.
- If it is an important bug that needs to be fixed, determine the release branch of the version that needs to be fixed (it can be one or more). In addition to submitting modification to the main branch, you also need to cherry-pick it to the specified release branch, and tag it at the same time (adding the minimum The version number). Finally you need to confirm whether Loggie-installation and docs need to be modified synchronously.