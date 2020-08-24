---
author: Grizwold
title: SSH, port forwarding and when to use it?
published: true
comments: true
tags: english linux everyday-job
---

So you've just finished working on your little shiny app which will be a part of huge distributed system. 
Today is the day! It goes onto production! But hey! You can't connect to database. It's behind thick firewall.
But no worries, you have access to prod bastion server. Simple ssh'ing into it should do the work. Nope!
It's still inaccessible! Turns out you have to jump onto second bastion from the first, but... The second bastion does 
not have DB client installed, and you have only one SSH configuration dialog in your `pgAdmin`. Where to put the second ssh config?!

![](assets/2020-08-23-ssh-and-port-forwarding/bastions.png)

## Local port forwarding to the rescue!

Local port forwarding is a neat tool created just for such cases. It will forward port from remote service `App` through
a second host `B2` onto first host `B1`. Then the services port will act like port local to first host. Thus you will be 
able to ssh into your postgres from your local network into first host!

Ok, theory aside and let's practice a lil'bit.

Lets say that our hosts are under following IPs/domains:

```
B1 - 192.0.0.1
B2 - 172.0.0.1
App - rds.xyz.amazon.com:5432
```

then to access the `App` (if you had a DB client on bastions) you could simple ssh to the `B1` and hop onto `B2` and voila!

```
grizwold@home:~$ ssh username@192.0.0.1
username@192.0.0.1's password:
Last login: Fri Aug 21 14:34:09 2020 from 127.0.0.1

[syscnt5u@ip-192-0-0-1 ~]$ ssh 172.0.0.1
username@172.0.0.1's password:
Last login: Fri Aug 21 14:34:34 2020 from 192.0.0.1

[syscnt5u@ip-172-0-0-1 ~]$ psql -h localhost -U my-apps-db-user -d my-apps-db
Password for user my-apps-db-user:
psql (9.6.6, server 10.7)

my-apps-db=> SELECT * FROM user
my-apps-db-> ;
    name   | role  | pass
-----------+-------+------
 griwzwold | ADMIN | dupa 
```

but! You don't have `psql` on `B2` bastion! So let's use local port forwarding, shall we?

First you need to open a connection to `B2` which will forward the port from our `App`. To do this login to `B1` and forward a port
by utilizing this command: `ssh -L 5432:rds.xyz.amazon.com:5432 172.0.0.1`

```
grizwold@home:~$ ssh username@192.0.0.1
username@192.0.0.1's password:
Last login: Fri Aug 21 14:34:09 2020 from 127.0.0.1

[syscnt5u@ip-192-0-0-1 ~]$ ssh -L 5432:rds.xyz.amazon.com:5432 172.0.0.1
username@172.0.0.1's password:
Last login: Fri Aug 21 14:34:34 2020 from 192.0.0.1

[syscnt5u@ip-172-0-0-1 ~]$ 
```

and leave it as is. Then open second terminal and simply connect to the `App` on `B1` just like it were running there on localhost

```
grizwold@home:~$ ssh username@192.0.0.1
username@192.0.0.1's password:
Last login: Fri Aug 21 14:34:09 2020 from 127.0.0.1

[syscnt5u@ip-192-0-0-1 ~]$ psql -h localhost -U my-apps-db-user -d my-apps-db
Password for user my-apps-db-user:
psql (9.6.6, server 10.7)

my-apps-db=> SELECT * FROM user
my-apps-db-> ;
    name   | role  | pass
-----------+-------+------
 griwzwold | ADMIN | dupa 
```

What's more! You can even forward the local forwarded port further onto you local machine and use the `App` on your local machine.
Or just simply configure your local `pgAdmin` to ssh into `B1` and use it like the `App` were there and not behind `B2`.

Yet more details on the command used? Let's slice it:

![](assets/2020-08-23-ssh-and-port-forwarding/bastions-forwarded.png)

#### `ssh -L 5432:rds.xyz.amazon.com:5432 172.0.0.1`

- `-L` means you want to open local port on which traffic will be directed to specified host behind next bastion.

- first `5432` means that this local port will be open and directed to specified external port. That is if I had this local 
port already open I can specify any other port to be used as my `App` entry point, so you are not forced to use same port 
as external service.

- `rds.xyz.amazon.com:5432` is the host behind `B2` which will be attached to our newly open local port. It can be `localhost`
if you wanted to attach something which is local to `B2`.

- Finally, last but not least: `172.0.0.1` is an address of our `B2` obviously.

Our job just started to be easier, and it really surprised me that many people forgot about this little, useful feature.