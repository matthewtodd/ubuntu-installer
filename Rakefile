namespace :packages do
  task :update do
    sh 'reprepro --confdir config/packages --outdir packages update'
  end
end



directory 'image'

file '/tmp/ubuntu-cdrom' do
  'hdiutil attach vendor/ubuntu-8.10-server-i386.iso'
end

task :clean do
  rm_rf 'image'
end

task :isolinux => ['image', '/tmp/ubuntu-cdrom'] do
  mkdir 'image/isolinux'
  cp '/tmp/ubuntu-cdrom/isolinux/boot.cat', 'image/isolinux'
  cp '/tmp/ubuntu-cdrom/isolinux/isolinux.bin', 'image/isolinux'
  cp 'config/isolinux/isolinux.cfg', 'image/isolinux'  
end

task :installer => ['image', '/tmp/ubuntu-cdrom'] do
  mkdir 'image/install'
  cp '/tmp/ubuntu-cdrom/install/initrd.gz', 'image/install'
  cp '/tmp/ubuntu-cdrom/install/vmlinuz', 'image/install'
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