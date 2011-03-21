require 'readline'

def title(text)
  "\e[33m#{'*' * 60}\n*#{text.center(58)}*\n#{'*' *  60}\e[0m"
end

def error(text)
  "\e[31m#{text}\e[0m"
end

def info(text)
  "\e[32m#{text}\e[0m"
end

def warn(text)
  "\e[33m#{text}\e[0m"
end

def diff(f1, f2)
  sh "diff -s #{f1} #{f2}" do |ok, res|
    yield(ok, res) if block_given?
  end
end

def diff_config
  changes_found = nil
  %w(vimrc.local gvimrc.local).each do |f|
    local_path = File.expand_path(".#{f}", '~')
    if File.exists?(local_path)
      diff(f, local_path) do |ok, res|
        puts (ok ? info("No differences found in #{f}\n") : ((changes_found ||= true) && error("Differences found in #{f}\n")))
      end
    end
  end
  changes_found
end

module VIM
  Dirs = %w[ after autoload doc plugin ruby snippets syntax ftdetect ftplugin colors indent ]
end

directory "tmp"

VIM::Dirs.each do |dir|
  directory("#{dir}")
end

def vim_plugin_task(name, repo=nil)
  cwd = File.expand_path("~/.vim", __FILE__)
  dir = File.expand_path("tmp/#{name}")
  subdirs = VIM::Dirs
  namespace(name) do
    if repo
      file dir => "tmp" do
        if repo =~ /git$/
          sh "git clone #{repo} #{dir}"

        elsif repo =~ /download_script/
          if filename = `curl --silent --head #{repo} | grep attachment`[/filename=(.+)/,1]
            filename.strip!
            sh "curl #{repo} > tmp/#{filename}"
          else
            raise ArgumentError, 'unable to determine script type'
          end

        elsif repo =~ /(tar|gz|vba|zip)$/
          filename = File.basename(repo)
          sh "curl #{repo} > tmp/#{filename}"

        else
          raise ArgumentError, 'unrecognized source url for plugin'
        end

        case filename
        when /zip$/
          sh "unzip -o tmp/#{filename} -d #{dir}"

        when /tar\.gz$/
          dirname  = File.basename(filename, '.tar.gz')

          sh "tar zxvf tmp/#{filename}"
          sh "mv #{dirname} #{dir}"

        when /vba(\.gz)?$/
          if filename =~ /gz$/
            sh "gunzip -f tmp/#{filename}"
            filename = File.basename(filename, '.gz')
          end

          # TODO: move this into the install task
          mkdir_p dir
          lines = File.readlines("tmp/#{filename}")
          current = lines.shift until current =~ /finish$/ # find finish line

          while current = lines.shift
            # first line is the filename (possibly followed by garbage)
            # some vimballs use win32 style path separators
            path = current[/^(.+?)(\t\[{3}\d)?$/, 1].gsub '\\', '/'

            # then the size of the payload in lines
            current = lines.shift
            num_lines = current[/^(\d+)$/, 1].to_i

            # the data itself
            data = lines.slice!(0, num_lines).join

            # install the data
            Dir.chdir dir do
              mkdir_p File.dirname(path)
              File.open(path, 'w'){ |f| f.write(data) }
            end
          end
        end
      end

      task :pull => dir do
        if repo =~ /git$/
          Dir.chdir dir do
            sh "git pull"
          end
        end
      end

      task :install => [:pull] + subdirs do
        Dir.chdir dir do
          if File.exists?("Rakefile") and `rake -T` =~ /^rake install/
            sh "rake install"
          elsif File.exists?("install.sh")
            sh "sh install.sh"
          else
            subdirs.each do |subdir|
              if File.exists?(subdir)
                sh "cp -rf #{subdir}/* #{cwd}/#{subdir}/"
              end
            end
          end
        end

        yield if block_given?
      end
    else
      task :install => subdirs do
        yield if block_given?
      end
    end
  end

  desc "Install #{name} plugin"
  task name do
    puts
    puts "*" * 40
    puts "*#{"Installing #{name}".center(38)}*"
    puts "*" * 40
    puts
    Rake::Task["#{name}:install"].invoke
  end
  task :default => name
end

namespace :config do

  desc 'Diff local files'
  task :diff do
    diff_config
  end

  desc 'Copy local files'
  task :copy do
    line = ''
    until line =~ /^(y|n)$/ do
      line = Readline.readline(warn('Differences where found with your local config files. Do you want to override? [y/n]'))
    end if changes_found = diff_config

    if !changes_found || line == 'y'
      sh 'cp vimrc.local ~/.vimrc.local'
      sh 'cp gvimrc.local ~/.gvimrc.local'
    end
  end
end

desc 'Clean temporary files'
task :clean do
  sh 'rm -rf tmp/*' if File.exists?('tmp')
end

desc 'Install'
task :install do
  if File.exists?('tmp')
    Rake::Task[:clean].invoke
  else
    Dir.mkdir('tmp')
  end

  Rake::Task['config:copy'].invoke
  Rake::Task[:clean].invoke
end

vim_plugin_task 'bufexplorer', 'git://github.com/vim-scripts/bufexplorer.zip.git'
vim_plugin_task 'matchit', 'git://github.com/vim-scripts/matchit.zip.git'
vim_plugin_task 'ruby', 'git://github.com/vim-ruby/vim-ruby.git'

#vim_plugin_task 'githublink' do
#  sh 'curl https://github.com/tenderlove/dot_vim/raw/master/plugin/githublink.vim > ~/.vim/plugin/githublink.vim'
#end

vim_plugin_task 'railscasts256' do
  sh 'curl http://www.vim.org/scripts/download_script.php?src_id=8379 > ~/.vim/colors/railscasts256.vim'
end

task :default => :install
