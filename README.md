# Role ivansible.lin-enter

Fix SSH port, username, python interpreter of remote host.


## Requirements

Playbook setting `gather_facts` should be set to false when running this role
because it will fail if the Ansible SSH port is not set correctly.

## Variables

None


## Tags

None


## Dependencies

None


## Example Playbook

    - hosts: dock2
      roles:
        - { role: ivansible.lin-enter }


## Testing

    ansible-playbook plays-all/test-role.yml -e role=ivansible.lin-enter -l newhost


## License

MIT


## Author

Created in 2018 by [IvanSible](https://github.com/ivansible)
