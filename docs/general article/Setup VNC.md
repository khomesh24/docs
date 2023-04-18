# VNC setup

## Disabling Wayland Display Manager and Enabling X.org

In __/etc/gdm/custom.conf__ uncomment **WaylandEnable=false** 

```yaml
# GDM configuration storage

[daemon]
# Uncoment the line below to force the login screen to use Xorg
WaylandEnable=false

[security]

[xdmcp]

[chooser]

[debug]
# Uncomment the line below to turn on debugging
#Enable=true
```

## Install VNC Server in RHEL 8

Install tigervnc packages

```bash
dnf install tigervnc-server tigervnc-server-module
```

set vncpasswd with root user

```bash
vncpasswd
```

## Configure VNC service 

Create systemd file

```bash
vi /etc/systemd/system/vncserver@.service

[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=forking
WorkingDirectory=/root
User=root
Group=root

PIDFile=/root/.vnc/%H%i.pid

ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver -autokill %i
ExecStop=/usr/bin/vncserver -kill %i

[Install]
WantedBy=multi-user.target
```

Disable selinux

```bash
setenforce 0
sed -i 's/enforcing/disabled/g' /etc/selinux/config
```

Start vnc service

```bash
systemctl daemon-reload
systemctl start vncserver@:1
systemctl status vncserver@:1
systemctl enable vncserver@:1
```

Open firewall for vnc

Identify the vnc port and add port in firewall

```bash
netstat -tlnp 

firewall-cmd --permanent --add-port=<port>/tcp
firewall-cmd --reload
```
