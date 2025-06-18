
# Ansible

## ssh basic requirements
```bash
sudo ufw allow ssh
sudo ufw enable
sudo ufw status
sudo apt install openssh-server               # this should be installed on target server
sudo systemctl status ssh
```

### fingerprint
+ An `SSH fingerprint` is a short, unique hash of a server’s public SSH key.
+ When you connect to an SSH server for the first time, your client gets the server’s public key.
+ SSH stores its public key fingerprint in a file called: `~/.ssh/known_hosts`
+ The fingerprint helps verify that you’re connecting to the correct server, not to an attacker
+ after this command we get `ssh fingerprint error`.
+ two solutions: 1. ssh to this ip before ansible comamnd (recommend) 2. "vi /etc/ansible/ansible.cfg" and set'host_key_checking' to False

```bash
```
## Install Ansible

```bash
# pipx is for installing Python CLI applications, its not for libraries and frameworks
pipx install --include-deps ansible                  # install full package
pipx install ansible-core                            # install minimal version
```

## Invenroty
+ Inventory file defines the hosts and groups of hosts that your playbooks or ad-hoc commands will manage
+ we can write "dynamic inventory file" --> `inventory.py` or "static inventory" ---> `inventory.txt`
+ default format for inventory file is `INI` but it also supprts yaml and txt.
+ default location: `/etc/ansible/hosts` but we can specify out file with -i flag in commands


```bash
# syntax for .txt or .ini       # syntax for yaml file is diff
[db]                           # we can group servers by this syntax [group_name]
server1.company.com            # we can write just hostname with no parameters
server2.company.com            # or we can specify an alias for the hostname

[server]
target1 absible_host=server2.company.com               # target1 is an alias for host_name
target2 absible_host=192.168.0.109                        # ansible_host is called a "parameter" or a variable
# parameters like ansible_port, ansible_connection, asnible_user, ansible_ssh_pass
target3  absible_host=192.168.0.109 ansible_connection=ssh    # how connect to the server like: winrm to windows_server
target3  absible_host=192.168.0.109 ansible_port=2222         # defult port is 22 but we can change it
target3  absible_host=192.168.0.109 ansible_user=admin         # default user is root
target3  absible_host=192.168.0.109 ansible_ssh_pass=123         # dont use this option, its not secure
mylocal ansible_connection=localhost                            # we can also set connection to localhost
```



## ad-hoc commands
+ An-hoc commands are one-line commands used to perform quick tasks on remote hosts without writing a playbook

```bash
cat /etc/ansible/hosts              # defautl location for inventory file we can change it with -i flag in ad-hoc commadns
ansible target1 -m ping -i inventory.txt            # -m = module #-i = inventory file # target1 is host_name # check the connection
ansible target1 -m shell -a "ls -l" -i inventory.txt              # -a for argument
ansible target2 -m apt -a "name=nginx state=present"            # multiple arguments        # install a package
ansible target4 -m apt -a "name=nginx state=present" -b         # -b = become sudo    # sudo apt install nginx
ansible target3 -m service -a "name=nginx state=restarted"     # restart a service
ansible all -m ping -t inventory.txt                # all is built-in group_name # we can use group_name   # ping all hosts
ansible target* -m ping -i inventory.txt              # target* means target1, target2, ....
```

## ansible commands
```bash
ansible all -m ping                                  # "ansible" runs an ad-hoc command
ansible-playbook site.yml -i inventory.txt          # ansible-playbook runs a playbook
ansible-playbook site.yml -i inventory.txt -v          # -v for verbose mode (some commnds with output like "ls" does not show without -v)
ansible-playbook site.yml -i inventory.txt -vvv          # see full execution logs
ansible-inventory --list -i inventory.ini            # display inventory file in json format in terminal
ansible-inventory --graph -i inventory.ini            # display inventory hosts like ini format
ansible-doc apt	                                      # Show help/documentation for module
ansible-doc -s apt                                # Show options of apt module (short format)
ansible-doc -l                                          # list available modules
ansible-doc -t callback -l                          #  list available callback plugins
ansible-config list                                  # list all config options
ansible-config dump                                 # dump all config options
ansible-galaxy install geerlingguy.nginx            # install a package (Ansible roles and collections)
ansible-vault encrypt secrets.yml                     # encrypt a file
ansible-vault decrypt secrets.yml                     # decrypt a file
ansible-vault edit secrets.yml                         # decrypt my file temprary in default editor for editing
ansible-console                                   # Start an interactive console for running tasks
```

### question
1. fingerprint
2. output
3. order of running
4. multiple task in one name
5. scrup module
6. with_items

### playbook
+ playbook=  a playbook is a single yaml file
+ play = defines a set of activities to run on host
+ task = a single action to run on a host
+ module = different actions run by tasks are called moduls like `command`, `script`, `service`, `yum`
+ directive = configuration parameters like hosts, vars, tasks and so on
+ idempotency = An operation is idempotent if the result of performing it once is exactly the same as the result of performing it repeatedly without any intervening actions.

```yaml
-                  # a dash is for a sinlge play      # each play has some properties like: name + hosts + tasks
    name: Play 1         # each play is a dictionary, and a playbook is a list of dictionaries
    hosts: localhost    # hosts come from inventory file  # hosts: localhost, group_name, host_name, patterns like *.company.com
    vars:              # we can define variables in yaml file
        dns_server: 10.1.250.10                  # this is a variable
    tasks:                                        # tasks is a ordered list and order is important
        - name: Execute an echo command          # command module is similar to shell module
          command: echo "{{dns_server}}"                      # use "{{}}" for variables which is called jinja2 templating
       
        - name: List open ports                               # command module does not support (|,>, >>) and shell variables
          shell: netstat -tuln | grep LISTEN                # shell is for runing inline shell commands directly in the playbook

        - name: Execute a script
          script: myscript.sh                # script module is for uploading and executing a local script file on the target

        - name: echo a message
          debug:                             # debug module is for print a message 
            msg: "this is a debug message"
        
        - name: a simple ping
          ping:                                # ping does not need any attribure

        - name: Install python
          apt: name={{item}} state=installed         # replace each value of with_items with {{item}}
          with_items:
              - python
              - python-pip

        - name: Install python modeuls
          pip:                                        # pip module
            name: flask
            state: present                             # state:'latest' to always get newest version
        
        - name: create db
          mysql_db: name=employees state=present

        - name: create database user 
          mysql_user:
            name: root
            password: root
            database: employees
            privileges: ["ALL"]

        - name: Start a web server
          service:                               # sudo systemctl start httpd
            name: httpd                        # httpd for debian-based is apache2
            state: started                 # sudo systemctl enable httpd
            enabled: yes                   # when the server restart, the service will automatically start


        - name: Install something
          yum:
            name: httpd
            state: present
          when: ansible_os_family =="RedHat" and          # conditional
                ansible_distribution_version == '16.4'

        - name: Copy index.html to web server            # use scp for copy a file from local to ansible controle 
          copy:                          # scp alternative for targets
            src: files/index.html       # Path on your Ansible control machine
            dest: /var/www/html/index.html    # path on ansible target
            owner: root
            group: root
            mode: '0644'

        - name: Reboot the server
          reboot:
            reboot_timeout: 300
```

### Task formats (syntax)

```bash
tasks:
    - apt: pkg=vim state=present           # 1. one-line command
    - apt:                                # 2. multi-line command (map)
        pkg: vim
        state: present
    - apt: >                                # 3. Foldable scalar
        pkg=vim
        state=present
```

### Output format

### variables in tasks

```yaml
- name: Example with vars
  hosts: all
  vars:
    username: "alice"
    port: 8080
  tasks:
    - name: Print variables                                         # {{}} called jinja2 templating
      command: echo "User is {{ username }} on port {{ port }}"
```

### jinja2 templating and filters
+ there are many filters in python jinja2 builtin
+ we can find lots of filters in ansible filters doc

```yaml
tasks:                         # lower, uppser, title, replace, min, max and so on are filters
command: echo "User is {{ username| upper }}"      # convert username to uppercase: ALICE
command: echo "User is {{ username| lower }}"      # convert username to lowercase: alice
command: echo "User is {{ username| title }}"      # convert username to titlecase: Alice
command: echo "User is {{ username| replace('foo', 'bar') }}"      # replace foo with bar
command: echo "User is {{ username| default('bar') }}"      # if username is empty or is not defined, use 'bar'
command: echo "User is {{ username| default(alireza) | upper | replace('A', '4') }}"      # multiple filters
command: echo "{{ [1, 2, 3] | min }}"      # returns 1    # max   # we can define a list instead of variable in jinja2
command: echo "{{ [1, 2, 3, 3, 3] | unique }}"      # returns 1, 2, 3
command: echo "{{ [1, 2, 3, 4] | union[4,5] }}"      # returns 1, 2, 3, 4, 5
command: echo "{{ [1, 2, 3, 4] | intersect[4,5] }}"      # returns 4
command: echo "{{ 100 | random }}"      # retunrs 37
command: echo "{{ ['a', 'b', 'c'] | join('') }}"      # returns abc  # string method
command: echo "{{ 'hello world' | split(' ') }}"      # returns ['hello', 'world']
command: echo "{{ '/etc/hosts' | basename }}"      # returns "hosts"          # file filters
command: echo "{{ 'c:\windows\myfoleder' | win_basename }}"      # returns "myfoleder"
```


### register
```yaml
- name: Example of register
  hosts: localhost
  tasks:
    - name: Run a command and register its output
      command: ls -l /tmp                  # by register we can capture the output of a task into a variable
      register: result                        # Saves the command output in a variable called result

    - name: Show the result
      debug:
        var: result.stdout             # result.stderr, result.rc (return code in integer), result.stdout_lines (output as a list)
```

### Conditions

##### 1. simple condition
```yaml
tasks:
  - name: Shutdown CentOS systems
    command: /sbin/shutdown -t now                   # run this command if condition is true
    when: ansible_facts['distribution'] == "CentOS"
```

##### 2. multiple conditions
```yaml
tasks:
  - name: Shut down CentOS 6 systems
    ansible.builtin.command: /sbin/shutdown -t now        # list means "AND"
    when:            # When you have multiple conditions that all need to be true we can specify them as a list
      - ansible_facts['distribution'] == "CentOS"
      - ansible_facts['distribution_major_version'] == "6"
    when: (ansible_facts['distribution'] == "CentOS") or (ansible_facts['distribution_major_version'] == "6")   # OR
```

##### 3. Regex_search in condition

```yaml
- name: Check if hostname matches pattern             # fully name: ansible.builtin.regex_search
  debug:
    msg: "Hostname matches web pattern"
  when: inventory_hostname | regex_search('web.*')
```

##### 4. condition to previous command

```yaml
- name: Check status of a service and email if it's down
  hosts: localhost
  tasks:
    - name: Check httpd service status
      command: service httpd status      # result is a variable
      register: result                  # save the output of a task useing "register"
      ignore_errors: yes                # Prevents task failure if service is not found/stopped

    - name: Send email alert if httpd is down     # send this email by condition
      mail:                              # send an email with this body and subject
        to: admin@company.com
        subject: "Service Alert"
        body: "Httpd Service is down"              # send this email if previous command gets error
      when: result.stdout.find('down') != -1        # when there is not 'down' in result, means the result is good
```


### Loops
+ we can use `loop` or `with_items` (in older versions) or `with_dict` for creating loops in yaml file
+ also there are other iterators like `with_files`, `with_url`(connect to urls), `with_mongodb`(connect to dbs)
+ `with-*` is a `look up plugin`
+ `item` keyword is commonly used in loops to reference the current item being processed.

##### 0. with_items

```yaml
- name: simple with_items
  shell: echo 1                      # run "echo 1" 3 times
  with_items:                        # we access the values of with_items by {{}}
    - foo
    - bar
    - nginx
```

##### 1. Loop over an array
```yaml
tasks:
  - name: Install multiple packages
    apt:
      name: "{{ item }}"
      state: present
    loop:                     # inside loop we defined an array of string
      - nginx
      - postgresql
      - redis
```

##### 2. Loop over an array of dictionaries

```yaml
tasks:
-
 name: Create users
 tasks:
    - user: name="{{item.name}}" state=present             # instead of duplicating this line we can use loop
    - user: name="{{item}}" state=present             # if we had an array of strings
      loop:                                        # loop or with_items in older versions
        - name: ali                              # loop is an array of dictionaries
          id: 12
        - name: mamad
          id: 13
```

##### 3. Accessing Item Index

```yaml
tasks:
  - name: Create files with index numbers
    file:                                      # loop.index in older versions
      path: "/tmp/file{{ ansible_loop.index }}"
      state: touch
    loop:
      - file1
      - file2
      - file3
    loop_control:
      index_var: ansible_loop.index
```

##### 4. Loop Control

```yaml
tasks:
  - name: Install packages
    apt:
      name: "{{ package }}"
      state: present
    loop:
      - nginx
      - postgresql
    loop_control:                # You can rename item for clarity using loop_control
      loop_var: package
```

##### 5. conditional in loops

```yaml
- name: Install Softwares
  hosts: all
  vars:
    packages:                          # packages is an array of dictionaries
      - name: nginx                      # each item should be indented under packages
        required: True
      - name: mysql
        required: True
      - name: apache
        required: False
  tasks:
    - name: Install "{{ item.name }}"
      apt:
        name: "{{ item.name }}"
        state: present
      when: item.required == True                 # Only install if required is True
      loop: "{{ packages }}"            # loop through an array
```



### Asynchronous tasks: basic timers

```yaml
tasks:
    - name: Start a web server          # by default ansible run tasks one by one
      command: service httpd start       # Just use poll with a number >0 for blocking (default is also blocking)
      async: 360                      # Max seconds to allow it to run, default is infinite
      poll: 60                    # how frequently check the status, defult is 10 seconds
      poll: 0                 # zero means fire-and-forget, don’t wait . Useful when you don’t care about the result
```

### Asynchronous tasks: Start, then Check Later 

```yaml
- name: Start task, check later
  shell: longtask.sh
  async: 300
  poll: 0
  register: long_job

- name: Wait and check the status                   # we need a new task for chenking later
  async_status:                                  # async_status is a module used to check async jobs and jid(job id) is a parameter for that
    jid: "{{ long_job.ansible_job_id }}"
  register: result                         #  Saves result of this status check
  until: result.finished                 # Loop until the job is marked 'finished'
  retries: 30                            # try up to 30 times
  delay: 5                                 # wait 5 seconds between tries
```


### strategy
1. `strategy` defines `execution flow control` and how tasks are executed across hosts during a play.
2. `linear` (default): Runs each task on all hosts (controlled by `forks`) before moving to the next task.
3. `free`: Each host runs independently; does not wait for others
4. `host_pinned` or `serial`: Similar to free, but limits concurrent tasks per host (uses `serial` instead of `strategy`).
5. `debug`: Interactive debugging (asks for user input on failures).
6. `ansible forks`: a number in `ansible.cfg` file which defines the maximum parallel host connections (default is 5)


```yaml
- hosts: all         # see animations in youtube to understand better
  strategy: free  # Overrides default 'linear'
  serial: 3         # Overrides default '1'   # we can use percentage or number
  tasks:
    - name: Run tasks as fast as possible
      command: /bin/some_task
```

### Error handling
+ if we have one server and we get error it exits the playbook
+ if we have multiple servers and we get error in one server, ansible takes that server out of the list and continues to execute tasks across other healthy servers and tries to complete as many servers as posible 
+ `ignore_errors: yes`: Ignores errors for a specific task and proceeds to the next task (dont skip the server)
+ `any_errors_fatal: true`: if we get error in one of the servers, all the servers will stop the execution all together.
+ `failed_when`: usually used with `ignore_errors: yes`. we can define a condition

```yaml
- name: Check disk space                     # fail the playbook if the condition is true
  command: df -h /                            # if the condition is not true continue as normal
  register: disk_result
  failed_when: "'100%' in disk_result.stdout"
```

### Async handling
+ Suppose you run a script that might update something, but it doesn't return a specific exit code to indicate change. You want to mark the task as changed only if the word updated appears in the output.

```yaml
- name: Run script
  command: ./my_script.sh
  register: script_out
  changed_when: "'Updated' in script_out.stdout"
```

### Try-catch

```yaml
- name: Example of block, rescue, always
  block:
    - name: Attempt risky task
      command: /bin/false

  rescue:
    - name: Handle failure
      debug:
        msg: "The task failed, running rescue actions"

  always:
    - name: Always do this
      debug:
        msg: "This will always run, regardless of success or failure"
```


### File separation

##### 1. Minimal Flat Structure (Good for Small Projects)

```css
my-ansible-project/
├── inventory.ini
├── playbook.yml
├── tasks\
│   └── deploy_app.yml                  /* reuse tasks that are related to app */
│   └── deploy_db.yml                   /* reuse tasks that are related to db */
│
├── host_vars\                       /* variables that are specific to hosts */
│    └── db_servers.yml               
├── group_vars\                      # 
│    └── web_servers.yml             # 
│   
├── templates/
│   └── index.html.j2
├── files/
│   └── somefile.conf
```

##### 0. file variables

+ `host_vars` and `group_vars` are both folders for variables
+ `host_vars` is for one host	and `group_vars` store variables shared by a group of hosts
+ `host_vars/<hostname>.yml` applies only to that single host
+ `group_vars/<group_name>.yml` applies to all hosts in that group ---> [group_name] in inventory file

```yaml
# host_vars/db_servers.yml
db_name: employees              # there is no need for including this file into playbook
db_user: root                   # just use key:value pair

# playbook.yml
name: Deploy app
hosts: db_servers            # variables in host_vars/db_servers will automatically import for this host 
tasks:
    - name: echo a var
      command: echo {{db_name}}

```
##### 0. task files

```yaml
#### tasks/depoly_db.yml
- name: deploy db
  command: echo "something"

### playbook.yml
name:
hosts:
tasks:
    - include: deploy_db.yml
    - include: deploy_app.yml
```

##### 2. Standard Role-based Structure (Good for Large Projects)
```css
site.yml
inventory/
├── hosts
roles/
└── webserver/
    ├── tasks/
    │   └── main.yml
    ├── handlers/
    │   └── main.yml
    ├── templates/
    │   └── index.html.j2
    ├── files/
    │   └── httpd.conf
    ├── vars/
    │   └── main.yml
    ├── defaults/
    │   └── main.yml
    ├── meta/
    │   └── main.yml
```



### moduels  (based on functionality)
+ A module is a reusable, standalone script

1. `System modules`: 1. user 2. group 3. hostname 4. ping 6. iptables
2. `service modules`: 1. service 2. systemd
3. `Command modules` : 1. command 2. script 3. shell 4. raw 5. expect
4. `File modules`: 1. copy 2. file 3. find 4. stat 5. archive
5. `Database modules`: 1. mysql 2. postgresql
6. `Cloud modules`: 1. docker 2. vmware 3. azure
7. `Windows modules`: 1. win_copy 2. win_command 3. win_file 4. win_ping


###### free_form parameter
+ free_form can be used in ad-hoc commands and playbook yaml file
+ free_form indicates that this module takes no parameter options like `cat 1.txt` or `mkdir /folder` are "free_form input"
+ free_form is inside `parameter category` for `command module` but there is no parameter actually named `free_form` like `chdir`
+ `copy` takes "parameterised input" and its not `free_form` since it requires two parameters: 1. src 2. dest
+ `copy` ad-hoc command: `ansible all -m copy -a "src=/local/path dest=/remote/path"`



##### 1. command execution
```yaml
tasks:
# command module parameters= `chdir`, `creates`,`removes`(when it exists run), `executable`(change shell), free_form
      command: cat 1.txt chdir=/etc               # "chdir" means change directory before executing cat  == cat /etc/1.txt
      command: mkdir /folder creates=/folder      # "creates" means check if that folder deos not exists creats it
      shell: "echo $HOME"	                      # shell is more powerful than command module
      raw: yum install -y nginx	                # Run raw SSH command
      script: ./app/script.sh     # # script module copy the file into remote machine by default and then runs it
```


##### 2. Package Management

```yaml
tasks:
    apt: name=nginx state=present	       # Install a package (Debian/Ubuntu)
    yum: name=httpd state=latest	        # Install a package (RHEL/CentOS)
    dnf: name=vim state=present	            # Install a package (Fedora/RHEL 8+)
    pip: name=requests state=present	           # Install Python package	
    package: name=curl state=latest	          # OS-independent package management	
```

##### 3. File Management
```yaml
tasks:
    file: path=/tmp/test state=directory mode=0755	                # Create directory with permissions
    copy: src=./file.txt dest=/tmp/file.txt	                        # Copy file from local controller to remote target
    template: src=template.j2 dest=/etc/app/config.cfg	            # Copy Jinja2 template to remote
    fetch: src=/etc/hosts dest=./hosts flat=yes	                   # Download remote file to local
    lineinfile: path=/etc/sysctl.conf line="vm.swappiness=10"	      # # search for a line in a file and replace it or add it if it does not exist
    blockinfile: path=/etc/config block="..."	                   # Insert block of text into file
```

##### 4. User Management
```yaml
tasks:
    user: name=john state=present groups=sudo	                # Add or modify a user     # state: present: Ensures the user exists
    group: name=developers state=present                            # Create group
    authorized_key: user=john key="{{ lookup('file', 'id_rsa.pub') }}"  # Add SSH key for user
# state=absent: Ensures the user is removed, state=present: Ensures the user exists
```


##### 5. Service Management
```yaml
tasks:
    service: name=postgresql state=started           # service module
    systemd: name=nginx state=started enabled=yes	          # systemd module
    systemd: name=nginx state=stopped 	                # stop service    # started, stopped, restarted, reloaded
    service:                                       # multi-line syntax
        name: postgresql                        # name and state are properties of service
        state: started          # stated means if psql is not started, please start it and if it started do nothing 
        enabled: yes            # Enabling service at boot automatically
```

##### 6. Network Management
```yaml
tasks:
    uri: url=https://api.example.com return_content=yes	    # Make HTTP request (GET, POST)
    firewalld: service=http permanent=yes state=enabled immediate=yes	# Manage firewalld firewall
    ufw: rule=allow port=22 proto=tcp	                 # Manage UFW firewall
```

##### 7. database management
```yaml
mysql_user: name=foo password=bar priv=*.*:ALL	           # Manage MySQL users
postgresql_db: name=mydb state=present	                  # Manage PostgreSQL DB
```

##### 8. Utilities
```yaml
tasks:
    set_fact: my_var=42	                 # Set a variable/fact
    debug: var=my_var	                # Print debug information
    pause: seconds=5	              # Pause execution
```



### Ansible roles
+ [ansible-galaxy](https://galaxy.ansible.com/ui/)
+ roles are Reusable Ansible components
+ we can create a role without `ansible-galaxy init` command, by creating a simple file directory manually

##### 1. ansible roles commands
```bash
ansible-galaxy init myrole    # cd roles/ before this          # create a role from scratch (creates a standard directory)
ansible-galaxy search mysql              # search in repo of ansible-galaxy
ansible-galaxy install user.role_name              # install a package
ansible-galaxy install geerlingy.mysql -p ./roles              # install it in current directory
ansible-galaxy remove user.role_name              # remove
ansible-galaxy list                             # list installed roles
ansible-galaxy import myrole                # share in website
ansible-config dump
ansible-config dump | grep ROLE
```
#### 2. ansible roles directory

```bash
my_playbook\
├── playbook.yml
├── roles\
│  └── role1\             
│  └── role2\
│  └── myrole/
#################################
myrole/
├── tasks/        # Main tasks to run (required)
│   └── main.yml  # when ansible runs the playbook this file is the main that runs which is required
├── handlers/     # Handlers (optional)
│   └── main.yml
├── templates/    # Jinja2 templates (optional)
├── files/        # Static files to copy (optional)
├── vars/         # Default variables (optional)
│   └── main.yml
├── defaults/     # Default variables (lower precedence) (optional)
│   └── main.yml
├── meta/         # Metadata about author, role, dependencies (optional)
│   └── main.yml  # this information will show in ansible-galaxy page
```


##### 3. ansible roles in playbooks
```yaml
name: Config mysql
hosts: db_server                  # roles runs tasks from `my_role/tasks/main.yml`
roles:                           # we can have multiple roles and the order is important
    - my_role            # first syntax with no option
    - role: my_role      # second syntax for additional arguments
      vars:
        mysql_user_name: db_user    
```


### lookups
+ `lookups` are plugins that retrieve data from external sources and make them available for use in playbooks
+ lookup is like a function inside jinja2 template

```yaml
debug:        # first argument is plugin_name or type of file like: 'file', 'csvfile', 'env', 'ini', 'json'
    msg: "{{ lookup('file', '/path/to/file') }}"     # read a file         # second argument is the path of the file 
    msg: "{{ lookup('env', 'HOME') }}"              # read an environment variable from the system
    msg: "{{ lookup('csvfile', 'file.csv') }}"     # read a complete csv file    
    msa: "{{ lookup('csvfile', 'alice', file='path', delimiter=',', key='col1') }}"      # read sth from a csv file # returns: alice@gmail.com
    msg: "{{ lookup('pipe', 'date +%Y-%m-%d') }}"        # run a command and return the output
    msg: "{{ lookup('url', 'https://api.example.com/data') }}"          # read data from a url
    msg: "{{ lookup('password', '/passfile') }}"        # read a password from a file, if does not exist, it generate one and store it in that path
    msg: "{{ lookup('password', '/passfile length=20 chars=ascii_letters,digits,special') }}"    # Control Password Length & Character Set
```

### ansible valut
+ Ansible Vault is a command line feature that allows you to encrypt sensitive data like passwords
+ Uses AES256 encryption (strong security)

```bash
ansible-vault create file.yml	     # Creates & encrypts a new file.
ansible-vault encrypt file.yml	# Encrypts an existing file.
ansible-vault decrypt file.yml	# Decrypts a file (use with caution!).
ansible-vault view file.yml         #	Views encrypted content.
ansible-vault edit file.yml	         # Edits an encrypted file.
ansible-vault rekey file.yml	# Changes the vault password.
ansible-playbook playbook.yml -i inventory.txt                        #  if inventory.txt is encrypted it gets error
ansible-playbook playbook.yml -i inventory.txt --ask-vault-pass       #  Running Playbooks with Vault which gives a Prompt for Password 
ansible-playbook playbook.yml -i inventory.txt --vault-password-file ~/.vault_pass.txt      #  instead of prompt useing a password file (not ok)
ansible-playbook playbook.yml -i inventory.txt --vault-password-file ~/.vault_pass.py      # use python for getting password from secure location
vault_password_file = ~/.vault_pass.txt               # Auto-Loading Vault Passwords in ansible.cfg
```
### dynamic inventory

```bash
ansible-playbook playbook.yml -i inventory.txt                    # static inventory
ansible-playbook playbook.yml -i inventory.py                    # 
```


```py
# !/usr/bin/env python3               # imp
import json
def get_inventory_data():             # Get inventory data from source in this format
    return {                          # return a json
        "databases": {                # group name
            "hosts": ["db_server"],      # alias name
            "vars": {
                "ansible_ssh_pass": "#Rs45$23",
                "ansible_ssh_host": "192.168.1.1"
            }
        },
        # [databases]
        # db_server ansible_ssh_host=192.168.1.1 ansible_ssh_pass=#Rs45$23
}

if __name__ == "__main__":                    # Default main function
    inventory_data = get_inventory_data()
    print(json.dumps(inventory_data))           # json.dump()
```

##### cli args

```py
# ansible-playbook playbook.yml -i inventory.py --list             # json format of all groups
# ansible-playbook playbook.yml -i inventory.py --host db_server    # json format of specifc host 
def read_cli_args():
    global args
    parser = argparse.ArgumentParser()
    parser.add_argument('--list', action='store_true')
    parser.add_argument('--host', action='store')
    args = parser.parse_args()

# Default main function
if __name__ == "__main__":
    global args
    read_cli_args()
    inventory_data = get_inventory_data()
    if args and args.list:
        print(json.dumps(inventory_data))
    elif args.host:
        print(json.dumps({
            'meta': {'hostvars': {}}
        }))
```

### windows
+ ansible control machine can only be linux and not windows, but they can be target using `winrm`
+ windows target should have some requirements like `pywinrm`, `winrm`
+ we can prepare windows by `windows system prep` in document

### custome modules
+ for creating a custome module we have to create a python script (can also be in other languages)
+ location of built-in modules (global location): `usr/lib/python2.7/dist-packages/ansible/modules`
+ Ansible automatically finds modules in `library/` directory or via `ANSIBLE_LIBRARY` env var. example: `/library/my_custom_module.py`


```py
#!/usr/bin/python                      # shebang or hashbang for scripting languages like perl, awk, bash
try:
    import json
except ImportError:
    import simplejson as json                  # if json is not available
from ansible.module_utils.basic import AnsibleModule
import time                               # for printing time in the output

DOCUMENTATION = r''' how to use '''
EXAMPLES = r'''name: mytest'''
RETURN = r''' something '''
# tasks:
#   custome_module:                       # name of this file
#        - msg: "Hello World"              # define in AnsibleModule

def main():
    module = AnsibleModule(                     # module.params[]      # module.exit_json()         # module.fail_json()
        argument_spec = dict(                         # argument_spec for defining accepted parameters
            msg = dict(required=True, type='str')     # msg is the name of parameter in tasks section in playbook
        )
    )
    msg = module.params['msg']                # get parameter from user
    try:
        module.exit_json(changed=True, msg='%s - %s' % (time.strftime("%c"), msg))      # Successful exit with your result
    except:
        module.fail_json(msg="Error Message")         # Fail Exit
```



### Plugnis
+ Ansible uses a `plugin architecture` to enable a rich, flexible and expandable feature set.
+ Plugin is a piece of code that extends Ansible's core functionality, enabling customization and flexibility that we can create our own plugins.
+ we can create our custome plugin like `custome callback plugin` or `custome filter plugin`
+ `Modules` run on target hosts, while `plugins` run on the Ansible controller.
+ Modules are focused on performing tasks on remote systems, while plugins extend Ansible's core functionality and behaviors.

1. Action Plugins --->	Control how a module runs on the target --->	normal, command, template
2. Callback Plugins --->	Modify output or logging --->	default, json, minimal, yaml
3. Connection Plugins --->	Control how to connect to hosts (SSH, Docker) --->	ssh, docker, local
4. Lookup Plugins --->	Retrieve data from external sources --->	file, env, passwordstore
5. Inventory Plugins --->	Load hosts dynamically --->	ini, yaml, aws_ec2, gcp_compute
6. Filter Plugins --->	Transform data in Jinja2 templates --->	to_json, lower, regex_replace
7. Vars Plugins --->	Load variables from external locations --->	host_group_vars, env_var
8. Strategy Plugins --->	Control task execution behavior --->	linear, free, debug

#### custome filter plugin
+ creating a Python file in this folder: `filter_plugins/`
+ `export ANSIBLE_FILTER_PLUGINS=filter_plugins` to say where to look for my custome plugins

```py
# file path: filter_plugins/myfilters.py
def reverse_string(value):                   # filter function accepts only one argument which is input of module
    return value[::-1]

class FilterModule(object):                 # class
    def filters(self):
        return {                                     # return a dict of filter names
            'reverse': reverse_string                # name inside playbook
        }
# - debug:
#    msg: "{{ 'wolf' | reverse }}"        # output: flow
```
#### custome callback plugin
+ the defulat callback plugin for printing output is `skippy`
+ `export ANSIBLE_STDOUT_CALLBACK=json` will show the output in json format
+ `Mail` is another callback plugin which can send email
+ `Hipchat`, `Slack`, `logstash` are other callback plugins for sending notifications in these apps























