# Working with the theme submodule

`themes/winston` is a git submodule pointing at [izabelacborges/adapted-hugo-winston](https://github.com/izabelacborges/adapted-hugo-winston). It is the author's own fork, so changing it is allowed. A theme change is a **two-repo change**: the theme repo gets the commit, and this repo records which theme commit to use.

Prefer not to touch the theme for colours. Those live in `assets/scss/style.scss` here. Touch the theme only for markup or behaviour.

## Hard rule: point at `master`, never a branch

The pointer committed in this repo must always be a commit on `master` of the theme fork. `.gitmodules` sets `branch = master` so `git submodule update --remote` follows it. Use theme branches only for local tests, and merge into the fork's `master` before committing the pointer. Check with:

```bash
git -C themes/winston branch -r --contains $(git -C themes/winston rev-parse HEAD)   # must list origin/master
```

## Changing the theme

1. Enter the theme and switch to its branch. A submodule normally sits on a detached HEAD, so a commit made there would belong to no branch.
   ```bash
   cd themes/winston && git switch master
   ```
2. Make and check your changes, then commit and push the **theme first**:
   ```bash
   git add -A && git commit -m "Describe the change" && git push origin master
   ```
3. Back in this repo, the pointer shows as modified. Commit it with the related changes:
   ```bash
   cd ../.. && git add themes/winston && git commit -m "Update theme submodule"
   ```
4. Push this repo.

Always push the theme before this repo. Otherwise the pointer references a theme commit that isn't on GitHub, and the deploy fails when it fetches the submodule.

## Pulling theme changes

```bash
git pull
git submodule update --init --recursive
```

To take the newest `master` of the theme instead of the recorded commit:

```bash
git submodule update --remote themes/winston
```

Then commit the new pointer.

## Common problems

- **`git status` shows `m themes/winston`.** The theme has uncommitted changes. Commit them in the theme (steps 1 and 2).
- **`git status` shows `modified: themes/winston (new commits)`.** The theme moved ahead. Commit the pointer (step 3).
- **The theme folder is empty.** Run `git submodule update --init --recursive`.
- **Detached HEAD in the theme.** Normal after `submodule update`. Run `git switch master` before committing.
- **CI can't find the theme.** The pointer references an unpushed commit, or the workflow checkout lacks `submodules: recursive` (it currently has it).
