namespace :packages do
  task :update do
    sh 'reprepro --confdir config/packages --outdir packages update'
  end
end



directory 'image'

task :clean do
  rm_rf 'image'
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
  # use mkisofs
end