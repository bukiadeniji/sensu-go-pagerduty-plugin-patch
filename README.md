# sensu-go-pagerduty-plugin-patch

This is a quick tutorial on how to patch the Pagerduty plugin to be compatible with Sensu-GO (5.0).

The steps will guide you through installing the available Pagerduty plugin and applying this patch to make it compatible with Sensu Go

For details about the requirements of Pagerduty, setting up a service and other info about the plugin, please refer to https://www.pagerduty.com/docs/guides/agent-install-guide/

# Assumptions
1. You already have a Pagerduty account. Signup for one if you dont.
2. You have a working Sensu-Go installation
3. You know how to create sensu handlers

# Installation
The following steps were written and tested on Centos 7. However, it can be easily adapted to other OS.

Log on to your Sensu Server, and execute the following.

1.  Install Pagerduty Repo
	
  ```
  sh -c 'cat >/etc/yum.repos.d/pdagent.repo <<EOF
	[pdagent]
	name=PDAgent
	baseurl=https://packages.pagerduty.com/pdagent/rpm
	enabled=0
	gpgcheck=1
	gpgkey=https://packages.pagerduty.com/GPG-KEY-RPM-pagerduty
	EOF'
  ```
  
2.  Install the PD agent package

`yum install --enablerepo=pdagent pdagent pdagent-integrations`

3.  Download this patch to your Sensu-Go server host

```
cd /usr/share/pdagent-integrations/bin/
git clone https://github.com/bukiadeniji/sensu-go-pagerduty-plugin-patch
```

4.  Patch the pd-sensu plugin, by applying the pd-sensu.patch file

```
cp sensu-go-pagerduty-plugin-patch/pd-sensu.patch .
patch -b < pd-sensu.patch
```

5. Verify your configuration

`pd-send -k <your_integration_key> -t trigger -d "Test test" -i server.test`

6. Create Sensu Pagerduty handler and complete your setup
```
sensuctl handler create pagerduty --type pipe --command "/usr/share/pdagent-integrations/bin/pd-sensu -k <your_integration_key>" --timeout 20
```
