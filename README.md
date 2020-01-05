# ivansible.lin_enter

Fix SSH port, username, python interpreter of remote host.


## Requirements

Playbook setting `gather_facts` should be set to false when running this role
because it will fail if Ansible SSH port is set incorrectly.

This role uses [JMES path](http://jmespath.org/), expressions
so please `pip install jmespath`.


## Variables


### Settings to probe for (and probably setup)

    linen_user: ""
    linen_pass: ""
    linen_port: ""
    linen_keyfile: ""

...

    linen_default_keyfile: ""

this can make command line shorter


### Settings to setup (only if differ from above)

    linen_new_user: ""
    linen_new_port: ""
    linen_new_keyfile: ""

...


### SSH ports and keys

    linen_ufw: true
Allows to install and enable `ufw` firewall.

    linen_secure: true
Allows to configure non-standard SSH port.


### Create new user

    linen_desired_uid: 1000

 attempt to adjust user to this uid

    linen_scratch_users:
      - ubuntu
      - vagrant

these users can be renamed


### When debugging, you can set these manually:
    found_user:"-"
    found_port:"-"
    found_python:"-"

## Tags

None


## Dependencies

None


## Example Playbook

    - hosts: newhost
      roles:
        - ivansible.lin_enter


## Testing

    ./bin/lin-enter.sh newhost


## Implementation Details

### Port and user probing

So, we:
- use wait_for_connection
    because all other methods are subject to UNREACHABLE errors
- override with ansible_ssh_user instead of ansible_user
    because former override does not work in a var: under loop
- what is 'unreachable' vs other network problems
    'unreachable' can happen if we use a wrong ssh key
- trick with 'failed_when' could speed up python search
- dont use trick with `failed_when` and /no_such_python
    because wait_for_connection hides ping output
- dont use async:1 poll:0
  link to stackoverflow:
    https://stackoverflow.com/q/23877781
    https://stackoverflow.com/questions/23877781/how-to-wait-for-server-restart-using-ansible
  but async/poll does not work with 'raw' and 'ping'
- dont use blocks and 'rescue', links to:
  "blocks rescue from unreachable host"
     https://github.com/ansible/ansible/issues/13870
     (proposed feature to recover after unreachable error)
  "abort/fail is host is unreachable"
     https://github.com/ansible/ansible/issues/18782
     (exactly opposite behavior - abort all hosts in playbook)

### Settings priorities

    new_port: "{{ linen_new_port
                | default(orig_port, true)
                | default(linen_port, true)
                | default(ansible_ssh_port, true) }}"
    new_user: "{{ linen_new_user
                | default(orig_user, true)
                | default(linen_user, true)
                | default(ansible_user_id) }}"
    new_keyfile: "{{ linen_new_keyfile_
                   | default(orig_keyfile, true)
                   | default(linen_keyfile_, true)
                   | default(ansible_private_key_file, true)
                   | default('') }}"

...

### Tuning concepts

why not: apt update/upgrade, install ntp, change timezone

### User management

rename if ...
create if

rename:
`raw` is better becase `command`/`shell` modules depend on home directory
`async`+`poll` prevents the unreachable error, but incompatible with `raw`
after testing many combinations, `command` is the only viable alternative

### Python management

Current logic is simple: install python2 if not present.
In future, more methods will be implemented.

what if ansible_python_interpreter != /usr/bin/python
... options: ignore, install python2, switch inventory to python3
.... ... or choose ansible_playbook_python (i.e. follow controller)
... variables for this logic??

### TODO

- change open vagrant password
- remove user password
- if linen_python=python3, search it first
- if linen_port=xxx, search it first
- if linen_vagrant=true, skip other methods


## License

MIT


## Author

Created in 2018-2020 by [IvanSible](https://github.com/ivansible)
