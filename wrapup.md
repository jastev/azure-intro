Wrap Up
=======

While SSH is allowed between subnets in the vnet, our jumpbox will need
our private key in order to establish a connection to the VMs serving HTTP.

```bash
$ scp ~/.ssh/id_rsa intro@13.91.100.127:.ssh/id_rsa
```

Now, ssh to the jumpbox, and then to the first HTTP server:

```bash
$ ssh intro@13.91.100.127
The authenticity of host '13.91.100.127 (13.91.100.127)' can't be established.
ECDSA key fingerprint is SHA256:MTE9DjK2YvxCvwFURsqjcjHQv29/YRaAV9NoNGpJOjs.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '13.91.100.127' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Dec  1 22:07:25 UTC 2016

  System load: 0.3               Memory usage: 2%   Processes:       80
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.
```

```bash
$ ssh intro@10.1.0.4
Enter passphrase for key '/home/intro/.ssh/id_rsa': 
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-65-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Thu Dec  1 22:20:50 UTC 2016

  System load: 0.23              Memory usage: 2%   Processes:       80
  Usage of /:  39.6% of 1.94GB   Swap usage:   0%   Users logged in: 0

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.
```

For each of the two servers (at 10.1.0.4 and 10.1.0.5) use apt-get to bring
the VM up to date, and install the Apache httpd server.

```bash
$ sudo apt-get update && sudo apt-get upgrade
```

```bash
$ sudo apt-get install apache2 apache2-doc apache2-utils
```

You should now be able to point a web browser from anywhere at the PIP
of the load balancer (104.45.233.180 in our example) and see the standard
Apache splash page.
