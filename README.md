# md-check-link

This tool was created as a a fork of [markdown-link-check](https://github.com/tcort/markdown-link-check) with added support for checking links to [docsify aliases](https://docsify.js.org/#/configuration?id=alias). During the development it turned out that the original tool is not maintained anymore, and too many changes are required to make it performant and fully functional. Therefore, it was decided to create a new tool with a new name and keep only similar configuration options and reporting format.

Features:
- check for broken links in markdown files
- validate anchors to headers in the same file and to other files in the same project (relative links)
- validate links to external resources
- validate links to websites hosting markdowns with [docsify](https://docsify.js.org/#/) (e.g. GitHub pages)
- parallel processing of links
- configurable timeout and retry count
- configurable replacement patterns for links (e.g. to replace docsify aliases with raw links)
- configurable alive status codes

## Prerequisites

- [node.js](https://nodejs.org/en) 20.x or higher

## Installation

npm install -g md-check-link

## Usage

```
Usage: md-check-link [options] [filesOrFolders...]

Options:
  -V, --version            output the version number
  -n, --parallel <number>  number of parallel requests (default: 2)
  -c, --config [config]    apply a config file (JSON), holding e.g. url specific header configuration
  -q, --quiet              displays errors only
  -h, --help               display help for command
```
Example:
```
md-check-link -n 8 -c config.json docs
```

## Sample github action

This workflow verify links in all markdown files in the repository. It runs every day at midnight and on pull requests. You can also run it manually. 

```
name: Verify markdown links

on:
  workflow_dispatch:
  schedule:
    - cron: '0 0 * * *'
  pull_request:
  
jobs:
  verify-links:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20.x'
      - name: Install md-check-link
        run: npm install -g md-check-link
      - name: Verify links
        run: |
          md-check-link -q -n 8 -c https://raw.githubusercontent.com/kyma-project/md-check-link/main/.mlc.config.js ./
```

## Configuration file
```
{
  "ignorePatterns": [
    {
      "pattern": "^http://example.net"
    }
  ],
  "replacementPatterns": [
    {
      "docsify": true,
      "pattern": "^https://kyma-project.io/#/btp-manager/",
      "replacement": "https://raw.githubusercontent.com/kyma-project/btp-manager/main/docs/"
    },
    {
      "pattern": "^/api-gateway/",
      "replacement": "https://raw.githubusercontent.com/kyma-project/api-gateway/main/docs/"
    }

  ],
  "timeout": "10s",
  "retryCount": 5,
  "aliveStatusCodes": [200]
}
```

## Contributing
<!--- mandatory section - do not change this! --->

See the [Contributing Rules](CONTRIBUTING.md).

## Code of Conduct
<!--- mandatory section - do not change this! --->

See the [Code of Conduct](CODE_OF_CONDUCT.md) document.

## Licensing
<!--- mandatory section - do not change this! --->

See the [license](./LICENSE) file.
