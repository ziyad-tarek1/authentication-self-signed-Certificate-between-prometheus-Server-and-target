### ****The Node Exporter****
### Encryption
- Create a directory node_exporter

`mkdir /etc/node_exporter`

- Make the current directory node_exporter

`cd /etc/node_exporter `

 - Using open SSL to generate certificates
   
`sudo openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout node_exporter.key -out node_exporter.crt -subj "/C=US/ST=California/L=Oakland/O=MyOrg/CN=localhost" -addext "subjectAltName = DNS:localhost"
`
- Create a config.yaml file which contains:

`tls_server_config:
  cert_file: node_exporter.crt
  key_file: node_exporter.key`
  
- Update the service of node_exporter with TLS config 

`[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target
[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter --web.config="/etc/node_exporter/config.yaml"
[Install]
WantedBy=multi-user.target`

- Reload the daemon and start the node_exporter

`sudo systemctl daemon-reload
sudo systemctl restart node_exporter`

### Authentication

- Run this command to create hash password
  
`htpasswd -nBC 12 "" | tr -d ':\n'`

- update the config.yaml file to be :

  `tls_server_config:
  cert_file: node_exporter.crt
  key_file: node_exporter.key
basic_auth_users:
  prometheus: $2y$12$EQmGdhTLBVUJOXOB.2ucS.Rw49JljvaeXiBHnvFlzeARzbdcUtVL2
`
- username is prometheus , hashed password is  $2y$12$EQmGdhTLBVUJOXOB.2ucS.Rw49JljvaeXiBHnvFlzeARzbdcUtVL2

 - Restart the node_exporter

    `sudo systemctl restart node_exporter
`

### ****Prometheus Server****

- Copy the node_exporter.crt file from the node exporter to the Prometheus server at /etc/prometheus

- Update Prometheus configuration file ,add user name and password in plain text as below:
  
![image](https://github.com/user-attachments/assets/03554412-b817-4479-a782-5e5dede5195e)


- Now you can restart the Prometheus 

- we see that the Node_Exporter is UP 

![image](https://github.com/user-attachments/assets/7a3c3cc6-fbb9-4ed6-9fba-a370ad035232)
