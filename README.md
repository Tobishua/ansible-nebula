# Ansible role for Nebula

[Nebula](https://github.com/slackhq/nebula/) is a scalable overlay networking tool with a focus on performance, simplicity and security.

And this ansible role for deploy nebula regular node and lighthouse node.

For this role you should have preconfigured host with nebula-cert which available via ansible.

## Configure & Install

Place role directory in your Ansible installation, then create playbook and change host_vars for each hosts

There is playbook file for nebula role:
```
- name: Deploy Nebula
  hosts: "*"
  vars:
    # Ansible group with one or few lighthouse (hosts with routable IPs)
    # (make sure you set "nebula_port" and "external_addr" hostvars for that hosts). In this example we use ansible group named "nebula_lighthouses"
    lighthouses: "{{ groups['nebula_lighthouses'] }}"
    # Local range is used to define a hint about the local network range, which speeds up discovering the fastest
    # path to a network adjacent nebula node.
    local_range: "10.100.100.0/24"

	# Preconfigured nebula node with nebula-cert bin
    cert_authority: "lighthouse.example.com"

	# Path with nebula-bin file
    nebula_cert_path: "/var/lib/nebula/"
  roles:
    - role: nebula
  become: true
```

### host_vars variables
`nebula_ip` - Internal IP (inside nebula network) for this host

`nebula_host` - IP address to bind nebula to (default: 0.0.0.0)

`nebula_port` - Nebula port (default: 4242)

`nebula_subnet` - Subnet size for sign in certificate (default: 8)

`nebula_groups` - Groups for host (default: nebula,node)

`nebula_log_level` - Log level (panic, fatal, error, warning, info, or debug) (default: info)

`nebula_log_format` - Log format, text or json (default: text)

`nebula_dev_tun` - Name of the tun device (default: nebula 1)

`nebula_mtu` - MTU for nebula interface (default: 1300)

`nebula_tx_queue` - Transmit queue length

`nebula_lighthouse` - Used to enable lighthouse functionality for a node (default: false)

`nebula_dns` - DNS listener (default: false)

`nebula_dns_port` - Port for DNS listener (default: 53)

`nebula_dns_host` - The DNS host defines the IP to bind the dns listener to (default: 0.0.0.0)

`nebula_update_interval` - Number of seconds between updates from this node to a lighthouse (default: 60)

`nebula_prometheus_host` - IP to bind prometheus to (default: 127.0.0.1)

`nebula_prometheus_port` - Port for prometheus (default: 9091)

`nebula_prometheus_path` - Metrics path (default: metrics)

`nebula_drop_multicast` - Toggles forwarding of multicast packets (default: false)

`nebula_drop_local_broadcast` - Toggles forwarding of local broadcast packets the address of which depends on the ip/mask encoded in pki.cert (default: false)

`nebula_inbound_rules` - Rules for inbound traffic

`nebula_outbound_rules` - Rules for outbound traffic

### Example host_vars
```
nebula_ip: "10.100.100.20"

nebula_prometheus_port: 9991

# Unsafe routes allows you to route traffic over nebula to non-nebula nodes
# Unsafe routes should be avoided unless you have hosts/services that cannot run nebula
# NOTE: The nebula certificate of the "via" node *MUST* have the "route" defined as a subnet in its certificate
nebula_unsafe_routes:
  - network: "10.0.0.0/24"
    gateway: "10.100.100.1"
  - network: "10.10.0.0/24"
    gateway: "10.100.100.1"

nebula_inbound_rules: |
  - port: 443
    proto: tcp
    host: any

  - port: 80
    proto: tcp
    host: any

  - port: any
    proto: icmp
    host: any

nebula_outbound_rules: |
  - port: any
    proto: any
    host: any
```

[MIT License](https://github.com/tobishua/ansible-nebula/blob/master/LICENCE).
