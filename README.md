# [grafana](#grafana)

Grafana - platform for analytics and monitoring

|GitHub|GitLab|Quality|Downloads|Version|Issues|Pull Requests|
|------|------|-------|---------|-------|------|-------------|
|[![github](https://github.com/buluma/ansible-role-grafana/workflows/Ansible%20Molecule/badge.svg)](https://github.com/buluma/ansible-role-grafana/actions)|[![gitlab](https://gitlab.com/buluma/ansible-role-grafana/badges/master/pipeline.svg)](https://gitlab.com/buluma/ansible-role-grafana)|[![quality](https://img.shields.io/ansible/quality/58593)](https://galaxy.ansible.com/buluma/grafana)|[![downloads](https://img.shields.io/ansible/role/d/58593)](https://galaxy.ansible.com/buluma/grafana)|[![Version](https://img.shields.io/github/release/buluma/ansible-role-grafana.svg)](https://github.com/buluma/ansible-role-grafana/releases/)|[![Issues](https://img.shields.io/github/issues/buluma/ansible-role-grafana.svg)](https://github.com/buluma/ansible-role-grafana/issues/)|[![PullRequests](https://img.shields.io/github/issues-pr-closed-raw/buluma/ansible-role-grafana.svg)](https://github.com/buluma/ansible-role-grafana/pulls/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/default/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: buluma.grafana
      vars:
        grafana_security:
          admin_user: admin
          admin_password: password
```

The machine needs to be prepared. In CI this is done using `molecule/default/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  gather_facts: false
  tasks: []
```


## [Role Variables](#role-variables)

The default values for the variables are set in `defaults/main.yml`:
```yaml
---
grafana_version: latest
grafana_yum_repo_template: etc/yum.repos.d/grafana.repo.j2
grafana_manage_repo: true

# Should we use the provisioning capability when possible (provisioning require grafana >= 5.0)
grafana_use_provisioning: true

# Should the provisioning be kept synced. If true, previous provisioned objects will be removed if not referenced anymore.
grafana_provisioning_synced: false

grafana_instance: "{{ ansible_fqdn | default(ansible_host) | default(inventory_hostname) }}"

grafana_logs_dir: "/var/log/grafana"
grafana_data_dir: "/var/lib/grafana"

grafana_address: "0.0.0.0"
grafana_port: 3000
# To enable the use of ports below 1024 for unprivileged processes linux needs to set CAP_NET_BIND_SERVICE.
# This has some security implications, and should be a conscious choice.
# Get informed by reading: http://man7.org/linux/man-pages/man7/capabilities.7.html
grafana_cap_net_bind_service: false

# External Grafana address. Variable maps to "root_url" in grafana server section
grafana_url: "http://{{ grafana_address }}:{{ grafana_port }}"
grafana_api_url: "{{ grafana_url }}"
grafana_domain: "{{ ansible_fqdn | default(ansible_host) | default('localhost') }}"

# Additional options for grafana "server" section
# This section WILL omit options for: http_addr, http_port, domain, and root_url, as those settings are set by variables listed before
grafana_server:
  protocol: http
  enforce_domain: false
  socket: ""
  cert_key: ""
  cert_file: ""
  enable_gzip: false
  static_root_path: public
  router_logging: false
  serve_from_sub_path: false

# Variables correspond to ones in grafana.ini configuration file
# Security
grafana_security:
  admin_user: admin
  admin_password: "password"
#  secret_key: ""
#  login_remember_days: 7
#  cookie_username: grafana_user
#  cookie_remember_name: grafana_remember
#  disable_gravatar: true
#  data_source_proxy_whitelist:

# Database setup
grafana_database:
  type: sqlite3
#  host: 127.0.0.1:3306
#  name: grafana
#  user: root
#  password: ""
#  url: ""
#  ssl_mode: disable
#  path: grafana.db
#  max_idle_conn: 2
#  max_open_conn: ""
#  log_queries: ""

# Remote cache
grafana_remote_cache: {}

# User management and registration
grafana_welcome_email_on_sign_up: false
grafana_users:
  allow_sign_up: false
  # allow_org_create: true
  # auto_assign_org: true
  auto_assign_org_role: Viewer
  # login_hint: "email or username"
  default_theme: dark
  # external_manage_link_url: ""
  # external_manage_link_name: ""
  # external_manage_info: ""

# grafana authentication mechanisms
grafana_auth: {}
#  disable_login_form: false
#  oauth_auto_login: false
#  disable_signout_menu: false
#  signout_redirect_url: ""
#  anonymous:
#    org_name: "Main Organization"
#    org_role: Viewer
#  ldap:
#    config_file: "/etc/grafana/ldap.toml"
#    allow_sign_up: false
#  basic:
#    enabled: true

grafana_ldap: {}
#  verbose_logging: false
#  servers:
#    host: 127.0.0.1
#    port: 389 # 636 for SSL
#    use_ssl: false
#    start_tls: false
#    ssl_skip_verify: false
#    root_ca_cert: /path/to/certificate.crt
#    bind_dn: "cn=admin,dc=grafana,dc=org"
#    bind_password: grafana
#    search_filter: "(cn=%s)" # "(sAMAccountName=%s)" on AD
#    search_base_dns:
#      - "dc=grafana,dc=org"
#    group_search_filter: "(&(objectClass=posixGroup)(memberUid=%s))"
#    group_search_base_dns:
#      - "ou=groups,dc=grafana,dc=org"
#    attributes:
#      name: givenName
#      surname: sn
#      username: sAMAccountName
#      member_of: memberOf
#      email: mail
#  group_mappings:
#    - name: Main Org.
#      id: 1
#      groups:
#        - group_dn: "cn=admins,ou=groups,dc=grafana,dc=org"
#          org_role: Admin
#        - group_dn: "cn=editors,ou=groups,dc=grafana,dc=org"
#          org_role: Editor
#        - group_dn: "*"
#          org_role: Viewer
#    - name: Alternative Org
#      id: 2
#      groups:
#        - group_dn: "cn=alternative_admins,ou=groups,dc=grafana,dc=org"
#          org_role: Admin

grafana_session: {}
#  provider: file
#  provider_config: "sessions"

grafana_analytics: {}
#  reporting_enabled: true
#  google_analytics_ua_id: ""

# Set this for mail notifications
grafana_smtp: {}
#  host:
#  user:
#  password:
#  from_address:

# Enable grafana alerting mechanism
grafana_alerting:
  execute_alerts: true
#  error_or_timeout: 'alerting'
#  nodata_or_nullvalues: 'no_data'
#  concurrent_render_limit: 5

# Grafana logging configuration
grafana_log:
# mode: 'console file'
# level: info

# Internal grafana metrics system
grafana_metrics: {}
#  interval_seconds: 10
#  graphite:
#    address: "localhost:2003"
#    prefix: "prod.grafana.%(instance_name)s"

# Distributed tracing options
grafana_tracing: {}
#  address: "localhost:6831"
#  always_included_tag: "tag1:value1,tag2:value2"
#  sampler_type: const
#  sampler_param: 1

grafana_snapshots: {}
#  external_enabled: true
#  external_snapshot_url: "https://snapshots-origin.raintank.io"
#  external_snapshot_name: "Publish to snapshot.raintank.io"
#  snapshot_remove_expired: true
#  snapshot_TTL_days: 90

# External image store
grafana_image_storage: {}
#  provider: gcs
#  key_file:
#  bucket:
#  path:


#######
# Plugins from https://grafana.com/plugins
grafana_plugins: []
#  - raintank-worldping-app

# Dashboards from https://grafana.com/dashboards
grafana_dashboards: []
#  - dashboard_id: '4271'
#    revision_id: '3'
#    datasource: 'Prometheus'
#  - dashboard_id: '1860'
#    revision_id: '4'
#    datasource: 'Prometheus'
#  - dashboard_id: '358'
#    revision_id: '1'
#    datasource: 'Prometheus'

grafana_dashboards_dir: "dashboards"

# Alert notification channels to configure
grafana_alert_notifications: []
#  - name: "Email Alert"
#    type: "email"
#    uid: channel1
#    is_default: true
#    settings:
#      addresses: "example@example.com"

# Datasources to configure
grafana_datasources: []
#  - name: "Prometheus"
#    type: "prometheus"
#    access: "proxy"
#    url: "http://prometheus.mydomain"
#    basicAuth: true
#    basicAuthUser: "admin"
#    basicAuthPassword: "password"
#    isDefault: true
#    jsonData:
#      tlsAuth: false
#      tlsAuthWithCACert: false
#      tlsSkipVerify: true

# API keys to configure
grafana_api_keys: []
#  - name: "admin"
#    role: "Admin"
#  - name: "viewer"
#    role: "Viewer"
#  - name: "editor"
#    role: "Editor"

# The location where the keys should be stored.
grafana_api_keys_dir: "{{ lookup('env', 'HOME') }}/grafana/keys"

grafana_environment: {}

# Panels configurations
grafana_panels: {}
#  disable_sanitize_html: false
#  enable_alpha: false
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/buluma/ansible-role-grafana/blob/main/requirements.txt).


## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://buluma.co.ke/) for further information.

Here is an overview of related roles:

![dependencies](https://raw.githubusercontent.com/buluma/ansible-role-grafana/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/buluma):

|container|tags|
|---------|----|
|ubuntu|all|
|debian|all|
|el|all|
|fedora|all|

The minimum version of Ansible required is 2.9, tests have been done to:

- The previous version.
- The current version.
- The development version.



If you find issues, please register them in [GitHub](https://github.com/buluma/ansible-role-grafana/issues)

## [License](#license)

Apache-2.0

## [Author Information](#author-information)

[Michael Buluma](https://buluma.github.io/)
