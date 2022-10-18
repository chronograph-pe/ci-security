# ci-security

A list of CI security tools to be used in Github action workflows. These yaml files are in a [Github reuseable workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows) format. 

Available security tools are:
1. [Semgrep SAST](https://semgrep.dev/): A static application security testing tool to secure software by reviewing the source code of the software to identify sources of vulnerabilities
2. [A dependency license check](https://github.com/chronograph-pe/dependency-track-gh-action): A Cg developed tool to check projects for approved software dependency licenses. 

## Prerequisites
Add a deploy key secret called `DEPLOY_KEY` to your repo to use `ci-security`. Contact the security team to set this up for you.

To use the Semgrep SAST, you need to create a Semgrep app token and store it as a secret in your repository. 

To use the dependency license check, you need to create:
1. `license-file_.json` in each app directory. The content of this file should be {} for initial setup.
2. `dependency-exceptions.json` in the repo root directory. The contents are a list of dependencies you do not want to be scanned for license violations. If there are no exceptions, the file must contain an empty list -> {}.
3. `allowed_licenses.json` in the repo root directory. The contents are a list of licenses you allow to be used in your apps. by default all other licenses will be restricted.


## Usage

1. Add to your repository a Github action called `.github/workflows/ci-security.yml` 
2. Add the security actions you want to use for your repository. An example `ci-security.yml` file is below:

```yaml
on: [push]


jobs:

  setup:
    uses: chronograph-pe/checkout@v3
    with:
      repository: 'chronograph-pe/ci-security'
      ref: 'main'
      ssh-key: ${{ secrets.DEPLOY_KEY }}
      path: 'ci-security'

  semgrep-sast-scan:
    uses: ./ci-security/.github/workflows/semgrep-sast.yml@main
    with:
      semgrep-scan-timeout: 300
    secrets:
      semgrep-app-token: ${{ secrets.SEMGREP_APP_TOKEN }}

  dependency-license-check:
    uses: ./ci-security/.github/workflows/dependency-license-check.yml@main
    with:
      dependency-check-config-file: "dependency-check-config.yml"
      
```