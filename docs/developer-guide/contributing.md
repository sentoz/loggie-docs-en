# Contribution

!!! note

    If you:
    
    - have new ideas
    - sumbmit feature
    - report/fix bug
    - contribute documentation
    - help wanted
    
    It is recommended to raise [Issues](https://github.com/loggie-io/loggie/issues) first, and describe your purpose or problem.

## Code/Documentation Contribution Process

### 1. Create Local Project
#### Fork Project
Visit [https://github.com/loggie-io/loggie](https://github.com/loggie-io/loggie), click Fork in the upper right corner, and fork it to your own GitHub repository.

#### Clone to Local
Clone the forked project to the local:
```bash
cd ${go-workspace}

git clone git@github.com:${yourAccountId}/loggie.git
```

### 2. Local development

#### Add Upstream
In local project, add upstream remote:

```bash
git remote add upstream https://github.com/loggie-io/loggie.git
git remote set-url --push upstream no_push
```

You can use `git remote -v` to check. Origin is the git address of your personal account fork, upstream is the official address of Loggie. Uptream is only used for local synchronization code (normally you do not have push permission).


#### Create Branch
Create your branch based on the latest main branch, usually starting with feat/fix/chore etc.

```bash
git checkout -b feat-mywork
```

After development, it is recommended to keep the code consistent with the upstream before submitting:

```bash
git pull upstream -r
```

#### Submit Code
```bash
git commit

git push origin feat-mywork
```

!!! caution "Commit Specification"
    Usually we use `<type>(<scope>): <subject>`:

    **`<type>:`**(necessary)

    - feat: represents a new feature
    - fix: bug fix
    - chore: changes to the build process or assistant tools
    - test: test related 
    - docs: documentation mofitied
    - perf: performance optimization related changes
    - refactor: code refactoring

    **`<scope>:`**(optional)

    - core: Changes of the Loggie core code framework
    - source: Some functions of the Loggie source have been added or changed
    - sink: Some functions of the logie sink are added or changed
    - interceptor: Some functions of the Logie interceptor have been added or changed.
    - discovery: Service discovery and configuration
    - monitor: monitor eventbus

    **`<subject>:`** (required)the description of the commit


### 3. Create PR

#### Submit to GitHub
Visit the Loggie project forked in your personal GitHub account, and `Open pull request`。  
Try to describe the PR's background, purpose, and changes as clearly as possible.
If there are related Issues, they need to be associated.

Please check before submitting:

- [ ] Do you need to add/modify relevant unit test/e2e/benchmark
- [ ] Whether related documents need to be added/modified

#### Seek for Merge
You can find someone in OWNERS, or by default add the loggie-robot account as Reviewers or Assignees of your PR.