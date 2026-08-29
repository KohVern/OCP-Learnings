## Running the Playbook

### Pre-Requisites
- Linux
- Ansible

Ensure `ansible` installed:
- `sudo apt update -y`
- `sudo apt install ansible`

### Run the playbook locally

Execute the `Sample.yml` playbook using the following command:

```bash
ansible-playbook -i inventory.ini Sample.yml
```

This command uses `inventory.ini` as the Ansible inventory and runs `Sample.yml` against the defined hosts.
