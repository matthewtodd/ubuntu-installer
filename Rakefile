namespace :packages do
  task :update do
    sh 'reprepro -V --confdir config/packages --basedir packages --noskipold update'
    sh 'reprepro -V --confdir config/packages --basedir packages --noskipold export'
    sh 'reprepro -V --confdir config/packages --basedir packages --noskipold createsymlinks'
  end
end



directory 'image'

task :clean do
  rm_rf 'image'
  rm_rf 'ubuntu-8.10-server-i386-custom.iso'
end

task :isolinux => 'image' do
  mkdir 'image/isolinux'
  cp '/cdrom/isolinux/boot.cat',     'image/isolinux'
  sh 'chmod u+w image/isolinux/boot.cat'
  cp '/cdrom/isolinux/isolinux.bin', 'image/isolinux'
  sh 'chmod u+w image/isolinux/isolinux.bin'
  cp 'config/isolinux/isolinux.cfg', 'image/isolinux'  
end

task :installer => 'image' do
  mkdir 'image/install'
  cp '/cdrom/install/initrd.gz', 'image/install'
  cp '/cdrom/install/vmlinuz', 'image/install'
end

task :preseed => 'image' do
  mkdir 'image/preseed'
  cp 'config/preseed/ubuntu-server.seed', 'image/preseed'
end

task :packages => 'image' do
  cp_r 'packages/dists', 'image'
  cp_r 'packages/pool', 'image'
  sh 'find image/pool -type d -empty -delete'
end

task :ubuntu => 'image' do
  Dir.chdir('image') { sh 'ln -s . ubuntu' }
end

task :disk => 'image' do
  cp_r '/cdrom/.disk', 'image'
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
