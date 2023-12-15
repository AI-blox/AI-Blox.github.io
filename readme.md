# Introduction

## Installing Jekyll and Ruby

Install procedure for different platforms
* [Ubuntu](https://jekyllrb.com/docs/installation/ubuntu/)
* [Windows](https://jekyllrb.com/docs/installation/windows/)
* [MacOS](https://jekyllrb.com/docs/installation/macos/)


After the above mentioned install procedure, you will need to install all the required Ruby Gems

```
$ bundle install
```

## Run locally

You can run jekyll locally with following command:

```bash
$ bundle exec jekyll serve
```

If you encounter " `require': cannot load such file -- webrick (LoadError)"
You will need to add webrick to the bundle

```bash
$ bundle add webrick
```


## Publish on github pages



Push the changes to the ai-blox.github.io site.

```bash
$ git add .
$ git commit -m 'my messages'
$ git push
```

And check if the deployment went successful: https://github.com/ai-blox/ai-blox.github.io/deployments


