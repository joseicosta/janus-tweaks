task :install do
  Dir.mkdir('tmp') unless File.exists?('tmp')
  sh 'rm -rf tmp/*'
  sh 'git clone https://github.com/vim-scripts/bufexplorer.zip.git tmp/bufexplorer'
  sh 'cp tmp/bufexplorer/plugin/bufexplorer.vim ~/.vim/plugin/'
  sh 'curl http://www.vim.org/scripts/download_script.php?src_id=8379 > ~/.vim/colors/railscasts256.vim'
  sh 'cp vimrc.local ~/.vimrc.local'
  sh 'cp gvimrc.local ~/.gvimrc.local'
  sh 'rm -rf tmp/*'
end

task :default => :install
