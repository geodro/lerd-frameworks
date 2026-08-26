# Lerd Framework Store

> The community-driven store of framework definitions that powers
> [Lerd](https://lerd.sh) — teach Lerd a new PHP framework by
> editing YAML, no binary release required.

[![Part of Lerd](https://img.shields.io/badge/part%20of-lerd-ff2d20)](https://lerd.sh)
[![Docs](https://img.shields.io/badge/docs-lerd.sh-blue)](https://lerd.sh/usage/frameworks)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

When you run `lerd link` on a project, Lerd detects which framework it is and pulls the matching definition from this store — then it knows how to serve it, which workers to run, how to set up its `.env`, how to scaffold it, and how to health-check it. Everything is a versioned YAML file. Add a framework here and every Lerd install can use it within 24 hours, with no new Lerd release and no Go code.

That's the whole point: **Lerd is framework-agnostic, and this repo is where that agnosticism lives.** Laravel, Symfony, WordPress, Drupal, CakePHP, CodeIgniter, Statamic, Magento, and Tempest are all defined here — not hardcoded in the binary — and so is whatever framework you add next.

## What a definition powers

A single `frameworks/<name>/<version>.yaml` teaches Lerd to:

- 🔎 **Auto-detect** the framework and its major version on `lerd link`, from lockfiles, marker files, or `composer.json` entries
- 🐘 **Pin PHP** to the versions the framework supports, so the right runtime is selected automatically
- 🌱 **Set up `.env`** — wire database, cache, and service hosts, generate app keys, and apply framework-specific defaults
- ⚒️ **Run the right workers** — queue, schedule, Horizon, Reverb, a host-side Vite dev server, and more, each self-healing and idle-suspendable
- 🚀 **Scaffold new projects** with the framework's own `create-project` command
- 🩺 **Health-check** the site through Lerd's framework-agnostic doctor, with checks declared right in the definition
- 🧪 **Drive the Tinker REPL**, custom commands, log tails, and post-link setup steps (migrations, `storage:link`, and friends)

All of it is data. None of it ships in the binary.

## Available frameworks

| Framework | Versions | Detection |
|-----------|----------|-----------|
| Laravel | 13, 12, 11, 10 | `artisan` file or `laravel/framework` in composer.json |
| Symfony | 8, 7 | `symfony.lock` file or `symfony/framework-bundle` in composer.json |
| WordPress | 7, 6, 5 | `wp-login.php` file or `wp-config.php` file |
| Drupal | 11, 10 | `drupal/core-recommended` or `drupal/core` in composer.json |
| TYPO3 | 14, 13, 12, 11, 10 | `typo3/cms-core` in composer.json or `public/typo3` directory |
| CakePHP | 5, 4 | `bin/cake` file or `cakephp/cakephp` in composer.json |
| CodeIgniter | 4 | `spark` file or `codeigniter4/framework` in composer.json |
| Statamic | 6, 5 | `statamic/cms` in composer.json |
| Magento | 2 | `bin/magento` file or `magento/product-community-edition` in composer.json |
| Tempest | 3 | `tempest` file or `tempest/framework` in composer.json |

Don't see yours? [Add it](#contributing) — that's what this repo is for.

## Usage

You rarely touch the store directly: link a project and Lerd offers to install the matching definition for you. When you want to manage it by hand:

```bash
lerd framework search                # list everything available
lerd framework search symfony        # search by name
lerd framework install symfony       # auto-detects the version from composer.lock
lerd framework install laravel@12    # install a specific major version
lerd framework list --check          # compare your local definitions against the store
lerd framework update                # refresh all installed definitions
```

Installed definitions auto-refresh every 24 hours, so improvements landed here reach existing installs without an update.

## Contributing

New frameworks and version bumps are welcome — this store is only as good as the community around it.

1. Fork this repo
2. Add or update `frameworks/<name>/<version>.yaml`
3. Optionally add the framework's own mark as `frameworks/<name>.svg` (see below)
4. Add or update the entry in `frameworks/index.json` (name, label, versions, latest, detect rules)
5. Open a pull request

### Definition schema

Every definition declares a `version` matching the major release it targets, plus detection rules and whichever capabilities apply:

```yaml
name: myframework
version: "7"
label: My Framework
color: "#4a90d9"
public_dir: public
detect:
  - composer: myvendor/myframework
php:
  min: "8.2"
env:
  # database/cache/service wiring, app key generation
workers:
  # queue, schedule, and other long-running processes
setup:
  # post-link commands (migrations, symlinks)
doctor:
  # declarative health checks
```

### The framework's own mark

A framework used to be a text label everywhere lerd showed it. It can now carry
its logo: add `frameworks/<name>.svg` beside the versioned directory, and declare
a `color:` in the YAML for the tint lerd paints it in.

The mark is per family, not per version, so one file serves every release and it
sits next to `<name>/` rather than inside it. The colour lives in the YAML, which
only exists per version, so repeat the same `color:` in each version file.

It is **monochrome**: one silhouette of filled paths with no `fill`, `stroke`,
`style` or `class` of its own, in a bare `<svg viewBox="...">`. lerd strips
everything but the geometry on the way in, along with script, `foreignObject`,
event handlers and external references, then paints it in the declared colour.
Not a full colour logo, and not a wordmark, which is unreadable at the size this
renders. Take the mark, not the lockup.

```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24"><path d="…"/></svg>
```

`color:` must be a plain hex literal, `#ff2d20` or `#abc`; anything else is
dropped and the framework renders as its label alone. A framework with a colour
but no mark still gets the tint. The marks currently in this repo come from
[Simple Icons](https://simpleicons.org), which is CC0, except Magento's, which
comes from the project's own repo.

See the [frameworks documentation](https://lerd.sh/usage/frameworks) for the full schema reference and every available field.

## License

MIT
