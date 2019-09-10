# Running Passenger with systemd

This repo provides systemd unit files for running multiple Rails apps with Phusion Passenger. For detailed instructions check out my blog post:

http://matthias-grosser.de/blog/2015/01/deploying-multiple-rails-apps-with-passenger-standalone-and-systemd/

## Install

Place these two files in the following locations:
```
/etc/systemd/system/passenger@.service
/usr/local/bin/passenger-app
```

And add some permissions:

```
# sudo chmod +x /usr/local/bin/passenger-app
```

## Per-app configuration

Passenger runtime directory:
```
/srv/apps/<APP>/passenger
```

Passenger configuration:
```
/srv/apps/<APP>/current/Passengerfile.json
```

## Controlling your applications

As root, with systemd:
```
# systemctl start passenger@APPNAME
# systemctl stop passenger@APPNAME
# systemctl reload passenger@APPNAME
# systemctl restart passenger@APPNAME
```

As user:
```
$ passenger-app --help
$ passenger-app <app> start
$ passenger-app <app> reload
$ passenger-app <app> stop
$ passenger-app <app> restart
$ passenger-app <app> status [<args>]
$ passenger-app <app> config <args>
```
## Running each app under its own account

To separate multiple applications from each others files, use:
```
# systemctl edit passenger@APPNAME
```
and make sure the override.conf reads:
```
[Service]
User=myapp
Group=myapp
```

You can also pass environment variables to your app using this
approach:
```
[Service]
Environment="SECRET=r1na5GWpNf052syOhHlUSM1bgmFL0I"
```

(generate your own secret using a tool like `tr -cd '[:alnum:]' < /dev/urandom | fold -w30 | head -n1`)
