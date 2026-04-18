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

