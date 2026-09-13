# ludus_jenkins_agent_windows

Ansible role that installs a Jenkins agent as a Windows service on Windows Server hosts. Uses NSSM for service management and connects to the Jenkins controller via WebSocket.

## What it does

1. Installs OpenJDK 21 via Chocolatey
2. Creates a dedicated local `jenkins-agent` user with admin privileges
3. Downloads `agent.jar` from the Jenkins controller
4. Installs NSSM (Non-Sucking Service Manager) via Chocolatey
5. Registers the Jenkins agent as a Windows service with auto-start
6. Creates a firewall rule for the agent port

## Requirements

- Windows Server 2022
- Chocolatey package manager available
- Network access to the Jenkins controller
- The agent must be pre-configured in Jenkins (matching the `ludus_jenkins_agent_name`)

## Role Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ludus_jenkins_url` | `http://jenkins:8080` | Jenkins controller URL |
| `ludus_jenkins_agent_name` | `{{ inventory_hostname }}` | Agent name (must match Jenkins node config) |
| `ludus_jenkins_agent_labels` | `windows-msvc windows-dotnet` | Space-separated labels for job assignment |
| `ludus_jenkins_agent_workdir` | `C:\Jenkins` | Agent working directory |
| `ludus_jenkins_agent_user` | `jenkins-agent` | Local user to run the service |
| `ludus_jenkins_agent_password` | `JenkinsAgent1!` | Password for the agent user |
| `ludus_jenkins_java_version` | `21` | Java version to install |

## Required Ansible Collections

- `ansible.windows`
- `community.windows`
- `chocolatey.chocolatey`

## Example (Ludus range config)

```yaml
- vm_name: '{{ range_id }}-runner-win01'
  hostname: '{{ range_id }}-runner-win01'
  template: win2022-server-x64-template
  vlan: 99
  ip_last_octet: 7
  ram_gb: 8
  cpus: 4
  windows: {}
  roles:
    - whispergate.ludus_jenkins_agent_windows
    - whispergate.ludus_vs_build_tools
  role_vars:
    ludus_jenkins_url: 'http://10.{{ range_number }}.99.3:8080'
    ludus_jenkins_agent_name: runner-win01
    ludus_jenkins_agent_labels: 'windows-msvc windows-dotnet'
```

## License

BSD-2-Clause
