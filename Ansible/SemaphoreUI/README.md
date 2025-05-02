#Semaphore Installation Playbook

This Ansible playbook will install and configure Semaphore, the modern UI for Ansible, on a Ubuntu 22.04 server.

Usage Instructions

- Save the playbook as install_semaphore.yml

- Create a templates directory in the same folder and save the two template files there

- Run the playbook with:
    
      ansible-playbook -i your_inventory_file install_semaphore.yml

Post-Installation Steps

- Access Semaphore at http://your_server_ip:3000

- The default username is admin

- To get the initial password, check the logs:
    
      journalctl -u semaphore -n 50

Notes
    This playbook installs Semaphore with SQLite backend by default
    For production use, consider using MySQL or PostgreSQL by modifying the configuration
    The playbook generates secure random values for encryption keys
    Semaphore will run on port 3000 by default (adjust the semaphore_port variable if needed)
    Make sure your firewall allows traffic to the Semaphore port
