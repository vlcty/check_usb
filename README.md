# USB overwatch for Icinga 2

## What is it

This is a plugin for Icinga 2 to overwatch the USB bus for new USB devices

## What it does

It's a dirty implementation to monitor USB activity on Linux servers. It fetches the last 10 lines of dmesg output and checks for newly discovered USB devices.

## Differences from check_hw

This plugin is not intended to monitor specific USB devices or ports but the whole bus at once. I run a Tor exit relay on a 1U rack server and don't want any nasty stuff attached to the server without proper notification in advance, for example: USB Sticks, KVM devices and so on.

I combined my setup with an EventCommand which turns off the Tor relay if the service goes into CRITICAL state.

## How to install

### Step 1: Fetch the script

Fetch the script and place it in your PluginDir-Folder on every server you want to monitor. That's usually `/usr/lib/nagios/plugins`.

Note: This is an agent based check!

### Step 2: Make it available for Icinga 2

#### Step 2.1: Create a CheckCommand object

Navigate on your Icinga 2 server to your config folder, e.g.: `/etc/icinga2/conf.d` and open the `commands.conf` file.
Place this piece of code at the end of the file:

```
object CheckCommand "usb-overwatch" {
        import "plugin-check-command"

        command = [ PluginDir + "/check_usb" ]
}
```

#### Step 2.2: Add check to a host

To add the check to a host use this snippet in a host configuration file:

```
object Host "mineralwasser" {
	[...]
}

object Service "usb-activity" {
      import "generic-service"

      host_name = "minerwalwasser"
      display_name = "USB Overwatch"

      check_command = "usb-overwatch"
}
```

If you want to add it to multiple hosts work with `apply`!
