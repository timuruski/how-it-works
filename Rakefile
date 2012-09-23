require 'stasis'

namespace :email do
  task :build do
    build('howemailworks')
  end
end

namespace :passwords do
  task :build do
    build('howpasswordswork')
  end
end

def build(name)
  puts "Building #{name}.com..."
  # Fork because stasis sets some sort of globals that makes this only
  # work once per session.
  pid = Process.fork do
    path = File.expand_path("./#{name}.com", Dir.pwd)
    options = { }
    Stasis.new(path, options).render
  end
  Process.detach(pid)
end

task :default => ['email:build', 'passwords:build']

