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
1. Edit [the configuration file](config.sh.source) in the extracted
   release package according to your proxy configuration setup.
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
        # Use relative path in the link target, avoid breakage when
        # renaming/moving the home directory
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

   Note that for most command-line applications the proxy configuration
   only applies to the text terminal session you run the `source`
   command in, if you are in another one you need to source the
   configuration script again.

## References

The following materials are referenced during the development of this project:

* [Linux修改系统代理 - JoXrays's Blog](https://www.joxrays.com/linux-system-proxy/)  
  Explains how to modify proxy settings using text terminal commands.
* [curl - How To Use - Environment](https://curl.se/docs/manpage.html)  
  Explains common environment variables used to configure proxy settings.
* [DevGnomeOrg/Gnome3PortingGuide/ProxyConfiguration – GNOME Wiki Archive](https://wiki.gnome.org/DevGnomeOrg(2f)Gnome3PortingGuide(2f)ProxyConfiguration.html)  
  Explains how to modify proxy settings for GSettings-based applications.
* [Proxy | KDE 5 docs](https://docs.kde.org/trunk5/en/kio/kcontrol5/proxy/)  
  Explains how to modify proxy settings for KDE-based applications.
* [Core configuration | Server configuration](https://documentation.ubuntu.com/lxd/latest/server/#core-configuration)  
  Explains the proxy configuration options for LXD.
* [system proxy.{http,https,ftp} | System options | Snapcraft documentation](https://snapcraft.io/docs/system-options#heading--proxy)  
  Explains the proxy configuration options for Snapd.
* [Legacy proxy server support | Network Connections in Visual Studio Code](https://code.visualstudio.com/docs/setup/network#_legacy-proxy-server-support)  
  Explains how to modify proxy settings for Visual Studio Code-like applications.
* [Proxy KCM: Show a warning when a KIO-specific setting is selected (!422) · Merge requests · Frameworks / KIO · GitLab](https://invent.kde.org/frameworks/kio/-/merge_requests/422)  
  Explains what the proxy settings in the KDE System Settings application applies to.

## Licensing

Unless otherwise noted(individual file's header/[REUSE DEP5](.reuse/dep5)), this product is licensed under [the 3.0 version of the GNU Affero General Public License](https://www.gnu.org/licenses/agpl-3.0.txt), or any of its recent versions you would prefer.

This work complies to [the REUSE Specification](https://reuse.software/spec/), refer the [REUSE - Make licensing easy for everyone](https://reuse.software/) website for info regarding the licensing of this product.
