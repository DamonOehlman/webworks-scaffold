var async = require('async'),
    fstream = require('fstream'),
    path = require('path'),
    fs = require('fs'),
    Zip = require('node-zip'),
    out = require('out'),
    config = require('./config.json'),
    spawn = require('child_process').spawn,
    handlebars = require('handlebars'),
    commandExt = process.platform == 'win32' ? '.bat' : '',
    devToolsPath = path.resolve(config.webworksPath, 'dependencies', 'tools', 'bin'),
    reIgnoreFiles = /^(\.git|node_modules|\.DS_Store|Jakefile|template|output|lib|package.json)/,
    procOpts = {
        detached: true,
        stdio: 'inherit'
    },
    projectFiles = [];
    
// default the projectname if not set
config.projectName = config.projectName || path.basename(__dirname);

// add the projectname to the app
config.app.projectName = config.app.projectName || config.projectName;

// discover the nature of the project through 
task('discovery', { async: true }, function() {
    var reader = fstream.Reader({
            path: __dirname,
            filter: function(entry) {
                var testPath = entry.path.slice(__dirname.length + 1);
                
                return ! reIgnoreFiles.test(testPath);
            }
        });
        
    reader.on('child', function(entry) {
        if (entry.type === 'File') {
            projectFiles.push(entry.path);
        }
    });
    
    reader.on('end', complete);
    out('!{bold}analyzing project structure');
});

task('write-config', ['discovery'], { async: true }, function() {
    var template;
    
    // load the template file
    out('!{bold}writing config');
    fs.readFile(path.resolve(__dirname, 'templates', 'config.xml'), 'utf8', function(err, data) {
        if (err) return fail(err);
        
        // compile the template
        template = handlebars.compile(data);
        
        // generate the output
        fs.writeFile(path.resolve(__dirname, 'config.xml'), template(config.app), 'utf8', complete);
    });
});

task('package', ['write-config', 'discovery'], { async: true }, function() {
    var archive = new Zip();
    
    out('!{bold}creating archive');
    async.map(projectFiles, fs.readFile, function(err, buffers) {
        if (err) return fail(err);
        
        // create the zip with entries
        buffers.forEach(function(buffer, index) {
            var entry = projectFiles[index].slice(__dirname.length + 1);
            
            // add the buffer entry
            archive.file(entry, buffer.toString());
        });
        
        // create the archive data
        fs.writeFile(
            path.resolve(__dirname, config.projectName + '.zip'),
            archive.generate({ base64: false, compression: 'DEFLATE' }),
            'binary',
            complete
        );
    });
});

task('build', ['package'], { async: true }, function() {
    var args = [
            path.resolve(config.projectName + '.zip'),
            '-d',
            '-o',
            path.resolve('output')
        ],
        proc = spawn(path.resolve(config.webworksPath, 'bbwp' + commandExt), args, procOpts);
        
    out('!{bold}building bar file');
    proc.on('exit', complete);
});

task('push', { async: true }, function() {
    var args = [
            '-installApp',
            // '-launchApp',
            '-password',
            config.devicePass,
            '-device',
            config.deviceIP,
            path.resolve(__dirname, 'output', 'device', config.projectName + '.bar')
        ],
        proc = spawn(path.resolve(devToolsPath, 'blackberry-deploy' + commandExt), args, procOpts);

    
    out('!{bold}pushing to the device');
    proc.on('exit', complete);
});

task('default', ['build']);