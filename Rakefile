require 'fileutils'
require 'stasis'

sites = %w[howemailworks.com howpasswordswork.com].map { |site|
  topic = site[/how(\w+)work/, 1].to_sym
  path = File.expand_path("./#{site}", Dir.pwd)
  [site, topic, path]
}

sites.each do |tuple|
  site, topic, path = tuple
  namespace :build do
    desc "Build #{site}"
    task topic do
      puts "Building #{site}..."
      Stasis.new(path).render
    end

    desc "Build all sites"
    task :all => sites.map { |tuple| "build:#{tuple[1]}" }
  end

  namespace :clean do
    desc "Removes product for #{site}"
    task topic do
      puts "removing #{site}/public"
      system "rm -rf #{site}/public/"
    end

    desc "Removes all product files"
    task :all => sites.map { |tuple| "clean:#{tuple[1]}" }
  end

  namespace :link_shared do
    desc "Links files from shared into #{site}"
    task topic do
      shared = FileList.new('shared/*')
      shared.each do |path|
        src = "../#{path}"
        dest = "#{site}/#{File.basename(path)}"

        FileUtils.symlink(src, dest) unless File.exist?(dest)
      end
    end
  end

  namespace :deploy do
    desc "Deploy #{site} to host"
    task topic => "build:#{topic}" do
      puts "Deploying #{site}..."
      options = %w[archive compress verbose delete]
      options = options.map { |opt| "--#{opt}" }.join(' ')
      system "rsync #{options} #{path}/public/ titans:#{site}"
    end

    desc "Deploys all sites"
    task :all => sites.map { |tuple| "deploy:#{tuple[1]}" }
  end
end

desc "Build all sites"
task :default => sites.map { |tuple| "build:#{tuple[1]}" }
