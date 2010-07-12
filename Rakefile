SANDBOX_DIR = File.expand_path('rbenv')
GEMS_DIR = File.join(SANDBOX_DIR, 'rubygems', 'gems')
VAR_DIR = File.join(SANDBOX_DIR, 'var')
RESQUE_DIR = Dir.glob(File.join(GEMS_DIR, 'resque-*')).last

if RESQUE_DIR
  load File.join(RESQUE_DIR, 'tasks', 'redis.rake')
end

def command?(command)
  system("type #{File.join(SANDBOX_DIR, 'bin', command)} > /dev/null 2>&1") or   system("type #{command} > /dev/null 2>&1")
end

namespace :sandbox do

  desc "Install redis using reque's Rakefile"
  task :setup_redis do
    if not command? "redis-server"
      Dir.chdir(RESQUE_DIR) do
        ENV['PREFIX'] = SANDBOX_DIR
        sh %{rake redis:install}
      end
    end
  end

  directory VAR_DIR
  
  desc "Download sources"
  task :setup_sources => [VAR_DIR] do
    Dir.chdir(VAR_DIR) do
      sh %{git clone git://github.com/defunkt/resque.git}
    end
  end

  desc "Setup a sandbox with all required gems"
  task :setup do
    if not command? "sandbox"
      fail "Cannot execute 'sandbox' command. Please install it with sudo gem install nkryptic-sandbox -s http://gems.github.com"
    end
    sh %{sandbox rbenv -g sinatra,rack,resque,rake}
    Rake::Task['sandbox:setup_redis'].invoke
    Rake::Task['sandbox:setup_sources'].invoke
  end

  desc "Activate sandbox"
  task :enter do
    sh %{bash -c "source rbenv/bin/activate_sandbox"}
  end

  desc "Deactivate sandbox"
  task :leave do
    sh %{deactivate_sandbox}
  end
end
