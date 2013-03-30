require 'rake'
require 'rake/clean'

# Immediately sync all stdout so that tools like buildbot can
# immediately load in the output.
$stdout.sync = true
$stderr.sync = true

# Change to the directory of this file.
BASE_DIR = File.expand_path("../", __FILE__)
Dir.chdir(BASE_DIR)

desc "Serve site at http://localhost:4567"
task :dev_server do 
  system 'mysqladmin -uroot -psecret_password create wordpress'
	system 'cd dist/ && bin/start.sh 4567 Verbose'
end

CLEAN.include("#{BASE_DIR}/dist/**")
SRC=FileList.new('src/**/*')

task :copy do
	SRC.each do |file|
		next if FileTest.directory?(file)

	  dir, filename = File.dirname(file), File.basename(file)
	  dest = File.join("#{BASE_DIR}/dist", dir.sub(/^src\//,''))
	  FileUtils.mkdir_p(dest)
	  FileUtils.copy_file(file, File.join(dest,filename))
	end
end

desc "Download buildpack to #{BASE_DIR}/buildpack"
task :refresh_buildpack do
   if Dir.exists?("#{BASE_DIR}/buildpack/.git") then
    puts 'Updating buildpack'
    system "git --git-dir #{BASE_DIR}/buildpack/.git pull"
   else
      puts 'Cloning buildpack'
      system "git clone git://github.com/mrdavidlaing/stackato-buildpack-wordpress.git #{BASE_DIR}/buildpack"
   end
end

task :build => [:refresh_buildpack, :copy] do 
	system "#{BASE_DIR}/buildpack/bin/compile #{BASE_DIR}/dist"
end 

task :watch do
	puts "TODO: implement watching via guard"
end

task :rebuild  => [:clean, :build] 
task :run => [:build, :dev_server, :watch]

task :release do 
	puts "deploying to stackato"
end

# Default task is to run the unit tests
task :default => "build"