# Role ivansible.lin-enter

Fix SSH port, username, python interpreter of remote host.


## Requirements

None


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
