# TIL Node things

## brew does not install Node LTS

`brew install node@6` does not install the actual LTS version of Node 6 (uses npm 5.0.3 instead of 3.10.10), use [nvm](https://github.com/creationix/nvm) instead

```
$ nvm install --lts=boron
Installing with latest version of LTS line: boron
Downloading and installing node v6.11.1...
Downloading https://nodejs.org/dist/v6.11.1/node-v6.11.1-darwin-x64.tar.gz...
######################################################################## 100.0%
Computing checksum with shasum -a 256
Checksums matched!
Now using node v6.11.1 (npm v3.10.10)
```

