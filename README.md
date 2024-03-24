# test-pipeline

---
# tasks file for tomcat_role
- name: Update apt package cache
  become: yes
  apt:
    update_cache: yes

- name: Install default JDK
  become: yes
  apt:
    name: default-jdk
    state: present

- name: Create tomcat group
  become: yes
  group:
    name: tomcat

- name: Create tomcat user
  become: yes
  user:
    name: tomcat
    shell: /bin/false
    group: tomcat
    home: /opt/tomcat

- name: Create tomcat directory
  become: yes
  file:
    path: "/opt/tomcat"
    state: directory

- name: Download Apache Tomcat
  become: yes
  get_url:
    url: "http://dlcdn.apache.org/tomcat/tomcat-11/v11.0.0-M17/bin/apache-tomcat-11.0.0-M17.tar.gz"
    dest: "/opt/tomcat/apache-tomcat-11.0.0-M17.tar.gz

- name: Extract Apache Tomcat
  become: yes
  unarchive:
    src: "/opt/tomcat/apache-tomcat-11.0.0-M17.tar.gz"
    dest: "/opt/tomcat"
    remote_src: yes
    creates: "/opt/tomcat/apache-tomcat-11.0.0-M17.tar.gz"

- name: Change ownership of tomcat directory
  become: yes
  file:
    path: "/opt/tomcat"
    owner: tomcat
    group: tomcat
    recurse: yes

- name: Add tomcat.service file
  become: yes
  template:
    src: "tomcat.service.j2"
    dest: "/etc/systemd/system/tomcat.service"
    mode: 0644

- name: Reload systemd daemon
  become: yes
  systemd:
    daemon_reload: yes

- name: Start Tomcat service
  become: yes
  systemd:
    name: tomcat
    state: started


---
---
# tasks file for maven_role
- name: Install python-software-properties
  become: yes
  apt:
    name: software-properties-common
    state: present

- name: Add Java repository
  become: yes
  apt_repository:
    repo: "ppa:linuxuprising/java"

- name: Update package lists
  become: yes
  apt:
    update_cache: yes

- name: Install default JDK
  become: yes
  apt:
    name: default-jdk
    state: present

- name: Download Apache Maven
  become: yes
  get_url:
    url: "https://dlcdn.apache.org/maven/maven-3/3.9.6/binaries/apache-maven-3.9.6-bin.tar.gz"
    dest: "/home/edureka/Downloads/apache-maven-3.9.6-bin.tar.gz"

- name: Extract Apache Maven
  become: yes
  unarchive:
    src: "/home/edureka/Downloads/apache-maven-3.9.6-bin.tar.gz"
    dest: "/opt/"
    remote_src: yes

- name: Set up alternatives for Maven
  become: yes
  command: "update-alternatives --install /usr/bin/mvn maven /opt/apache-maven-3.3.6/bin/mvn 1001"
