# Ansible Collection - aradovic.windows

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
