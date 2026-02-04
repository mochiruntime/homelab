# Homelab Ansible playbook


## Structure

- `roles/`: Ansible roles
- `molecule/`: Molecule testing scenarios
- `requirements.yml`: Galaxy requirements (roles/collections)

## Testing

This project uses Molecule to test the Ansible playbook with the Vagrant driver and the VirtualBox provider. It will create a virtual machine and run the Ansible playbook against it.

### Prerequisites

- **Python 3.10+** (managed via `.venv`)
- **Ansible**
- **Vagrant**
- **VirtualBox**
- **Molecule**

### Setup

To set up the development environment, create a virtual environment and install the dependencies:

```bash
# Create virtual environment (run once)
python3 -m venv .venv

# Activate virtual environment
source .venv/bin/activate       # Bash/Zsh
source .venv/bin/activate.fish  # Fish

# Install dependencies
pip install -r requirements.txt
ansible-galaxy install -r requirements.yml
```

### WSL Users

This project includes configuration in `molecule/default/molecule.yml` to support running Vagrant from within WSL (Windows Subsystem for Linux). 

The `provisioner.env` section specifically appends standard Windows system paths (`C:\Windows\System32`, `Powershell`, `VirtualBox`) to the `PATH` during test execution. This ensures that the Linux `vagrant` binary can communicate with the Windows `cmd.exe` and `VBoxManage.exe` as required by the `vagrant` driver in WSL.

**Note for WSL2:** Running VirtualBox VMs from WSL2 requires the `virtualbox_WSL2` Vagrant plugin. The project is configured to attempt to install this plugin automatically during `molecule create`. If it fails, you can install it manually:

```bash
vagrant plugin install virtualbox_WSL2
```

To run the full test suite:

```bash
molecule test
```

To create the test instance only:

```bash
molecule create
```
