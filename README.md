
Key notes to self:
Watch the space parsing for route additions - 
Must have the upstream router (first hop) for bidirectional traffic
Static routes on second or third hop router to enable remaining connectivity

## Source-policy routing for site gateways

Some Trixie/Raspberry Pi gateway builds need return traffic from remote LANs to
go back to the local LAN router instead of being emitted directly to the local
client MAC. Enable the source-route helper only on WireGuard site gateways, not
ordinary WireGuard clients.

Example host vars:

```yaml
wireguard_source_routing_enabled: true
wireguard_source_routing_gateway: 192.168.3.1
wireguard_source_routing_remote_subnets:
  - 192.168.100.0/24
```

When enabled, the role installs `/usr/local/sbin/wireguard-source-routes` and
adds `wg-quick` hooks:

```ini
PostUp = /usr/local/sbin/wireguard-source-routes up
PostDown = /usr/local/sbin/wireguard-source-routes down
```

The helper writes only to the configured policy table, defaulting to table
`100`, and adds one source rule for each remote routed LAN prefix.
