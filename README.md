# ansible-miarec-monitoring
Ansible playbooks install monitoring clients on miarec servers

The following services will be installed on each service
- `prometheus` in agent mode, will scrape locally install services `node_exporter` and `process_exporter` and remote write metrics to a remote server
- `promtail` will tail specified logs and write to remote loki server
- `process_exporter` will scrape local metrics about specified processes
- `node_exporter` will scrape local metrics about system performance (storage, CPU, memory, etc)

## Variables
 - `prometheus_write_target` defines remote prometheus server, example "https://prometheus.example.com" or "http://1.1.1.1:9090"
 - `promtail_loki_url` defined remote loki server, example "https://loki.example.com" or "http://1.1.1.1:3100"

 - `prometheus_external_labels` hash of labels that will be applied to all metrics sent to remote prometheus
 - `promtail_external_labels` hash of labels that will be applied to all logs sent to remote loki
 - `common_process_exporter_names` list of processes `process_exporter` should track

    example:
    ```yaml
    common_process_exporter_names:
      - promtail
      - node_exporter
      - process_exporter
    ```
 - `common_promtail_scrape_jobs` list of log files `node_exporter` should tail

     example:
    ```yaml
    common_promtail_scrape_jobs: [
      {'job_name':'example1', 'path':'/path/to/file.log'},
      {'job_name':'example2', 'path':'/path/to/file.log'}
    ]
    ```

## Group Variables
In some cases you may wnat to apply a variable to multiple hosts in an ansible group, for instance you only need to follow process `miarec` on any instance in group `recorder`

These are defined under `vars/{group.yml}`
```yaml
recorder_process_exporter_names:
  - miarec
```

## Install Ansible locally (instructions for Ubuntu)

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

For other OS see official Ansible documentation: [Installing Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)



## Install the required Ansible Galaxy roles

```
ansible-galaxy install -r requirements-galaxy.yml
```

## Check the Ansible inventory
Terraform project `monitoring-stack` should generate an inventory file in this directory


## Run the playbook

```
ansible-playbook -i inventory setup-monitoring-clients.yml
```
