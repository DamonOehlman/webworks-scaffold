# Webworks Scaffold

This is a simple scaffold project designed to get you up and running writing a webworks application quickly.  It's a massive work in progress but should assist you getting your code onto the device.

## Getting Started

First-grab a copy of this repository, either [download](https://github.com/DamonOehlman/webworks-scaffold/tarball/master) or clone: 

```
git clone git://github.com/DamonOehlman/webworks-scaffold.git
```

Once you've got the scaffold, I'd recommend using the `Jakefile` provided to make your life easier.  To do this, you will need to install [jake](/mde/jake) globally, and then the build script dependencies locally:

```
sudo npm install -g jake
npm install
```

Then update the `config.json` to match your configuration.  Default values are shown below:

```json
{
    "webworksPath": "/development/tools/bb-devtools/webworks",
    "devicePass": "password",
    "deviceIP": "192.168.0.1"
}
```

Once you've done this you should be able to `package`, `build` and `push` your apps to your device.  Though remember all the usual process regarding [installing debug tokens](https://developer.blackberry.com/html5/documentation/runnning_unsigned_apps_using_a_debug_token_1866987_11.html) applies. 