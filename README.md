##ansible_fileglob_to_dict

This is an Ansible lookup plugin that will allow you to easily fileglob a directory and cleanly iterate over the response dictionaries. 


####Installation

Put the `fileglob_to_dict` file wherever you are storing your lookup plugins.  If you haven't already specified a location, try the following steps.

Place the following in your ansible.cfg or ~/.ansible.cfg to specify an include path:

    lookup_plugins = ~/my_ansible/plugins/lookup_plugins:/usr/share/ansible_plugins/lookup_plugins

Go to your Ansible root directory, make a `plugins` directory, then a `lookup_plugins` directory, and then copy fileglob_to_dict.py to this new location so that it will get picked up by Ansible runs.

e.g. 

    mkdir ~/my_ansible/plugins/lookup_plugins/
    cd ~/my_ansible/plugins/lookup_plugins/
    curl -O https://raw.githubusercontent.com/tfishersp/ansible_fileglob_to_dict/master/fileglob_to_dict.py
    
        
####Example usage:

Create a directory of JSON or YAML files that you'll target with a glob:

Files:

`roles/users/files/ops/tfisher`:

    {
        "username": "tfisher",
        "comment": "Tristan Fisher",
        "uid": "1005",
        "groups": "ops",
        "sudo": "True",
        "ssh_public_key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAA..."
    }
    
`roles/users/files/ops/tfisher`:

    {
        "username": "jdoe",
        "comment": "Jon Doe",
        "uid": "1006",
        "groups": "ops",
        "sudo": "True",
        "ssh_public_key": "ssh-rsa AAAAA6CbcDd0a2EABBBPAAACCA..."
    }

And then simply call `fileglob_to_dict` from a task.

File `roles/users/tasks/main.yml`:

    - name: create ops users
      user:
        name="{{item.username}}"
        comment="{{item.comment}}"
        uid="{{ item.uid }}"
        groups="{{ item.groups }}"
        generate_ssh_key=no
        shell="{{item.shell|default("/bin/bash")}}"
        state=present
      fileglob_to_dict:
        - "{{ 'ops/*' }}"
       