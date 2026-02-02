# Intro

# git-pkgs

A git subcommand that indexes your dependency history into a local SQLite database, then lets you query it like git history.

```terminal
$ brew tap git-pkgs/git-pkgs && brew install git-pkgs

$ go install github.com/git-pkgs/git-pkgs@latest

$ git pkgs --version
git-pkgs version 0.11.4
         commit faefea598fe88271224c668ca0b46716bbaf2dc7
         date 2026-01-31T21:25:27Z
```
---

```terminal
$ git pkgs help

Available Commands:
  add         Add a dependency
  bisect      Find the commit that introduced a dependency-related change
  blame       Show who added each dependency
  browse      Open installed package source in editor
  diff        Compare dependencies between commits or working tree
  ecosystems  List supported ecosystems
  history     Show history of dependency changes
  info        Show database information
  init        Initialize git-pkgs database for this repository
  licenses    Show license information for dependencies
  list        List dependencies at a commit
  log         List commits with dependency changes
  outdated    Find packages with newer versions available
  sbom        Generate Software Bill of Materials
  search      Find dependencies matching a pattern
  show        Show dependency changes in a commit
  stats       Show dependency statistics
  tree        Display dependencies as a tree
  vulns       Vulnerability scanning commands
  where       Find where a package is declared
  why         Show why a dependency was added
```

---

```terminal
$ git pkgs ecosystems
Ecosystem   Manifest         Lockfiles                          Managers        Registry
cargo       Cargo.toml       Cargo.lock                         cargo           yes
composer    composer.json    composer.lock                      composer        yes
gem         Gemfile          Gemfile.lock                       bundler         yes
hex         mix.exs          mix.lock                           mix             yes
npm         package.json     package-lock.json, yarn.lock...    npm, yarn...    yes
pypi        pyproject.toml   uv.lock, poetry.lock               uv, poetry      yes
pub         pubspec.yaml     pubspec.lock                       pub             yes
cocoapods   Podfile          Podfile.lock                       cocoapods       yes
maven       pom.xml          gradle.lockfile                    maven, gradle   yes
nuget       packages.config  packages.lock.json                 nuget           yes
cpan        cpanfile         cpanfile.snapshot                  cpanm           yes
crystal     shard.yml        shard.lock                         shards          no
swift       Package.swift    Package.resolved                   swift           no
nix         flake.nix        flake.lock                         nix             no
cran        DESCRIPTION      renv.lock                          renv            yes
julia       Project.toml     Manifest.toml                      pkg             yes
golang      go.mod                                              gomod           yes
deno        deno.json        deno.lock                          deno            yes
conda       environment.yml                                     conda           yes
docker                                                                          no
...
```

---

```terminal
$ git pkgs init && git pkgs info
Database Info
========================================

Location: /Users/andrew/code/octobox/.git/pkgs.sqlite3
Size: 10.3 MB
Schema version: 7

Branch: master
Last analyzed: 7825978

Row Counts
----------------------------------------
  Branches                        1
  Commits                      5237
  Branch-Commits               5237
  Manifests                       9
  Dependency Changes           4775
  Dependency Snapshots        17932
  ----------------------------------
  Total                       33191

Ecosystems: docker, gem, github-actions
```

---

```terminal
$ git pkgs stats
Branch: master
Commits analyzed: 5237
Commits with changes: 2545

Current Dependencies: 246
  gem: 226, github-actions: 16, docker: 4

Dependency Changes: 4775
  added: 399, modified: 4223, removed: 153

Most Changed Dependencies
  rails: 133, pagy: 116, autoprefixer-rails: 109
  oj: 90, skylight: 87, nokogiri: 85, puma: 74

Top Contributors
  dependabot[bot]: 2816 changes
  Andrew Nesbitt: 1282 changes
  dependabot-preview[bot]: 467 changes
  Mark Tareshawty: 64 changes
```

---

```terminal
$ git pkgs list --ecosystem=gem
Gemfile (gem):
  active_record_query_trace [development]
  attr_encrypted https://github.com/octobox/attr_encrypted.git
  benchmark
  better_errors [development]
  bootsnap
  bootstrap 4.6.2
  brakeman [development]
  bugsnag
  commonmarker 2.6.2
  dotenv-rails [development]
  factory_bot [test]
  faraday >= 2
  faraday-gzip
  faraday-retry
  gemoji <4
  jbuilder
  jquery-rails
  jwt
  listen [development]
  local_time 2.1.0
  ...
```

---

```terminal
$ git pkgs tree --ecosystem=gem
Gemfile (gem)
├── development
│   ├── active_record_query_trace
│   ├── better_errors
│   ├── brakeman
│   ├── dotenv-rails
│   ├── listen
│   ├── rails-controller-testing
│   ├── spring
│   ├── sql_queries_count
│   └── web-console
├── runtime
│   ├── attr_encrypted https://github.com/octobox/attr_encrypted.git
│   ├── bootsnap
│   ├── bootstrap 4.6.2
│   ├── bugsnag
│   ├── commonmarker 2.6.2
│   ├── faraday >= 2
│   ├── jbuilder
│   ├── jquery-rails
│   └── ...
└── test
    ├── factory_bot
    ├── mocha
    └── webmock
```

---

```terminal
$ git pkgs search rails
autoprefixer-rails              10.4.21.0  (gem)
  First seen: 2016-12-16  Last changed: 2025-04-14
dotenv-rails                    3.2.0  (gem)
  First seen: 2016-12-16  Last changed: 2025-12-08
jquery-rails                    4.6.1  (gem)
  First seen: 2016-12-16  Last changed: 2025-10-22
omniauth-rails_csrf_protection  2.0.1  (gem)
  First seen: 2021-11-02  Last changed: 2025-12-11
rails                           8.1.2  (gem)
  First seen: 2016-12-16  Last changed: 2026-01-15
rails-controller-testing        1.0.5  (gem)
  First seen: 2016-12-16  Last changed: 2019-02-08
rails-dom-testing               2.3.0  (gem)
  First seen: 2016-12-16  Last changed: 2025-12-11
rails-html-sanitizer            1.6.2  (gem)
  First seen: 2016-12-16  Last changed: 2025-12-11
railties                        8.1.2  (gem)
  First seen: 2016-12-16  Last changed: 2026-01-15
```

---

```terminal
$ git pkgs show e323669
Gemfile.lock:
  ~ active_record_query_trace 1.6.1 -> 1.9
  ~ brakeman 7.1.2 -> 8.0.1
  ~ bugsnag 6.28.0 -> 6.29.0
  ~ prism 1.8.0 -> 1.9.0
  ~ puma 7.1.0 -> 7.2.0
  ~ redis-client 0.26.3 -> 0.26.4
```

---

```terminal
$ git pkgs history rails
History for rails:

2016-12-16 Added 5.0.0.1
  Package: rails (gem)
  Commit: e323669 Hello World
  Author: Andrew Nesbitt <andrewnez@gmail.com>

2016-12-16 Added 6.5.3.1
  Package: autoprefixer-rails (gem)
  Commit: e323669 Hello World
  Author: Andrew Nesbitt <andrewnez@gmail.com>

2016-12-16 Added 2.1.1
  Package: dotenv-rails (gem)
  Commit: e323669 Hello World
  Author: Andrew Nesbitt <andrewnez@gmail.com>

2016-12-16 Added 4.2.1
  Package: jquery-rails (gem)
  Commit: e323669 Hello World
  Author: Andrew Nesbitt <andrewnez@gmail.com>
...
```

---

```terminal
$ git pkgs log
7825978 Ignore .gitattributes
  Author: Andrew Nesbitt <andrewnez@gmail.com>
  Date:   2026-01-31
  Changes: ~6

cb3e017 build(deps): Bump commonmarker from 2.6.1 to 2.6.2
  Author: dependabot[bot]
  Date:   2026-01-20
  Changes: ~2

d8d8666 build(deps): Bump github/codeql-action from 3.31.10 to 4...
  Author: dependabot[bot]
  Date:   2026-01-20
  Changes: ~3

9bd74ac Add zizmor workflow and fix security findings
  Author: Andrew Nesbitt <andrewnez@gmail.com>
  Date:   2026-01-19
  Changes: +2 ~11

f51f866 build(deps-dev): Bump listen from 3.9.0 to 3.10.0
  Author: dependabot[bot]
  Date:   2026-01-19
  Changes: ~1
```

---

```terminal
$ git pkgs why rails
rails was added in commit e323669
Date:     2016-12-16
Author:   Andrew Nesbitt <andrewnez@gmail.com>
Manifest: Gemfile

$ git pkgs where dotenv-rails
Gemfile:48:  gem 'dotenv-rails'
Gemfile.lock:126:    dotenv-rails (3.2.0)

$ git pkgs browse rails
Opening /Users/andrew/.gem/ruby/3.3.0/gems/rails-8.1.2...
```

---

```terminal
$ git pkgs blame
.github/workflows/ci.yml (github-actions):
  actions/checkout                Andrew Nesbitt  2021-04-05  a83996d
  actions/setup-node              Andrew Nesbitt  2021-04-05  a83996d
  docker://postgres               Andrew Nesbitt  2021-04-05  a83996d
  docker://redis                  Andrew Nesbitt  2021-04-05  a83996d
  ruby/setup-ruby                 Andrew Nesbitt  2021-04-05  a83996d

.github/workflows/codeql-analysis.yml (github-actions):
  actions/checkout                Andrew Nesbitt  2021-11-09  464afc8
  github/codeql-action/analyze    Andrew Nesbitt  2021-11-09  464afc8
  github/codeql-action/autobuild  Andrew Nesbitt  2021-11-09  464afc8
  github/codeql-action/init       Andrew Nesbitt  2021-11-09  464afc8

.github/workflows/docker-build.yml (github-actions):
  actions/checkout                Andrew Nesbitt  2025-07-28  ed1b8d2

.github/workflows/publish.yml (github-actions):
  actions/checkout                Andrew Nesbitt  2023-04-18  e0a5436
  docker/build-push-action        Andrew Nesbitt  2023-04-18  e0a5436
  docker/login-action             Andrew Nesbitt  2023-04-18  e0a5436
  docker/metadata-action          Andrew Nesbitt  2023-04-18  e0a5436
```

---

```terminal
$ git pkgs outdated
Found 10 outdated dependencies:

Major updates:
  attr_encrypted 3.1.0 -> 4.2.0
  local_time 2.1.0 -> 3.0.2
  popper_js 1.16.1 -> 2.11.8
  bootstrap 4.6.2 -> 5.3.8
  gemoji 3.0.1 -> 4.1.0

Minor updates:
  ethon 0.15.0 -> 0.18.0
  numerizer 0.1.1 -> 0.2.0
  octicons 19.20.0 -> 19.21.2

Patch updates:
  rake-compiler-dock 1.11.0 -> 1.11.1
  commonmarker 2.6.2 -> 2.6.3
```

---

```terminal
$ git pkgs licenses
actions/checkout (github-actions): MIT
actions/setup-node (github-actions): MIT
active_record_query_trace (gem): MIT
attr_encrypted (gem): MIT
benchmark (gem): Ruby
better_errors (gem): MIT
bootsnap (gem): MIT
bootstrap (gem): MIT
brakeman (gem): QPL-1.0
bugsnag (gem): MIT
commonmarker (gem): MIT
docker/build-push-action (github-actions): Apache-2.0
docker/login-action (github-actions): Apache-2.0
docker/metadata-action (github-actions): Apache-2.0
dotenv-rails (gem): MIT
factory_bot (gem): MIT
faraday (gem): MIT
jbuilder (gem): MIT
jwt (gem): MIT
library/postgres (docker): Apache-2.0
library/redis (docker): Apache-2.0
...
```

---

```terminal
$ git pkgs vulns scan
No vulnerabilities found.

$ git pkgs vulns log
Vulnerability changes in 1 commits:

edbc795 build(deps): Bump public_suffix from 7.0.0 to 7.0.2
  + GHSA-g9jg-w8vm-g96v - action_text-trix@2.1.15 (low)
```

---

```terminal
$ git pkgs sbom
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.5",
  "metadata": {
    "timestamp": "2026-02-01T08:32:23Z",
    "tools": [{ "vendor": "git-pkgs", "name": "git-pkgs" }],
    "component": { "type": "application", "name": "project" }
  },
  "components": [
    {
      "type": "library",
      "name": "actions/checkout",
      "version": "8e8c483db84b4bee98b60c0593521ed34d9990e8",
      "purl": "pkg:githubactions/actions/checkout",
      "licenses": [{ "id": "MIT" }]
    },
    {
      "type": "library",
      "name": "rails",
      "version": "8.1.2",
      "purl": "pkg:gem/rails@8.1.2",
      "licenses": [{ "id": "MIT" }]
    },
    ...
  ]
}
```

---

```terminal
$ git pkgs bisect start HEAD v3.0.0
Bisecting: 847 dependency-changing commits between v3.0.0 and HEAD
[a4b2c8d] build(deps): Bump rails from 6.1.4 to 6.1.5

$ git pkgs bisect good
Bisecting: 423 commits left to test
[f8e9d1a] build(deps): Bump nokogiri from 1.12.0 to 1.13.0

$ git pkgs bisect bad
Bisecting: 211 commits left to test
[c3a7b2e] build(deps): Bump puma from 5.3.0 to 5.4.0

$ git pkgs bisect reset
Returning to HEAD at 7825978
```

---

```terminal
$ git pkgs add redis
Adding redis to Gemfile...
Running bundle install...
  Installing redis 5.4.0
Added redis 5.4.0 to Gemfile.lock

$ git pkgs update rails
Updating rails...
  rails 8.1.1 -> 8.1.2
  actioncable 8.1.1 -> 8.1.2
  actionmailer 8.1.1 -> 8.1.2
  activerecord 8.1.1 -> 8.1.2
Updated 12 dependencies

$ git pkgs remove unused-gem
Removing unused-gem from Gemfile...
Removed unused-gem and 3 unused transitive dependencies
```

---

```terminal
$ git pkgs schema
branch_commits
--------------
  id         INTEGER  PK
  branch_id  INTEGER
  commit_id  INTEGER
  position   INTEGER

branches
--------
  id                 INTEGER   PK
  name               TEXT      NOT NULL
  last_analyzed_sha  TEXT

commits
-------
  id            INTEGER   PK
  sha           TEXT      NOT NULL
  message       TEXT
  author_name   TEXT
  author_email  TEXT
  committed_at  DATETIME

dependency_changes
------------------
  id           INTEGER  PK
  commit_id    INTEGER
  manifest_id  INTEGER
  name         TEXT
  change_type  TEXT
  old_version  TEXT
  new_version  TEXT
...
```

---

```terminal
$ sqlite3 .git/pkgs.sqlite3 "SELECT name, COUNT(*)
  FROM dependency_changes GROUP BY name ORDER BY 2 DESC LIMIT 10;"
rails|133
pagy|116
autoprefixer-rails|109
oj|90
skylight|87
nokogiri|85
puma|74
actioncable|72
actionmailer|72
actionpack|72
```

# Go Modules

- github.com/git-pkgs/managers - Wrapping package manager CLIs behind a common interface
- github.com/git-pkgs/manifests - Parsing manifest and lockfiles
- github.com/git-pkgs/purl - Package URL construction, parsing, and registry URL mapping
- github.com/git-pkgs/registries - Fetching package metadata from registry APIs
- github.com/git-pkgs/spdx - SPDX license expression parsing and normalization
- github.com/git-pkgs/vers - Version range parsing and comparison per the VERS spec

# Thanks!

git-pkgs.dev

github.com/git-pkgs/git-pkgs

andrew@ecosyste.ms

# Coming soon...

![Forgejo integration](forgejo2.png)

---

![Forgejo integration](forgejo1.png)