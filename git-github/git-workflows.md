# Git Workflows

Code branching strategy,

1- GitFlow

2- GitHub Flow

3- Trunk-Based Development

## GitFLow
Have multiple long lived branches in the project, each one serving specific purpose. For example, 
 
 `main` -> production ready code
 
 `develop` -> work in progress, reflects development and is mainly used for integration
 
 `feature`  -> feature branches for new work, every feature branch is used to build a new feature in isolation, then merged back into develop when complete (never directly into main).
 
 `release` -> preparing deployments   
 
 `hotfix` -> created directly from main to quickly fix a production issue. After the fix, its merged back into main and also into `develop` to keep future releases aligned.

 ### Real-world Scenario
 For example, a Mobile application where new version of the app are released. For example, `v2.3`, `v2.4`, and `v3.0`.
 Let say there is a defect in `v2.3` which need to be fixed.
with GitFlow, you need to create a hotfix branch from `v2.3`, made the fix and merge the fix into `v2.3` and `develop`.

### Where does GitFlow shines?
- Software that ships in versions (traditional way)
- Multiple versions of Software are running simultaneously
- Large teams needs clear structure
- Changes needs approval

### Gitflow Downside
- Slows down your development cycle, because one fix will have to go into multiple branches
- Doesn't play well with continuous delivery
- Not suitable for web apps that deploy continuously


## GitHub Flow
The core principle of GitHub Flow is:
Anything in the main branch should always be deployable.

- Creata a branch from main 
- Make the changes in the branch
- Open a pull request, get it reviewed
- And merged it back to main
- Deploy it immediately

### When does GitHub Flow shines?
- Web applications that deploy constantly, like SAAS products, APIs, Web Services, etc. Anything where there is only one version in production at a time.
- Team is Small to medium in size
- Solid automated testing and CI/CD pipelines, when main is always deployable, you need confidence that when ever new code changes get merged into main, which happen daily in this case, any problems or issues will be caught by your extensive automated tests.
- When you want to ship features instead of managing branches

### GitHub Flow's Downsides
- Doesn't handle multiple versions
- Demands discipline, and this is where the culture of your team and proper guidelines are really important. If someone merges bad code in main and main gets broken, your entire deployment pipeline stops. And since main is always supposed to be deployable, this is a big problem.
- Struggles when multiple teams are working on the same codebase.

## Trunk-Based development
Integrate Continuously, means it directly ties to one DevOps principles of CI / CD.

So, if multiple features are being worked upon same time and merged in the `main` then it means the work is in progress and half done feature are merged in the `main`.
This is where `fetaure-flags` are used, which is a switch that turns on or off parts of the code that are still in progress.

- No code conflicts when new features are merged, because these features are already being merged in small chunks into main.

### When does TBD shines  
- your team consists mainly of experience developers
- bulletproof automated testing
- Rapid integration and faster deployment 
- Shipping SaaS products with one version in production

### TBD's Challanges 
- Doesn't handle multiple versions
- Test coverage is weak


Most teams don't follow a standard workflow.
For example, they use GitHub Flow + Trunk-Based Development.

### Practical Tips
- Automate everything you can: 
    - Automated tests
    - Automated CI/CD pipelines
    - Linting
    - Code formatting
    - Automated security scanning and testing

- Document you process
    - Branching Strategy
    - Commit message conventions
    - Code review expectations
    - Pull request workflow details

- Iterate 
    - Your workflow should evolve as your team and product evolves. So don't be afraid to change it, if its not working anymore.
