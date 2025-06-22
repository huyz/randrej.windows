# Ansible Collection - randrej.windows

An odds-and-ends collection of PS-based modules for Windows, created mostly to
host `win_blockinfile`.


## Modules

### `win_blockinfile`

A Windows work-a-like of of `ansible.builtin.blockinfile` developed by [Marcus
Watkins](https://github.com/marwatk) under GPL3, in a Github PR to Ansible.
For some reason, that
[PR](https://github.com/ansible/ansible/pull/52586) was declined (even though
it was high quality), and wasn't later included in `community.windows`.
The author didn't create a collection for it.

#### Example Usage

Here's an example of how to use it to do something akin to a
`win_authorized_key` module in order to give you password-less SSH access
to a Windows host running
[Win32-OpenSSH](https://github.com/powershell/Win32-OpenSSH):

```yaml
    - name: WIN_INBLOCKFILE | Add my public key to remote host's authorized_keys
      randrej.windows.win_blockinfile:
        dest: '%ProgramData%\ssh\administrators_authorized_keys'
        create: true
        marker: "# {mark} ANSIBLE MANAGED BLOCK (controller's key)"
        block: |-
          {{ lookup('ansible.builtin.file', '~/.ssh/id_ed25519.pub') }}
      become: true

    - name: WIN_SHELL | Ensure permissions of administrators_authorized_keys
      ansible.windows.win_powershell:
        script: |
          if (Test-Path "C:\ProgramData\ssh\administrators_authorized_keys") {
            $acl = Get-Acl C:\ProgramData\ssh\administrators_authorized_keys
            $acl.SetAccessRuleProtection($true, $false)
            $administratorsRule = New-Object system.security.accesscontrol.filesystemaccessrule("Administrators","FullControl","Allow")
            $systemRule = New-Object system.security.accesscontrol.filesystemaccessrule("SYSTEM","FullControl","Allow")
            $acl.SetAccessRule($administratorsRule)
            $acl.SetAccessRule($systemRule)
            $acl | Set-Acl
            Write-Output "Permissions updated successfully"
          } else {
            Write-Error "File does not exist"
          }
      changed_when: false
```
