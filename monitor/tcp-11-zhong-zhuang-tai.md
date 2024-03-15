# TCP 11种状态

### TCP的11种状态

* ESTABLISHED
* SYN\_SENT
* SYN\_RECV
* FIN\_WAIT1
* FIN\_WAIT2
* TIME\_WAIT
* CLOSE
* CLOSE\_WAIT
* LAST\_ACK
* LISTEN
* CLOSING



### 状态说明

#### The state of the socket.&#x20;

```
ESTABLISHED
       The socket has an established connection.

SYN_SENT
       The socket is actively attempting to establish a connection.

SYN_RECV
       A connection request has been received from the network.

FIN_WAIT1
       The socket is closed, and the connection is shutting down.

FIN_WAIT2
       Connection is closed, and the socket is waiting for a shutdown from the remote end.

TIME_WAIT
       The socket is waiting after close to handle packets still in the network.

CLOSE  The socket is not being used.

CLOSE_WAIT
       The remote end has shut down, waiting for the socket to close.

LAST_ACK
       The remote end has shut down, and the socket is closed. Waiting for acknowledgement.

LISTEN The  socket is listening for incoming connections.  Such sockets are not included in the output unless you specify the --lis‐
       tening (-l) or --all (-a) option.

CLOSING
       Both sockets are shut down but we still don't have all our data sent.

UNKNOWN
       The state of the socket is unknown.
```



### 配置网络状态监控项

```bash
cat > /etc/zabbix/zabbix_agentd.d/userparameter_netstat.conf << EOF
UserParameter=ESTABLISHED,netstat -ant | grep -c ESTABLISHED
UserParameter=SYN_SENT,netstat -ant | grep -c SYN_SENT
UserParameter=SYN_RECV,netstat -ant | grep -c SYN_RECV
UserParameter=FIN_WAIT1,netstat -ant | grep -c FIN_WAIT1
UserParameter=FIN_WAIT2,netstat -ant | grep -c FIN_WAIT2
UserParameter=TIME_WAIT,netstat -ant | grep -c TIME_WAIT
UserParameter=CLOSE,netstat -ant | grep -c CLOSE
UserParameter=CLOSE_WAIT,netstat -ant | grep -c CLOSE_WAIT
UserParameter=LAST_ACK,netstat -ant | grep -c LAST_ACK
UserParameter=LISTEN,netstat -ant | grep -c LISTEN
UserParameter=CLOSING,netstat -ant | grep -c CLOSING
EOF
```

