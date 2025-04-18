---
layout: default
title: Cheatsheet
parent: Proxmox
nav_order: 1
---

# Cheatsheet

___

## disable subscription popup (very annoying)
```bash
sed -i.bak 's/notfound/active/g' /usr/share/perl5/PVE/API2/Subscription.pm && systemctl restart pveproxy.service
```
OR
```bash
sed -Ezi.bak "s/(Ext.Msg.show\(\{\s+title: gettext\('No valid sub)/void\(\{ \/\/\1/g" /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js && systemctl restart pveproxy.service
```

___

## patch cloud init type nocloud2 <span class="fs-1">[source](https://forum.proxmox.com/threads/fedora-35-36-37-cloud-init-does-ignore-network-config-settings-for-nameservers-patch-for-v2.120923/#post-613111){: .btn .btn-purple }</span>

```patch
--- a/usr/share/perl5/PVE/QemuServer.pm	2023-01-11 15:54:21.309856406 +0100
+++ b/usr/share/perl5/PVE/QemuServer.pm	2023-01-11 15:54:38.113952493 +0100
@@ -761,7 +761,7 @@
    description => 'Specifies the cloud-init configuration format. The default depends on the'
        .' configured operating system type (`ostype`. We use the `nocloud` format for Linux,'
        .' and `configdrive2` for windows.',
-	enum => ['configdrive2', 'nocloud', 'opennebula'],
+	enum => ['configdrive2', 'nocloud', 'opennebula', 'nocloud2'],
    },
    ciuser => {
    optional => 1,
--- a/usr/share/perl5/PVE/QemuServer/Cloudinit.pm	2023-01-11 15:37:24.388358477 +0100
+++ b/usr/share/perl5/PVE/QemuServer/Cloudinit.pm	2023-01-11 15:44:07.950432111 +0100
@@ -346,7 +346,7 @@
    my $net = PVE::QemuServer::parse_net($conf->{$iface});
    my $ipconfig = PVE::QemuServer::parse_ipconfig($conf->{"ipconfig$id"});

-	my $mac = $net->{macaddr}
+	my $mac = lc($net->{macaddr})
        or die "network interface '$iface' has no mac address\n";

    $content .= "${i}$iface:\n";
@@ -509,6 +509,32 @@
    commit_cloudinit_disk($conf, $vmid, $drive, $volname, $storeid, $files, 'cidata');
}

+sub generate_nocloud2 {
+    my ($conf, $vmid, $drive, $volname, $storeid) = @_;
+
+    my ($user_data, $network_data, $meta_data, $vendor_data) = get_custom_cloudinit_files($conf);
+    $user_data = cloudinit_userdata($conf, $vmid) if !defined($user_data);
+    $network_data = nocloud_network_v2($conf) if !defined($network_data);
+    $vendor_data = '' if !defined($vendor_data);
+
+    if (!defined($meta_data)) {
+	$meta_data = nocloud_gen_metadata($user_data, $network_data);
+    }
+
+    # we always allocate a 4MiB disk for cloudinit and with the overhead of the ISO
+    # make sure we always stay below it by keeping the sum of all files below 3 MiB
+    my $sum = length($user_data) + length($network_data) + length($meta_data) + length($vendor_data);
+    die "Cloud-Init sum of snippets too big (> 3 MiB)\n" if $sum > (3 * 1024 * 1024);
+
+    my $files = {
+	'/user-data' => $user_data,
+	'/network-config' => $network_data,
+	'/meta-data' => $meta_data,
+	'/vendor-data' => $vendor_data
+    };
+    commit_cloudinit_disk($conf, $vmid, $drive, $volname, $storeid, $files, 'cidata');
+}
+
sub get_custom_cloudinit_files {
    my ($conf) = @_;

@@ -557,6 +583,7 @@
    configdrive2 => \&generate_configdrive2,
    nocloud => \&generate_nocloud,
    opennebula => \&generate_opennebula,
+    nocloud2 => \&generate_nocloud2
};

sub has_changes {

}
```

{: .note}
use citype: nocloud2 in qemu conf
