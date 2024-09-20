# Linux proxy configuration helper

Helper scripts to assist in configuring proxy settings on a GNU/Linux desktop.

<https://gitlab.com/brlin/linux-proxy-configuration-helper>  
[![The GitLab CI pipeline status badge of the project's `main` branch](https://gitlab.com/brlin/linux-proxy-configuration-helper/badges/main/pipeline.svg?ignore_skipped=true "Click here to check out the comprehensive status of the GitLab CI pipelines")](https://gitlab.com/brlin/linux-proxy-configuration-helper/-/pipelines) [![GitHub Actions workflow status badge](https://github.com/brlin-tw/linux-proxy-configuration-helper/actions/workflows/check-potential-problems.yml/badge.svg "GitHub Actions workflow status")](https://github.com/brlin-tw/linux-proxy-configuration-helper/actions/workflows/check-potential-problems.yml) [![pre-commit enabled badge](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white "This project uses pre-commit to check potential problems")](https://pre-commit.com/) [![REUSE Specification compliance badge](https://api.reuse.software/badge/gitlab.com/brlin/linux-proxy-configuration-helper "This project complies to the REUSE specification to decrease software licensing costs")](https://api.reuse.software/info/gitlab.com/brlin/linux-proxy-configuration-helper)

## Problems

Currently there are several problems in the configuration of forward
proxy settings on GNU+Linux operating systems:

* Different desktop environments/applications/frameworks/services uses
  different methods to configure proxy server, there isn't a central
  place to set it.
* In many context applying proxy setting modification requires a user
  session restart(e.g. logout and re-login), which is costly.

## Solution

This project feature a stopgap measure by source-ing shell scripts to
configure/remove proxy settings in most known components on-demand in a
text terminal, which simplifies the configuration process.

## Prerequisites

* You need to have the `sudo` software installed.
* You need to have sudo privileges to run `root` commands.

## Usage

1. Download the release package from [the project's Releases page](https://gitlab.com/brlin/linux-proxy-configuration-helper/-/releases).
1. Extract the downloaded release package.
1. Edit [the set-proxy.sh.source proxy configuration script](set-proxy.sh.source)
   and [the unset-proxy.sh.source proxy de-configuration script](unset-proxy.sh.source)
   in the extracted release package to customize the header variables
   according to your proxy configuration setup.
1. Launch a text terminal, then change the working directory to the
   extracted release package directory by running the following command:

   ```bash
   cd /path/to/linux-proxy-configuration-helper-X.Y.Z
   ```

1. Create symbolic links to the configuration/de-configuration script
   for easy access in text terminals by running the following comamnd:

   ```bash
   ln_opts=(
        # Create symbolic link instead of hark link
        --symbolic
        # Use relative path in the link target
        --relative
        # Replace existing links
        --force
        # Print what is actually done
        --verbose
   )
   ln "${ln_opts[@]}" *.sh.source ~
   ```

1. You can now configure the proxy settings by running the following
   command in a text terminal:

   ```bash
   source ~/set-proxy.sh.source
   ```

   and deconfigure them by running the following command:

   ```bash
   source ~/unset-proxy.sh.source
   ```

   Note that for text terminal operations the proxy configuration only
   applies to this text terminal session, if you started a new one you
   need to source the configuration script again.

## References

The following materials are referenced during the development of this project:

* [Linux修改系统代理 - JoXrays's Blog](https://www.joxrays.com/linux-system-proxy/)  
  Explains how to modify proxy settings using text terminal commands.

## Licensing

Unless otherwise noted(individual file's header/[REUSE DEP5](.reuse/dep5)), this product is licensed under [the 3.0 version of the GNU Affero General Public License](https://www.gnu.org/licenses/agpl-3.0.txt), or any of its recent versions you would prefer.

This work complies to [the REUSE Specification](https://reuse.software/spec/), refer the [REUSE - Make licensing easy for everyone](https://reuse.software/) website for info regarding the licensing of this product.
