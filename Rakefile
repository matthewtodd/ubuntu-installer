SELECTIONS = FileList.new('config/packages/selections.d/*')

file 'config/packages/selections' => SELECTIONS do |task|
  selections = SELECTIONS.map { |file| File.readlines(file).map { |line| line.chomp } }.flatten.sort.uniq

  File.open(task.name, 'w') do |result|
    result.puts selections.map { |line| "#{line}\tinstall" }
  end
end

namespace :packages do
  task :update => 'config/packages/selections' do
    sh 'reprepro -V --confdir config/packages --basedir work --noskipold update'
    sh 'reprepro -V --confdir config/packages --basedir work --noskipold export'
    sh 'reprepro -V --confdir config/packages --basedir work --noskipold createsymlinks'
  end

  task :push do
    host = ENV['HOST'] || raise('Please specify a HOST.')
    sh "rsync --rsh=ssh --recursive --progress work/dists #{host}:/opt/local/var/lib/apt/packages"
    sh "rsync --rsh=ssh --recursive --progress --ignore-existing --prune-empty-dirs work/pool #{host}:/opt/local/var/lib/apt/packages"
  end
end



directory 'image'

task :clean do
  rm_rf 'image'
  rm_rf 'ubuntu-8.10-server-i386-custom.iso'
  # don't remove work: it's really expensive to recreate with crappy bandwidth.
end

task :isolinux => 'image' do
  mkdir 'image/isolinux'

  cp '/cdrom/isolinux/boot.cat', 'image/isolinux'
  sh 'chmod u+w image/isolinux/boot.cat'

  cp '/cdrom/isolinux/isolinux.bin', 'image/isolinux'
  sh 'chmod u+w image/isolinux/isolinux.bin'

  cp 'config/isolinux.cfg', 'image/isolinux'
end

task :installer => 'image' do
  mkdir 'image/install'
  cp '/cdrom/install/initrd.gz', 'image/install'
  cp '/cdrom/install/vmlinuz', 'image/install'
end

task :preseed => 'image' do
  mkdir 'image/preseed'
  cp 'config/preseed.seed', 'image/preseed'
end

task :packages => 'image' do
  cp_r 'work/dists', 'image'
  cp_r 'work/pool', 'image'
  sh 'find image/pool -type d -empty -delete'
end

task :ubuntu => 'image' do
  Dir.chdir('image') { sh 'ln -s . ubuntu' }
end

def write(path, *lines)
  File.open(path, 'w') { |f| lines.each { |l| f.puts(l) } }
end

task :disk => 'image' do
  mkdir 'image/.disk'

  Dir.chdir('image/.disk') do
    write 'base_components', 'main'
    write 'base_installable'
    write 'cd_type',         'full_cd/single'
    write 'info',            'Ubuntu 8.10 Server (Custom)'
    write 'udeb_include',    'netcfg', 'ethdetect', 'pcmcia-cs-udeb', 'wireless-tools-udeb'
  end
end

task :iso => [:isolinux, :installer, :preseed, :packages, :ubuntu, :disk] do
  sh <<-END.gsub(/\s+/, ' ').strip
    mkisofs \
      -boot-info-table \
      -boot-load-size 4 \
      -cache-inodes \
      -eltorito-boot isolinux/isolinux.bin \
      -eltorito-catalog isolinux/boot.cat \
      -full-iso9660-filenames \
      -joliet \
      -no-emul-boot \
      -output ubuntu-8.10-server-i386-custom.iso \
      -rational-rock \
      -volid "Ubuntu 8.10 Server (Custom)" \
      image
  END
end
