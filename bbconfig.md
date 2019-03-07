【配置supervisor】
apt-get install supervisor

cat /etc/supervisor/supervisord.conf

【配置supervisor】
; supervisor config file

[unix_http_server]
file=/var/run/supervisor.sock   ; (the path to the socket file)
chmod=0700                       ; sockef file mode (default 0700)

[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
pidfile=/var/run/supervisord.pid ; (supervisord pidfile;default supervisord.pid)
childlogdir=/var/log/supervisor            ; ('AUTO' child log dir, default $TEMP)

; the below section must remain in the config file for RPC
; (supervisorctl/web interface) to work, additional interfaces may be
; added by defining them in separate rpcinterface: sections
[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket

; The [include] section can just contain the "files" setting.  This
; setting can list multiple files (separated by whitespace or
; newlines).  It can also contain wildcards.  The filenames are
; interpreted as relative to this file.  Included files *cannot*
; include files themselves.

[include]
files = /etc/supervisor/conf.d/*.conf

【启动supervisor】
supervisord -c supervisor.conf

【自启动supervisor】
vim /lib/systemd/system/supervisor.service

[Unit]
Description=Supervisor process control system for UNIX
Documentation=http://supervisord.org
After=network.target

[Service]
ExecStart=/usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf
ExecStop=/usr/bin/supervisorctl $OPTIONS shutdown
ExecReload=/usr/bin/supervisorctl -c /etc/supervisor/supervisord.conf $OPTIONS reload
KillMode=process
Restart=on-failure
RestartSec=50s

[Install]
WantedBy=multi-user.target




【supervisor 配置 ssh】
vim /etc/supervisor/conf.d/connect.conf

[program:connect]
command = /home/frp/frpc -c /home/frp/frpc.ini
directory = /
autostart = true
autorestart = true
startretries = 30
stopwaitsecs = 10
stdout_logfile_maxbytes = 1MB
stdout_logfile = /home/frp/autossh.log


【配置客户端FRP】

vim /home/frp//frpc.ini

[common]
server_addr = 47.100.14.63
server_port = 7000
user = bb109
token = lBKYhaxpxZ1men5m
[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 7101
