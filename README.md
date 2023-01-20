# chenjun305.github.io
my personal site including my tech blog

## Setup
```
bundle install
bundle exec jekyll serve
```

## Auto Deploy
using github actions to auto deploy this site.
need to change the repository settings:

* Settings -> Actions -> Workflow permissions -> Read and write permissions
* Settings -> Pages -> Build and deployment -> Source: Deploy from a branch -> Branch: gh-pages


check the deploy status in the reposity`s Actions tab.

the deployment script written in .github/workflows/deploy.yml & bin/deploy