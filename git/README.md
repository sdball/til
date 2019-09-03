# TIL git

## Force `git pull` to do nothing

```bash
git() {
  if [[ "$*" =~ "pull" ]]; then
    echo "Don't git pull."
  else
    command git $*
  fi
}
```

## Handle weird line endings on clone

Some repos have weird things about line endings and a fresh clone will say it has DIFFs. That's because line endings and Git are complicated: https://adaptivepatchwork.com/2012/03/01/mind-the-end-of-your-line/

Here's some basic steps that should clean up the clone but leave everything unchanged:

- git clone the repo
- cd into the repo
- `echo "* native" > .gitattributes`
- `git checkout .`

That should leave you with a "clean" repo.
