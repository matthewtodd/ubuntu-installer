namespace :packages do
  task :update do
    sh 'reprepro --confdir config/packages --basedir work --outdir packages update'
  end
end



directory 'image'

task :clean do
  rm_rf 'image', 'ubuntu-8.10-server-i386-custom.iso'
end

task :isolinux => 'image' do
  mkdir 'image/isolinux'
  cp '/cdrom/isolinux/boot.cat',     'image/isolinux'
  cp '/cdrom/isolinux/isolinux.bin', 'image/isolinux'
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
end

task :iso => [:isolinux, :installer, :preseed, :packages] do
  sh <<-END
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
