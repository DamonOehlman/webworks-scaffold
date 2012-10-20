# Webworks Scaffold

This is a simple scaffold project designed to get you up and running writing a webworks application quickly.  It's a massive work in progress but should assist you getting your code onto the device.

__NOTE:__ This tool has only been proven to work on Mac, and whilst in theory windows support should work. The zip file format cannot be read successfully in windows :/

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

If everything is installed (and configured) correctly, you should be able to run the following command in the project directory:

```
jake build push
```

__NOTE:__ The `push` command is not required if you don't want to push to the device.

All being well, you should see output similar to what is shown below:

```
damo-mbair:webworks-scaffold damo$ jake build push
analyzing project structure
writing config
creating archive
building bar file
[INFO]    Populating application source
[INFO]    Parsing config.xml
[WARN]    Build ID set in config.xml [version], but no signing password was provided [-g]. Bar will be unsigned
[INFO]    Generating output files
[INFO]    Info: Package created: /Volumes/development/projects/DamonOehlman/webworks-scaffold/output/simulator/webworks-scaffold.bar
[INFO]    Info: Package created: /Volumes/development/projects/DamonOehlman/webworks-scaffold/output/device/webworks-scaffold.bar
[INFO]    BAR packaging complete
pushing to the device
Info: Sending request: Install
Info: Action: Install
Info: File size: 319022
Info: Installing ...
Info: Processing 319022 bytes
Info: Progress 90%...
Info: Progress 100%...
actual_dname::sampleapp.testDev_sampleapp__42572c37
actual_id::testDev_sampleapp__42572c37
actual_version::2.0.0.0
result::success
```