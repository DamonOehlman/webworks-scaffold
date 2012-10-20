var async = require('async'),
    fstream = require('fstream'),
    path = require('path'),
    fs = require('fs'),
    Zip = require('node-zip'),
    config = require('./config.json'),
    devToolsPath = path.resolve(config.webworksPath, 'dependencies', 'tools', 'bin'),
    reIgnoreFiles = /^(node_modules|\.DS_Store|Jakefile|template|output|package.json)/;
    
// default the projectname if not set
config.projectName = config.projectName || path.basename(__dirname);
    
task('package', { async: true }, function() {
    var files = [],
        reader = fstream.Reader({
            path: __dirname,
            filter: function(entry) {
                var testPath = entry.path.slice(__dirname.length + 1);
                
                return ! reIgnoreFiles.test(testPath);
            }
        });
        
    function buildPackage() {
        var archive = new Zip();
        
        async.map(files, fs.readFile, function(err, buffers) {
            if (err) return fail(err);
            
            // create the zip with entries
            buffers.forEach(function(buffer, index) {
                var entry = files[index].slice(__dirname.length + 1);
                
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
        complete();
    }
        
    reader.on('child', function(entry) {
        if (entry.type === 'File') {
            files.push(entry.path);
        }
    });
    
    reader.on('end', buildPackage);
});

task('build', ['package'], { async: true }, function() {
    var args = [
        path.resolve(config.webworksPath, 'bbwp'),
        path.resolve(config.projectName + '.zip'),
        '-d',
        '-o',
        path.resolve('output')
    ];
    
    jake.exec(args.join(' '), function() {
        console.log('did stuff');
        complete();
    }, { printStdout: true });
});

task('device', ['build'], { async: true }, function() {
    var args = [
        path.resolve(devToolsPath, 'blackberry-deploy'),
        '-installApp',
        '-launchApp',
        '-password',
        config.devicePass,
        '-device',
        config.deviceIP,
        path.resolve(__dirname, 'output', 'device', config.projectName + '.bar')
    ];
    
    jake.exec(args.join(' '), function() {
        console.log('did more stuff');
        complete();
    }, { printStdout: true });
});