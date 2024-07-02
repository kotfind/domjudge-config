# DOMjudge Simple Installation and Configuration Example

Simple docker-compose.yml for domjudge and some utils.

Tested on Arch Linux.

## Installation

### Enable Linux Cgroups (v1)
Enabling cgroups v1 is needed for judgehost.
Open `/etc/default/grub` and add these options to `GRUB_CMDLINE_LINUX_DEFAULT`:

    - cgroup_enable=memory,cpuset
    - swapaccount=1
    - isolcpus=2 (optional)
    - systemd.unified_cgroup_hierarchy=0 (if cgroup (v2) is enabled by default)
    - SYSTEMD_CGROUP_ENABLE_LEGACY_FORCE=1 (needed on some systemd)

So 
```bash
GRUB_CMDLINE_LINUX_DEFAULT="... cgroup_enable=memory,cpuset swapaccount=1 isolcpus=2 systemd.unified_cgroup_hierarchy=0 SYSTEMD_CGROUP_ENABLE_LEGACY_FORCE=1"
```

Updage grub config:
```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

Reboot your computer:
```bash
reboot
```

### Run DOMjudge
Install `git`, `docker` and `docker-compose`.

Clone this repository:
```bash
git clone https://github.com/kotfind/domjudge-config
```

Change `JUDGEDAEMON_PASSWORD` in `docker-compose.yml`.

Start server:
```bash
docker compose up -d
```

Check [localhost:8000](localhost:8000).

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

### Config DOMjudge
Go to `Users > judgehost > Edit`. Set judgehost password to same value as `JUDGEDAEMON_PASSWORD`.

Create jury team to allow admin to submit solutions.
Go to `Teams > Add new team`.

| Field | Value |
-----------------
| Team name | Jury |
| Category | System |
| Add user to this team | Add existing user |
| User | Administrator |

Config languages. Go to `Languages`. Enable needed languages.

Add group (category) for teams. Go to `Team Categories > Add new category`.

| Field | Value |
-----------------
| Name | Teams |
| Visible | Yes |

Remember it's `ID`. I'll call it `TEAMS_GROUP_ID`.

Add some users. You can either add them one by one with `bin/add_user` script
or by batch with `bin/add_user_batch` script. For more information run:
```bash
bin/add_user -h
bin/add_user_batch -h
```

Create a contest. Go to `Contests > Add new contest`.

| Field | Value |
-----------------
| Short Name | \*Value\* |
| Name | \*Value\* |
| Activate time | \*Value\* |
| Start time | \*Value\* |
| End time | \*Value\* |

Add some problem. Create a problem template with `bin/gen_problem_template`. For mor information run:
```bash
bin/gen_problem_template -h
```
After updating it, go to problem's directory and put it's contents into archive.
Archive should be called same as problem's short name (i.e. `A`, `B`, `C`, ...).
```bash
cd A
7z a A.zip ./*
```
Go to `Problems > Import problem > Problems > Import JSON / YAML`.
Select contest and problem archive, press `Import`.

Allow teams to download their submissions. Go to `Configuration settings > Display > Allow team submission download`.
