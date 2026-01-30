run /util bash -c "tail /var/log/audit"
logger -p local0.info "F5-AUDIT-TEST-TO-SPLUNK-9515"


tmsh modify sys syslog {
    include "
    filter f_remote_loghost {
        level(debug..emerg) and
        not (facility(local6) and level(info) and filter(f_httpd_ssl_acc)) and
        not (facility(local6) and level(info) and filter(f_httpd_ssl_req));
    };
    destination d_remote_loghost {
        udp(\"10.90.255.13\" port(514));
    };
    destination d_remote_splunk {
        tcp(\"10.90.255.16\" port(9515));
    };
    log {
        source(s_syslog_pipe);
        destination(d_remote_loghost);
    };
    log {
        source(s_syslog_pipe);
        filter(f_remote_loghost);
        destination(d_remote_splunk);
    };
    log {
        source(s_syslog_pipe);
        filter(f_audit);
        destination(d_remote_splunk);
    };
    "
    remote-servers replace-all-with {
        remotesyslog1 {
            host 10.90.255.16
            remote-port 9514
        }
    }
}
