var async = require('async'),
    fstream = require('fstream'),
    path = require('path'),
    fs = require('fs'),
    Zip = require('node-zip'),
    out = require('out'),
    config = require('./config.json'),
    handlebars = require('handlebars'),
    devToolsPath = path.resolve(config.webworksPath, 'dependencies', 'tools', 'bin'),
    reIgnoreFiles = /^(node_modules|\.DS_Store|Jakefile|template|output|lib|package.json)/,
    projectFiles = [];
    
// default the projectname if not set
config.projectName = config.projectName || path.basename(__dirname);

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
        path.resolve(config.webworksPath, 'bbwp'),
        path.resolve(config.projectName + '.zip'),
        '-d',
        '-o',
        path.resolve('output')
    ];
    
    out('!{bold}building bar file');
    jake.exec(args.join(' '), function() {
        complete();
    }, { printStdout: true });
});

task('push', { async: true }, function() {
    var args = [
        path.resolve(devToolsPath, 'blackberry-deploy'),
        '-installApp',
        // '-launchApp',
        '-password',
        config.devicePass,
        '-device',
        config.deviceIP,
        path.resolve(__dirname, 'output', 'device', config.projectName + '.bar')
    ];
    
    out('!{bold}pushing to the device');
    jake.exec(args.join(' '), function() {
        complete();
    }, { printStdout: true });
});

task('default', ['build']);