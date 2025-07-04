# Kong Pongo action

A Github Action for running plugin tests using [Kong Pongo](https://github.com/Kong/kong-pongo).


## Usage

See the [Kong Pongo repo](https://github.com/Kong/kong-pongo) for help on Pongo commands and environments.

This action will install and configure Pongo for a specific version of [Kong Gateway](https://konghq.com) to test against. The settings will be exported as standard Pongo environment variables, such that any follow up commands will work with the same settings.


## Inputs

| input | default | required | description |
| ----- | ------- | -------- | ----------- |
| `kong_version` |  | yes | The Kong Gateway version to test your plugin against. Check the Pongo documentation for allowed formats for latest patch releases and/or nightly builds. |
| `pongo_version` | `"latest"` | yes | The Pongo version to use for testing. This can be a Pongo version tag or branch name (use `"master"` for bleeding-edge). A special case is `"latest"` which will use the latest released version. |
| `start_environment` | `"true"` | no | By default the test environment will be spun up. Set this value to `"false"` to not start the test environment. |
| `build_image` | `"true"` | no | By default the test image will be build. Set this value to `"false"` to not build the test image. |
| `cache` |  | no | If set, cache the Pongo test image using the given docker cache type to avoid rebuilding the image at each run. Can only be set to `github` for now. |
| `license` | value of env var `KONG_LICENSE_DATA` | no | The Kong license. This is only required if you are testing a plugin against an Enterprise version of the Kong Gateway. </br>**Note**: make sure to pass it to Github as a secret! |


## Example

```yaml
# .github/workflows/test.yaml

name: "Test"
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    strategy:
      fail-fast: false
      matrix:
        kong_version:
        - "2.7.x"
        - "2.8.x"
        - "dev"

    steps:
    - uses: actions/checkout@v3

    # install Pongo, build the image for the Kong version, and
    # start the environment (postgres, cassandra, etc)
    - uses: Kong/kong-pongo-action@v1
      with:
        kong_version: ${{ matrix.kong_version }}

    # Pongo commands will now work after the setup step above
    - run: pongo run
```

## History

#### Version 1.0.3 (released 28-Aug-2024)

 -  fix: conditional syntax errors in `steps[*].if` 

#### Version 1.0.2 (released 26-Feb-2023)

- Nightlies: also export required variable if version is `"nightly-ee"` (the old
  name for `"dev-ee"` used since Pongo 2)

#### Version 1.0.1 (released 20-Oct-2022)

- Github: replace deprecated "set-output" with environment files

#### Version 1 (released 7-Jul-2022)

- Initial version of the action
