# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/xenial64"

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "playbook.yml"
  end

  config.vm.define "web" do |web|
    web.vm.network "private_network", ip: "192.168.33.10"
  end

end

# playbook.yml

---
- hosts: all
  become: true
  vars:
    nginx_listen_port: "8080"
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
      notify: Start Nginx

    - name: Copy Nginx Config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Reload Nginx

  handlers:
    - name: Start Nginx
      systemd:
        name: nginx
        enabled: yes
        state: started

    - name: Reload Nginx
      systemd:
        name: nginx
        state: reloaded

# nginx.conf.j2

user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    server {
        listen {{ nginx_listen_port }};
        server_name localhost;

        location / {
            root /var/www/html;
            index index.html;
        }
    }
}
