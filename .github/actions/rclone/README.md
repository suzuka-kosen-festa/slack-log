# Github Action - `rclone`

Run [rclone](https://rclone.org) to sync files and directories from different cloud storage providers.

## Usage

```yaml
---
name: "🔄 Rclone"
# Working example: https://github.com/suzuka-kosen-festa/slack-archive/blob/main/.github/workflows/cron.yml
name: Backup Slack

on:
  schedule:
    - cron: 0 0 * * *
  workflow_dispatch:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  backup:
    runs-on: ubuntu-latest
    steps:
      - name: Setup | Checkout
        uses: actions/checkout@v4.1.1
      - name: Setup | Cache Stack
        id: cache-stack
        uses: actions/cache@v4.0.0
        with:
          path: ~/.stack
          key: ${{ runner.os }}-stack-${{ hashFiles('**/package.yaml') }}-${{ hashFiles('**/stack.yaml.lock') }}
          restore-keys: |
            ${{ runner.os }}-stack-
      - name: Setup | Stack and GHC
        uses: haskell-actions/setup@v2.6.1
        with:
          ghc-version: 8.10.4 # stack.yamlのresolverに合わせて更新してください
          enable-stack: true
          stack-version: latest
      - name: Run | Archive Slack
        run: stack build --exec 'slack-archive save'
        env:
          SLACK_API_TOKEN: ${{ secrets.SLACK_API_TOKEN }}
      - name: Run | Upload Archive
        uses: ./.github/actions/rclone
        with:
          config: ${{ secrets.RCLONE_CONFIG }}
          args: "sync ./docs r2:suzuka-kosen-festa-slack-log"
```

> - `config` can be omitted if [CLI arguments](https://rclone.org/flags/#backend-flags) or [environment variables](https://rclone.org/docs/#environment-variables) are supplied.
>   - can also be encrypted if [`RCLONE_CONFIG_PASS`](https://rclone.org/docs/#configuration-encryption) secret is set.
> - `args` pass any argumets supported by `rclone`.
> - `config-file` set custom location for `rclone` configuration file.
> - `debug` verbose debugging and logging or carry on, but do quit on errors.
