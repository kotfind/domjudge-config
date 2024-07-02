# Simple DOMjudge Setup Example

Simple docker-compose.yml for DOMjudge and some utils.

Tested on Arch Linux.

## Instal dependencies
These executables should be available on your system:
- docker
- docker-compose
- git (for installation only)
- jq (for bin/ scripts only)
- 7z (for bin/ scripts only)

## Enable Linux Cgroups (v1)
Enabling cgroups v1 is needed for judgehost.
Open `/etc/default/grub` and add these options to `GRUB_CMDLINE_LINUX_DEFAULT`:

    - cgroup_enable=memory,cpuset
    - swapaccount=1
    - isolcpus=2 (optional)
    - systemd.unified_cgroup_hierarchy=0 (if cgroup (v2) is enabled by default)
    - SYSTEMD_CGROUP_ENABLE_LEGACY_FORCE=1 (needed on some systemd)

So `/etc/default/grub` should have similar line:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="... cgroup_enable=memory,cpuset swapaccount=1 isolcpus=2 systemd.unified_cgroup_hierarchy=0 SYSTEMD_CGROUP_ENABLE_LEGACY_FORCE=1"
```

Update grub config:
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Reboot your computer:
```bash
reboot
```

## Run DOMjudge
Clone this repository:
```bash
git clone https://github.com/kotfind/domjudge-config
```

Change `JUDGEDAEMON_PASSWORD` in `docker-compose.yml`.

Start server:
```bash
docker compose up -d
```

Check [http://localhost:8000](http://localhost:8000).

Get admin password with:
```bash
bin/get_admin_initall_pass
```
Or reset it with:
```bash
bin/reset_admin_pass
```

Set `DOMJUDGE_DOMAIN` and `DOMJUDGE_ADMIN_PASS` environment variables
if you want to use scripts from `bin/utils`:
```
export DOMJUDGE_DOMAIN="localhost:8000"
export DOMJUDGE_ADMIN_PASS="*PASSWORD*"
```

## Config DOMjudge
### Set judgehost password
Go to `Users > judgehost > Edit`.
Set judgehost password to same value as `JUDGEDAEMON_PASSWORD`.
Now judgehost `judge-0-0` should appear in judgehosts list (in the menu bar).

### Create jury team
Create jury team to allow admin to submit solutions.
Go to `Teams > Add new team`.

| Field | Value |
|---|---|
| Team name | Jury |
| Category | System |
| Add user to this team | Add existing user |
| User | Administrator |

### Config programming languages
To enable language `*LANG*` do `Languages > *LANG* > Allow submit`.

### Create a team group
Add group (category) for teams. Go to `Team Categories > Add new category`.

| Field | Value |
|---|---|
| Name | Teams |
| Visible | Yes |

Remember it's `ID`. It should be passed to `bin/add_user` and `bin/add_user_batch` scripts with `-g` flags if used.

### Create user
You can either add users one by one with `bin/add_user` script
or by batch with `bin/add_user_batch` script. For more information run:
```bash
bin/add_user -h
bin/add_user_batch -h
```

> **_NOTE:_** Don't forget the [team group step](#create-a-team-group).

### Create a contest
Create a contest. Go to `Contests > Add new contest`.

| Field | Value |
|---|---|
| Short Name | \*Value\* |
| Name | \*Value\* |
| Activate time | \*Value\* |
| Start time | \*Value\* |
| End time | \*Value\* |

### Add a problem
Create a problem template with `bin/gen_problem_template`. For more information run:
```bash
bin/gen_problem_template -h
```

After updating it, use `./bin/load_problem` to upload it. For more information run:
```bash
bin/load_problem -h
```

### Other configuration
To allow teams to download their submissions go to `Configuration settings > Display > Allow team submission download`.
