# repeaterts-docgen
This is the application that generates the `docs.json` files for our organization. While it is replica of [discord.js-docgen](https://github.com/discordjs/docgen). We froze the repo that way breaking changes don't happen. We also implemented some fixes that may be more stable than the current docgen.

---
# Example Github Action Deployment.

Create a orphan branch:
```sh
git checkout --orphan docs
touch README.md
git add .
git commit -m "init"
git push
```

Make sure the following is in your package.json. During build, `npm run docs` will be called.

`package.json`:
```json
    "scripts": {
        "docs": "docgen --source src --custom docs/index.yml --output docs/docs.json",
        "docs:test": "docgen --source src --custom docs/index.yml"
    },
    "devDependencies": {
        "repeaterts-docgen": "github:repeaterts/docgen"
    }
```

`.github/workflows/test.yml`:
```yml
name: Deployment
on:
  push:
    branches:
      - '*'
      - '!docs'
    tags:
      - '*'
jobs:
  docs:
    name: Documentation
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@master

      - name: Install Node v14
        uses: actions/setup-node@master
        with:
          node-version: 14

      - name: Install dependencies
        run: npm ci

      - name: Build and deploy documentation
        uses: discord.js/action-docs@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUBTOKEN }}

```