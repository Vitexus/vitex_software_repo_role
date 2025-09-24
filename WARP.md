# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

This is an Ansible role (`vitex_software_repo_role`) that configures the VitexSoftware Debian repository on Debian/Ubuntu systems. The role installs repository keys and configures apt sources to enable installation of VitexSoftware packages.

## Architecture

### Ansible Role Structure
- `meta/main.yml`: Role metadata, dependencies, and Ansible Galaxy configuration
- `defaults/main.yml`: Default variables (repository components: main, backports, borrowed)
- `tasks/main.yml`: Main installation tasks (download key, install keyring package)
- `handlers/main.yml`: Event handlers (currently empty)
- `vars/main.yml`: Role-specific variables (currently empty)
- `files/`: Static files (contains keyring .deb package)
- `tests/test.yml`: Basic test playbook

### Key Components
- **Repository Key Management**: Downloads GPG key from `http://repo.vitexsoftware.cz/KEY.gpg`
- **Keyring Package**: Installs `repovitexsoftwarecom-archive-keyring_2.0.0_all.deb` for secure repository access
- **Component Configuration**: Supports main, backports, and borrowed repository components

## Common Development Commands

### Testing and Validation
```bash
# Lint YAML files
yamllint .

# Lint Ansible content
ansible-lint .

# Test role locally (requires root/sudo)
ansible-playbook tests/test.yml --ask-become-pass

# Syntax check
ansible-playbook tests/test.yml --syntax-check

# Docker-based testing (recommended)
docker build -f Dockerfile.test -t vitex-repo-role-test .
docker run --rm -it vitex-repo-role-test ansible-playbook test.yml

# Test idempotency with persistent container
docker run -d --name test-container vitex-repo-role-test
docker exec test-container ansible-playbook test.yml  # First run
docker exec test-container ansible-playbook test.yml  # Second run should show no changes
docker stop test-container && docker rm test-container
```

### Development Workflow
```bash
# Test role against specific host
ansible-playbook -i inventory tests/test.yml

# Run with specific components
ansible-playbook tests/test.yml -e "vitex_software_repo_components=['main']"

# Check what would change (dry run)
ansible-playbook tests/test.yml --check
```

### Role Usage Examples
```yaml
# Basic usage with all components
- hosts: servers
  roles:
    - vitex_software_repo_role

# Custom components
- hosts: servers
  roles:
    - { role: vitex_software_repo_role, vitex_software_repo_components: [main, games] }

# Conditional installation
- name: Include role to add vitexsoftware repository
  ansible.builtin.include_role:
    name: vitex_software_repo_role
  when: not vitexsoftware_list.stat.exists
```

## Key Variables

- `vitex_software_repo_components`: List of repository components to enable (default: [main, backports, borrowed])

## Platform Support

- **Debian**: bullseye, bookworm, trixie
- **Ubuntu**: focal, jammy, noble
- **Minimum Ansible Version**: 2.1

## Development Notes

- The role follows standard Ansible Galaxy structure
- Tasks focus on repository setup without package installation
- Static keyring package is bundled in `files/` directory
- No dependencies on other roles
- MIT licensed

## Files to Update When

### Adding New Supported Distributions
- Update `meta/main.yml` platforms section
- Test on new distribution versions

### Updating Repository Key
- Replace `files/repovitexsoftwarecom-archive-keyring_*.deb`
- Update version in `tasks/main.yml`
- Update download URL if needed

### Adding New Repository Components
- Add to `defaults/main.yml`
- Document in `README.md`