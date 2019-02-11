# Rakefile

desc "Build files"
task :build do
  sh "make"
end

desc "Cleanup compiled files"
task :clean do
  file_types = [".o", ".so"]

  Dir.glob("*").each do |file|
    rm file if file_types.include?(File.extname(file))
  end
end

desc "Cleanup then build files"
task clean_and_build: [:clean, :build] do; end

desc "Update @rpath value" # check with otool -L oracle_fdw.so
task :update_rpath do
  ora_dir = File.expand_path("~/Library/Oracle/instantclient_12_2")

  if File.exist?("oracle_fdw.so")
    lib = "libclntsh.dylib.12.1"
    sh "install_name_tool -change @rpath/#{lib} #{ora_dir}/#{lib} oracle_fdw.so"
  else
    puts "Missing oracle_fdw.so"
  end
end

desc "Install extension (VERSIONS='')"
task :install do
  if ENV["VERSIONS"]
    versions = ENV["VERSIONS"].split(",")
  else
    versions = ["latest"]
  end
  pg_root = File.expand_path("/Applications/Postgres.app/Contents/Versions")

  versions.each do |v|
    pg_dir = File.join(pg_root, v)
    ext_dir = File.join(pg_dir, "share", "postgresql", "extension")
    lib_dir = File.join(pg_dir, "lib", "postgresql")
    
    if Dir.exist?(pg_dir)
      sh "cp -v oracle_fdw.control #{ext_dir}"
      sh "cp -v oracle_fdw--1.1.sql #{ext_dir}"
      sh "cp -v oracle_fdw.so #{lib_dir}"
    else
      puts "Missing dir: #{pg_dir}"
    end
  end
end

desc "All the things"
task default: [:clean, :build, :update_rpath, :install] do; end
