---
pdf: true
covers:
  front: ./docs/assets/pdf/ansible-core-cover.html.j2
---

# 1 - Check the Prerequisites

## Objective

* Understand the lab topology and how to access the environment.
* Understand how to work the workshop exercises
* Understand challenge labs

These first few lab exercises will be exploring the command-line utilities of the Ansible Automation Platform.  This includes

* [ansible-core](https://docs.ansible.com/core.html){:target="_blank"} - the base executable that provides the framework, language and functions that underpin the Ansible Automation Platform.  It also includes various cli tools like `ansible`, `ansible-playbook` and `ansible-doc`.  Ansible Core acts as the bridge between the upstream community with the free and open source Ansible and connects it to the downstream enterprise automation offering from Red Hat, the Ansible Automation Platform.
* [ansible-navigator](https://github.com/ansible/ansible-navigator){:target="_blank"} - a command line utility and text-based user interface (TUI) for running and developing Ansible automation content.
* [Execution Environments](https://docs.ansible.com/automation-controller/latest/html/userguide/execution_environments.html){:target="_blank"} - not specifically covered in this workshop (day 1) because the built-in Ansible Execution Environments already included all the Red Hat supported collections which includes all the collections we use for this workshop.  Execution Environments are container images that can be utilized as Ansible execution.
* [ansible-builder](https://github.com/ansible/ansible-builder){:target="_blank"} - not specifically covered in this workshop, `ansible-builder` is a command line utility to automate the process of building Execution Environments.

If you need more information on new Ansible Automation Platform components bookmark this landing page [https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4](https://docs.redhat.com/en/documentation/red_hat_ansible_automation_platform/2.4){:target="_blank"}

We will be using especially the *ansible-core* executable and the CLI tools it provides, as currently (Q1/2023) it is still the main interface to interact with Ansible.

In the (near) future this will be replaced/supplemented by the *Ansible Navigator*, which on the one hand brings more useful additional features and in the end serves a much greater purpose than just be a drop in replacement or alias to the currently used Ansible utilities. It requires a broader introduction and explanation regarding the use of containers and collections, which we will discuss on workshop day 3.  
Still, although we will be using the *ansible-core* executable in all exercises, it is shown how to also achieve everything using the `ansible-navigator` utility in a separate tab.

=== "Ansible"

    ``` { .console .no-copy }
    [student@ansible-1 ~]$ ansible-playbook --help
    usage: ansible-playbook [-h] [--version] [-v] [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT] [--ssh-common-args SSH_COMMON_ARGS]
                            [--sftp-extra-args SFTP_EXTRA_ARGS] [--scp-extra-args SCP_EXTRA_ARGS] [--ssh-extra-args SSH_EXTRA_ARGS]
                            [-k | --connection-password-file CONNECTION_PASSWORD_FILE] [--force-handlers] [--flush-cache] [-b] [--become-method BECOME_METHOD] [--become-user BECOME_USER]
                            [-K | --become-password-file BECOME_PASSWORD_FILE] [-t TAGS] [--skip-tags SKIP_TAGS] [-C] [--syntax-check] [-D] [-i INVENTORY] [--list-hosts] [-l SUBSET]
                            [-e EXTRA_VARS] [--vault-id VAULT_IDS] [--ask-vault-password | --vault-password-file VAULT_PASSWORD_FILES] [-f FORKS] [-M MODULE_PATH] [--list-tasks]
                            [--list-tags] [--step] [--start-at-task START_AT_TASK]
                            playbook [playbook ...]

    Runs Ansible playbooks, executing the defined tasks on the targeted hosts.

    positional arguments:
      playbook              Playbook(s)

    optional arguments:

    [...]
    ```

=== "Navigator"

    !!! note
        By default, the *ansible-navigator* will start in the *interactive* mode. This will launch the navigator in the *Text-based User Interface* (TUI) mode.  
        If you want to use the tool the same way as with the normal *ansible-playbook* command, provide the parameter `-m stdout` which forces the output to the command-line (though, this prevents you from experiencing the cool features of the navigator).

    ``` { .console .no-copy }
    [student@ansible-1 ~]$ ansible-navigator --help
    usage: ansible-navigator [-h] [--version] [--rad ANSIBLE_RUNNER_ARTIFACT_DIR] [--rac ANSIBLE_RUNNER_ROTATE_ARTIFACTS_COUNT] [--rt ANSIBLE_RUNNER_TIMEOUT]
                            [--cdcp COLLECTION_DOC_CACHE_PATH] [--ce CONTAINER_ENGINE] [--co CONTAINER_OPTIONS [CONTAINER_OPTIONS ...]] [--dc DISPLAY_COLOR] [--ecmd EDITOR_COMMAND]
                            [--econ EDITOR_CONSOLE] [--ee EXECUTION_ENVIRONMENT] [--eei EXECUTION_ENVIRONMENT_IMAGE]
                            [--eev EXECUTION_ENVIRONMENT_VOLUME_MOUNTS [EXECUTION_ENVIRONMENT_VOLUME_MOUNTS ...]] [--la LOG_APPEND] [--lf LOG_FILE] [--ll LOG_LEVEL] [-m MODE] [--osc4 OSC4]
                            [--penv PASS_ENVIRONMENT_VARIABLE [PASS_ENVIRONMENT_VARIABLE ...]] [--pp PULL_POLICY] [--senv SET_ENVIRONMENT_VARIABLE [SET_ENVIRONMENT_VARIABLE ...]]
                            {subcommand} --help ...

    optional arguments:

    [...]
    ```

!!! info
    Although the tab is titled **Ansible**, this can be any of the *classic* utilities provided by the *ansible-core* executable. The **Navigator** tab uses the same utils but acts as an abstraction layer.

## Guide

### Your Lab Environment

In this lab you work in a pre-configured lab environment. You will have access to the following hosts:

| Role                 | Inventory name |
| ---------------------| ---------------|
| Ansible Control Host | ansible-1      |
| Managed Host 1       | node1          |
| Managed Host 2       | node2          |
| Managed Host 3       | node3          |

Every host is reachable via SSH.

### Step 1 - Access the Environment

Connect to Visual Studio Code from the Workshop launch page (provided by your instructor).  The password is provided below the WebUI link.

  ![launch page](images/launch_page.png)

Type in the provided password to connect.

  ![login vs code](images/vscode_login.png)

In the menu bar of VS Code, click on `File` &#8594; `Open Folder...` and open your *home directory* in Visual Studio Code (this folder should already be shown in the *Open Folder* pop-up and called `/home/student`. This will reload your browser window.

  ![open folder vs code](images/vscode_open_folder.png)

### Step 2 - Using the Terminal

Open a terminal in Visual Studio Code:

  ![picture of new terminal](images/vscode_new_terminal.png)

Navigate to the `lab_inventory` directory on the Ansible control node terminal.

``` { .console .no-copy }
[student@ansible-1 ~]$ cd lab_inventory
[student@ansible-1 lab_inventory]$ pwd
/home/student/lab_inventory
[student@ansible-1 lab_inventory]$ ls
[student@ansible-1 lab_inventory]$
hosts
[student@ansible-1 lab_inventory]$ cd ..
[student@ansible-1 ~]$
```

* `~` - the tilde in this context is a shortcut for the home directory, i.e. `/home/student`
* `cd` - Linux command to change directory
* `pwd` - Linux command for print working directory.  This will show the full path to the current working directory.

### Step 3 - Challenge Labs

You will soon discover that many chapters in this lab guide come with a "Challenge Lab" section. These labs are meant to give you a small task to solve using what you have learned so far. The solution of the task is shown underneath a warning sign.
