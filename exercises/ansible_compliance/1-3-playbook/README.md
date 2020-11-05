Overview
========
A playbook is where you list the steps you would like to automate into a repeatable set of **plays** and **tasks**. It is recommended that you store playbooks in a **source control management** (SCM) system so you will have version control and branches assigned.

A playbook can have multiple plays and a play can have one or multiple
tasks. The goal of a **play** is to map a group of hosts. The goal of a
**task** is to implement modules against those hosts.


Section 1: Reviewing the Playbook
=====================================================================

**Step 1:**

We will be using our playbook that includes all the ad-hoc commands we just ran, this will demonstrate what we just did, However; We can see how the individual adhoc jobs are now put into a list of tasks.

```yaml
---
- name: Playbook Demo
  hosts: web
  tasks:
  - name: ping the hosts
    ping:

  - name: Ensure Apache is installed
    yum:
      name: httpd
      state: present
    become: true

  - name: Ensure Apache is running
    service:
      name: httpd
      state: started
      enabled: true
    become: true
    
  - name: Get a list of all services
    service_facts:

  - name: Read the redhat-release file
    shell: cat /etc/redhat-release
    check_mode: no
    changed_when: false
```
This is the entire playbook

Section 2: Defining The Play
=============================

Breaking the playbook down we can see to start with the play we specify our target audience

```yaml
---
- name: Playbook Demo
  hosts: web
```
- `---` Defines the beginning of YAML

- `name: Playbook Demo` This describes our play

- `hosts: web` Defines the host group in your inventory on which this play will run against

The hosts is our target and is set to web, this is the same group name we used in the ad-host command, this value could be as global as 

```yaml
hosts: all 
```
or can be a group or node name and it can include multiple if needed. 

```yaml
hosts: node1,node2,node3
```
Section 3: Adding Tasks to a Play
====================================

Now that playbook has been defined, We need to add some tasks to get some things done.


```yaml
  tasks:
  - name: Ping the hosts
    ping:

  - name: Ensure Apache is installed
    yum:
      name: httpd
      state: present
    become: true

  - name: Ensure Apache is running
    service:
      name: httpd
      state: started
      enabled: true
    become: true
    
  - name: Get list of all services on box
    service_facts:

  - name: Read the redhat-release file
    shell: cat /etc/redhat-release
    check_mode: no
    changed_when: false
```

- `tasks:` This denotes that one or more tasks are about to be defined

- `- name:` Each task requires a name which will print to standard output when you run your playbook. Therefore, give your tasks a name that is short, sweet, and to the point

```yaml
  - name: Ping the hosts
    ping:
```
- The first task is to run the ping module this is just as we set our first adhoc command.

```yaml
  - name: ensure Apache is installed
    yum:
      name: httpd
      state: present
    become: true
```
- Here we are calling the Ansible module **`yum`** to install the Apache Web Server package, we have also stated become is set to true this will elevate our permissions upto a root account which is the same as when we checked the Enable Priviledge Escalation box in the Ad-Hoc command 

```yaml
  - name: ensure Apache is running
    service:
      name: httpd
      state: started
      enabled: true
    become: true
```

- Next we are using the ansible module **service** to start and ensure it starts on a reboot of the httpd service.

```yaml
  - name: get list of all services on box
    service_facts:
```
- Next we are getting the service facts on the box we have a module to run for this compared to our shell command to show the difference in this case

```yaml
  - name: read the redhat-release file
    shell: cat /etc/redhat-release
    check_mode: no
    changed_when: false
```
- Finally we are running the shell command to read the contents of a file as we did in our last Ad-Hoc command
There are 2 extra params added here that are new.

`check_mode: no` 
- When running in a check mode i.e check for drift or changes that would happen to a box. Ansible will not execute if a change would happen, a shell command will return as changed by default because it's not idempotent all it knows is you executed it. Say the command is 'rm -Rf /home'
That could be devestating and so check mode has a safety net which we must override if needed by adding this flag.

`changed_when: false`
- Ansible will always say a shell or command module made a change regardless again due to the nature of it, and if we dont want to see a change when running a read command etc this is a simple flag to override that output.



> **Note**
>
> Ansible (well, YAML really) can be a bit particular about formatting
> especially around indentation/spacing. When you get back to the
> office, read up on this [YAML
> Syntax](http://docs.ansible.com/ansible/YAMLSyntax.html) a bit more
> and it will save you some headaches later.