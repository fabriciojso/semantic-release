# Semantic Release
Geração de versões com changelog de forma automatizada.

*IMPORTANTE*: Caso seu projeto esteja sem versionamento, coloque a versão no package.json como: *0.0.0*

## Como configurar um projeto

### Dependências

* Instale como dependencias de **desenvolvimento** (_devDependencies_) 
```json
npm install @commitlint/cli -D
npm install @commitlint/config-conventional -D
npm install @semantic-release/changelog -D
npm install @semantic-release/git -D
npm install @semantic-release/github -D
npm install cz-conventional-changelog -D
npm install semantic-release -D
npm install husky -D
```

### Configurando o semantic release
Inclua os seguintes códigos no final do seu **package.json**:
```json
"release": {
    "prepare": [
      "@semantic-release/changelog",
      "@semantic-release/npm",
      {
        "path": "@semantic-release/git",
        "assets": [
          "package.json",
          "package-lock.lock",
          "CHANGELOG.md"
        ],
        "message": "chore(release): ${nextRelease.version} [skip ci]\n\n${nextRelease.notes}"
      }
    ],
    "publish": [
      "@semantic-release/github"
    ],
    "plugins": [
      "@semantic-release/commit-analyzer",
      "@semantic-release/release-notes-generator",
      "@semantic-release/github"
    ]
  }
  ```
  
### Configurando o commitlint
Para evitar que você faça commit de forma incorreta, é necessário instalar o linter de commit no seu projeto.
Para isso inclua os seguintes códigos no seu *package.json*:
  
**Husky**
```json
 "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
```

**Convenção de commit**
```json
"config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  }
```

### Configurando o Circle CI

Inclua o seguinte job:
```yaml
semantic-release:
    docker:
      - image: circleci/node:latest
    working_directory: ~/magamais-hurley
    steps:
      - checkout
      - restore_cache:
          keys:
            - v1-dependencies-{{ checksum "package.json" }}
            - v1-dependencies-
      - run:
          name: Install dependencies
          command: npm install
      - save_cache:
          paths:
            - node_modules
          key: v1-dependencies-{{ checksum "package.json" }}
      - run: npm run semantic-release
```
      
Depois incluia no seus **workflows** o seguinte flow:
```yaml
- semantic-release:
  filters:
    branches:
      only: master
  requires:
    - build-test
```

Instale o Commitizen para facilitar os seus commits:
`npm install -g commitizen`
