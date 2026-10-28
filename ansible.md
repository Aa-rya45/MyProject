Step 0: Install Ubuntu (If Not Installed)
1. Open Windows PowerShell as Administrator.
2. Check whether Ubuntu is installed:
wsl -l -v
Expected Output (if installed):
NAME       STATE       VERSION
Ubuntu     Stopped     2

3. If Ubuntu is not installed, run:
wsl --install -d Ubuntu
Expected Output:
Installing: Ubuntu
The distribution has been installed successfully.
Restart Windows if prompted.

4. Open Ubuntu from the Windows Start menu.
Create a username and password if prompted.
Note: If Ubuntu is already installed, skip Step 0. If using a Linux computer, open Terminal directly.

Step 1: Update Ubuntu Packages


Command:

1.sudo apt update

Expected Output:

Reading package lists... Done
Building dependency tree... Done
Reading state information... Done

Explanation: Updates the list of available software packages.

Step 2: Install Ansible

Command:

sudo apt install ansible -y

Expected Output:

Setting up ansible ...

If already installed:

ansible is already the newest version.

Explanation: Installs Ansible on Ubuntu.

Step 3: Check Ansible Version

Command:

ansible --version

Expected Output (on practice laptop):

ansible [core 2.20.1]

Additional Python and configuration details will also appear.

Explanation: Verifies that Ansible is installed successfully.

Step 4: Test Ansible on Localhost

Command:

ansible localhost -i localhost, -m ping -c local

Expected Output:

localhost | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

Explanation: Checks whether Ansible can communicate with the localhost machine.

Step 5: Create Ansible Playbook

Command:

nano localhost.yml

Paste the following YAML code:

---
- name: Software provisioning on localhost
  hosts: localhost
  connection: local
  become: true

  tasks:
    - name: Update package cache
      apt:
        update_cache: yes

    - name: Install tree package
      apt:
        name: tree
        state: present

    - name: Verify tree installation
      command: tree --version
      register: tree_version
      changed_when: false

    - name: Display tree version
      debug:
        msg: "{{ tree_version.stdout }}"

Save the file:

Press Ctrl + O.

Press Enter.

Press Ctrl + X.

Explanation: Creates an Ansible playbook to update the package cache, install the tree package, verify installation, and display its version.

Step 6: Check Playbook Syntax (Optional)

Command:

ansible-playbook localhost.yml --syntax-check

Expected Output:

playbook: localhost.yml

Explanation: Checks whether the YAML playbook has valid syntax.

Step 7: Execute Ansible Playbook

Command:

ansible-playbook localhost.yml --ask-become-pass

Expected Prompt:

BECOME password:

Enter your Ubuntu password and press Enter.

Expected Output:

PLAY [Software provisioning on localhost]

TASK [Gathering Facts]
ok: [localhost]

TASK [Update package cache]
ok: [localhost]

TASK [Install tree package]
ok: [localhost]

TASK [Verify tree installation]
ok: [localhost]

TASK [Display tree version]
ok: [localhost]

PLAY RECAP
localhost : ok=5 changed=0 unreachable=0 failed=0

Note: On the first execution, some tasks may display changed instead of ok, and the final changed count may be greater than zero.

Explanation: Executes the Ansible playbook to provision and configure software on localhost.

Step 8: Verify Tree Installation

Command:

tree --version

Expected Output (on practice laptop):

tree v2.3.1

Explanation: Confirms that the tree package is installed successfully.

Step 9: Display Saved Playbook (Optional)

Command:

cat localhost.yml

Expected Output: Displays the complete YAML code saved in the localhost.yml file.

Troubleshooting (Only If Required)

Problem 1: Ubuntu Cannot Connect to the Internet

Check connectivity:

ping -c 4 8.8.8.8

Check DNS resolution:

getent hosts archive.ubuntu.com

If WSL networking is not working, close Ubuntu and open Windows PowerShell.

Run:

wsl --shutdown

Reopen Ubuntu and test the connection again.

Problem 2: Forgot Ubuntu Password

Open PowerShell and check the distribution name:

wsl -l -v

If the distribution is named Ubuntu, run:

wsl -d Ubuntu -u root

Check your Linux username if necessary:

ls /home

Reset the password, replacing username with your actual Ubuntu username:

passwd username

Enter and confirm the new password.

Exit:

exit

Reopen Ubuntu normally.

Quick Revision: All Main Commands

sudo apt update
sudo apt install ansible -y
ansible --version
ansible localhost -i localhost, -m ping -c local
nano localhost.yml
ansible-playbook localhost.yml --syntax-check
ansible-playbook localhost.yml --ask-become-pass
tree --version
cat localhost.yml
