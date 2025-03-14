# Fail2ban Input Plugin

This plugin gathers the count of failed and banned IP addresses using
[fail2ban][fail2ban] by running the `fail2ban-client` command.

> [!NOTE]
> The `fail2ban-client` requires root access, so please make sure to either
> allow Telegraf to run that command using `sudo` without a password or by
> running telegraf as root (not recommended).

⭐ Telegraf v1.4.0
🏷️ network, system
💻 all

[fail2ban]: https://www.fail2ban.org

## Global configuration options <!-- @/docs/includes/plugin_config.md -->

In addition to the plugin-specific configuration settings, plugins support
additional global and plugin configuration settings. These settings are used to
modify metrics, tags, and field or create aliases and configure ordering, etc.
See the [CONFIGURATION.md][CONFIGURATION.md] for more details.

[CONFIGURATION.md]: ../../../docs/CONFIGURATION.md#plugins

## Configuration

```toml @sample.conf
# Read metrics from fail2ban.
[[inputs.fail2ban]]
  ## Use sudo to run fail2ban-client
  # use_sudo = false

  ## Use the given socket instead of the default one
  # socket = "/var/run/fail2ban/fail2ban.sock"
```

## Using sudo

Make sure to set `use_sudo = true` in your configuration file.

You will also need to update your sudoers file.  It is recommended to modify a
file in the `/etc/sudoers.d` directory using `visudo`:

```bash
sudo visudo -f /etc/sudoers.d/telegraf
```

Add the following lines to the file, these commands allow the `telegraf` user
to call `fail2ban-client` without needing to provide a password and disables
logging of the call in the auth.log.  Consult `man 8 visudo` and `man 5
sudoers` for details.

```text
Cmnd_Alias FAIL2BAN = /usr/bin/fail2ban-client status, /usr/bin/fail2ban-client status *
telegraf  ALL=(root) NOEXEC: NOPASSWD: FAIL2BAN
Defaults!FAIL2BAN !logfile, !syslog, !pam_session
```

## Metrics

- fail2ban
  - tags:
    - jail
  - fields:
    - failed (integer, count)
    - banned (integer, count)

## Example Output

```text
fail2ban,jail=sshd failed=5i,banned=2i 1495868667000000000
```

### Execute the binary directly

```shell
# fail2ban-client status sshd
Status for the jail: sshd
|- Filter
|  |- Currently failed: 5
|  |- Total failed:     20
|  `- File list:        /var/log/secure
`- Actions
   |- Currently banned: 2
   |- Total banned:     10
   `- Banned IP list:   192.168.0.1 192.168.0.2
```
